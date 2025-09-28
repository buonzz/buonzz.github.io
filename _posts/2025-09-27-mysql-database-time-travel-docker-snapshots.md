---
layout: post
title: "MySQL Database Time Travel with Docker Snapshots"
date: 2025-09-27
slug: mysql-database-time-travel-docker-snapshots
categories: [mysql, docker, backup, devops]
tags: [mysql, docker, snapshots, backup, registry, blackblaze]
subtitle: "Do you wish you could have database time travel capabilities, making it easy to restore not just data, but the complete database environment from any point in time?"
---

# MySQL Database Time Travel with Docker Snapshots

Traditional MySQL backups using `mysqldump` only capture data and schema. But what if you need to preserve the complete database state including users, permissions, configurations, and system tables? This tutorial shows how to create complete MySQL database snapshots using Docker images, enabling true "time travel" to any previous database state.

## Why Docker Snapshots Over SQL Dumps?

**SQL Dumps capture:**
- Table schemas and data
- Stored procedures and functions
- Triggers

**Docker Snapshots capture:**
- Everything in SQL dumps
- User accounts and permissions
- MySQL configuration
- System databases (mysql, information_schema, performance_schema)
- Complete server state

## Prerequisites

- Docker Engine 20.10+
- MySQL 8.0+ database
- Docker registry (local or cloud)
- Blackblaze B2 account (optional, for cold storage)

## Step 1: Create the Snapshot Infrastructure

### Directory Structure

```bash
mkdir mysql-snapshots
cd mysql-snapshots
mkdir docker-entrypoint-initdb.d
```

### Dockerfile

Create a multi-stage Dockerfile that captures the complete MySQL state:

```dockerfile
FROM mysql:8.0.39 AS builder

# Disable normal startup to capture state
RUN ["sed", "-i", "s/exec \"$@\"/echo \"not running $@\"/", "/usr/local/bin/docker-entrypoint.sh"]

# Set environment variables for your database
ENV MYSQL_ROOT_PASSWORD=your_root_password
ENV MYSQL_DATABASE=your_database_name
ENV MYSQL_USER=your_app_user
ENV MYSQL_PASSWORD=your_app_password

# Prepare directories
RUN chown -R mysql:root /var/lib/mysql/
RUN mkdir /initialized-db
RUN chown -R mysql:root /initialized-db

# Copy your database dump
COPY ./docker-entrypoint-initdb.d/*.sql /docker-entrypoint-initdb.d/

# Initialize database with complete state
RUN ["/usr/local/bin/docker-entrypoint.sh", "mysqld", "--datadir", "/initialized-db"]

# Final stage with initialized data
FROM mysql:8.0.39
COPY --from=builder /initialized-db /var/lib/mysql

CMD ["mysqld"]
EXPOSE 3306
```

### .gitignore

```
.DS_Store
.env
*.sql
```

## Step 2: Create Database Dump Script

Create `create-dump.sh`:

```bash
#!/bin/bash
set -e

# Configuration
DB_HOST="localhost"
DB_PORT="3306"
DB_USER="root"
DB_PASSWORD="your_password"
DB_NAME="your_database"
DUMP_FILE="./docker-entrypoint-initdb.d/${DB_NAME}.sql"

echo "Creating complete database dump..."

mysqldump \
  --host=$DB_HOST \
  --port=$DB_PORT \
  --user=$DB_USER \
  --password=$DB_PASSWORD \
  --databases $DB_NAME \
  --add-drop-database \
  --add-drop-table \
  --triggers \
  --routines \
  --events \
  --column-statistics=0 \
  --single-transaction \
  --verbose \
  --result-file=$DUMP_FILE

echo "Database dump created: $DUMP_FILE"
```

Make it executable:
```bash
chmod +x create-dump.sh
```

## Step 3: Automated Snapshot Creation

Create `create-snapshot.sh`:

```bash
#!/bin/bash
set -e

# Generate version tag (YYYY.MM.DD format)
VERSION=$(date +"%Y.%m.%d")
IMAGE_NAME="localhost:5000/mysql-snapshots"
FULL_TAG="$IMAGE_NAME:$VERSION"

echo "Creating MySQL snapshot - Version: $VERSION"

# 1. Create fresh database dump
./create-dump.sh

# 2. Build Docker image with current state
echo "Building Docker image..."
docker build -t $FULL_TAG .

# 3. Push to registry
echo "Pushing to registry..."
docker push $FULL_TAG

# 4. Tag as latest
docker tag $FULL_TAG $IMAGE_NAME:latest
docker push $IMAGE_NAME:latest

echo "Snapshot created successfully: $FULL_TAG"
```

Make it executable:
```bash
chmod +x create-snapshot.sh
```

## Step 4: Set Up Local Docker Registry

```bash
# Start local registry
docker run -d -p 5000:5000 --name registry registry:2

# Verify registry is running
curl http://localhost:5000/v2/_catalog
```

## Step 5: Automate with Cron

Create daily snapshots at 2 AM:

```bash
# Edit crontab
crontab -e

# Add this line:
0 2 * * * /path/to/mysql-snapshots/create-snapshot.sh >> /path/to/logs/snapshot.log 2>&1
```

## Step 6: Using Snapshots (Time Travel)

### List Available Snapshots

```bash
# List all snapshot versions
curl http://localhost:5000/v2/mysql-snapshots/tags/list
```

### Restore to Specific Date

```bash
# Pull specific snapshot
docker pull localhost:5000/mysql-snapshots:2024.12.15

# Run snapshot as new database
docker run -d \
  --name mysql-snapshot-2024-12-15 \
  -p 3307:3306 \
  localhost:5000/mysql-snapshots:2024.12.15

# Connect to restored database
mysql -h 127.0.0.1 -P 3307 -u root -p
```

### Compare Database States

```bash
# Run two different snapshots
docker run -d --name mysql-old -p 3307:3306 localhost:5000/mysql-snapshots:2024.12.01
docker run -d --name mysql-new -p 3308:3306 localhost:5000/mysql-snapshots:2024.12.15

# Use tools like mysqldiff to compare
mysqldiff --server1=root:password@localhost:3307 --server2=root:password@localhost:3308 --difftype=sql
```

## Step 7: Cold Storage with Blackblaze B2

### Install B2 CLI

```bash
pip install b2
```

### Configure B2

```bash
b2 authorize-account your_key_id your_application_key
```

### Archive Old Snapshots

Create `archive-snapshots.sh`:

```bash
#!/bin/bash
set -e

BUCKET_NAME="mysql-snapshots-archive"
REGISTRY_URL="localhost:5000"
IMAGE_NAME="mysql-snapshots"

# Get snapshots older than 30 days
CUTOFF_DATE=$(date -d "30 days ago" +%Y.%m.%d)

# List all tags
TAGS=$(curl -s http://$REGISTRY_URL/v2/$IMAGE_NAME/tags/list | jq -r '.tags[]')

for TAG in $TAGS; do
    if [[ "$TAG" < "$CUTOFF_DATE" ]]; then
        echo "Archiving snapshot: $TAG"
        
        # Save image as tar
        docker save $REGISTRY_URL/$IMAGE_NAME:$TAG | gzip > snapshot-$TAG.tar.gz
        
        # Upload to B2
        b2 file upload $BUCKET_NAME snapshot-$TAG.tar.gz snapshots/snapshot-$TAG.tar.gz
        
        # Remove local files
        rm snapshot-$TAG.tar.gz
        docker rmi $REGISTRY_URL/$IMAGE_NAME:$TAG
        
        echo "Archived: $TAG"
    fi
done
```

### Restore from B2

```bash
#!/bin/bash
SNAPSHOT_DATE="2024.11.15"
BUCKET_NAME="mysql-snapshots-archive"

# Download from B2
b2 file download $BUCKET_NAME snapshots/snapshot-$SNAPSHOT_DATE.tar.gz snapshot-$SNAPSHOT_DATE.tar.gz

# Load into Docker
docker load < snapshot-$SNAPSHOT_DATE.tar.gz

# Run restored snapshot
docker run -d --name mysql-restored -p 3307:3306 localhost:5000/mysql-snapshots:$SNAPSHOT_DATE
```

## Benefits

1. **Complete State Preservation**: Unlike SQL dumps, captures everything including users, permissions, and configurations
2. **Instant Restoration**: No need to rebuild users, permissions, or configurations
3. **Version Control**: Easy to track and manage different database states
4. **Cost-Effective Storage**: Use local registry for recent snapshots, B2 for long-term archive
5. **Portable**: Snapshots can run anywhere Docker is available

## Best Practices

1. **Retention Policy**: Keep daily snapshots for 30 days, weekly for 6 months, monthly for 2 years
2. **Naming Convention**: Use YYYY.MM.DD format for easy sorting and identification
3. **Monitoring**: Set up alerts for failed snapshot creation
4. **Testing**: Regularly test snapshot restoration process
5. **Security**: Use private registries and encrypt sensitive snapshots

## Troubleshooting

### Large Database Sizes
```bash
# Use compression for large databases
docker save localhost:5000/mysql-snapshots:2024.12.19 | gzip > snapshot.tar.gz
```

### Registry Storage Issues
```bash
# Clean up old images from registry
docker exec registry registry garbage-collect /etc/docker/registry/config.yml
```

### B2 Upload Failures
```bash
# Resume interrupted uploads
b2 file upload --threads 4 bucket-name large-snapshot.tar.gz snapshots/large-snapshot.tar.gz
```

This approach provides true database time travel capabilities, making it easy to restore not just data, but the complete database environment from any point in time.

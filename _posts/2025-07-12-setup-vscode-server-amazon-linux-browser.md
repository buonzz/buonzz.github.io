---
layout: post
title: "How to Set Up VS Code Server on Amazon Linux 2023 (Browser-based Development)"
date: 2025-07-12
tags: [vscode, amazon-linux, code-server, apache, ssl, devops]
subtitle: If you're like me and want to **develop on a remote EC2 instance** using just your **browser**, this guide walks through setting up **VS Code Server** (`code-server`) on **Amazon Linux 2023**, securely served over **HTTPS** with **Apache and Let's Encrypt**. 
---

If you're like me and want to **develop on a remote EC2 instance** using just your **browser**, this guide walks through setting up **VS Code Server** (`code-server`) on **Amazon Linux 2023**, securely served over **HTTPS** with **Apache and Let's Encrypt**.

This setup is tested and working as of July 2025.

---

## 🔧 Prerequisites

- A running **EC2 instance** using **Amazon Linux 2023**
- Apache (`httpd`) installed and running
- A **domain name** (e.g., `code.example.com`) pointing to your instance
- **Ports 80 and 443 open** in your EC2 Security Group
- `sudo` access to the instance

---

## ✅ Step 1: Install code-server via RPM

```bash
wget https://github.com/coder/code-server/releases/download/v4.101.2/code-server-4.101.2-amd64.rpm
sudo dnf install -y ./code-server-4.101.2-amd64.rpm
```

Verify the installation:

```bash
code-server --version
```


---

## ⚙️ Step 2: Configure code-server

Create a systemd service
```bash
mkdir -p ~/.config/code-server
cat << EOF > ~/.config/code-server/config.yaml
bind-addr: 0.0.0.0:8080
auth: password
password: yourStrongPasswordHere
cert: false
EOF
```

Enable code-server service
```bash
cat << EOF | sudo tee /etc/systemd/system/code-server.service
[Unit]
Description=code-server
After=network.target

[Service]
User=ec2-user
ExecStart=/usr/local/bin/code-server
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable --now code-server
```

check if its running
```bash
sudo systemctl status code-server
```

---

## 🛠 Step 3: Configure Apache Virtual Host (HTTP)

Create the file:

```bash
sudo nano /etc/httpd/conf.d/code.example.com.conf
```

Add the following contents:

```
<VirtualHost *:80>
    ServerName code.example.com
    RewriteEngine On
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```

Update your DNS to point `code.example.com` to your EC2 instance's public IP.  

restart Apache
```
sudo systemctl restart httpd
```

Once DNS propagation completes, visiting `http://code.example.com` should redirect to HTTPS (even if there's a warning due to missing certificate — which will be fixed next).

---

## 🌐 Step 4: Let Apache Serve code-server Securely

Install required Apache modules:

```bash
sudo dnf install -y mod_ssl mod_proxy mod_proxy_http mod_proxy_wstunnel
```

---

## 🔐 Step 5: Obtain and Configure SSL with Certbot

Install Certbot and the Apache plugin:

```bash
sudo dnf install -y certbot python3-certbot-apache
```

Request the certificate:

```bash
sudo certbot --apache -d code.example.com
```

Certbot will automatically generate `/etc/httpd/conf.d/code.example.com-le-ssl.conf`.

---

## 🔁 Step 6: Enable Reverse Proxy in the SSL VHost

Edit the generated SSL config:

```bash
sudo nano /etc/httpd/conf.d/code.example.com-le-ssl.conf
```

Replace its contents with:

```
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName code.example.com

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/code.example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/code.example.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf

    ProxyPreserveHost On
    ProxyRequests Off

    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/

    # WebSocket support
    RewriteEngine On
    RewriteCond %{HTTP:Upgrade} =websocket [NC]
    RewriteRule /(.*) ws://127.0.0.1:8080/$1 [P,L]

    RewriteCond %{HTTP:Upgrade} !=websocket [NC]
    RewriteRule /(.*) http://127.0.0.1:8080/$1 [P,L]

    ErrorLog /var/log/httpd/code-server_error.log
    CustomLog /var/log/httpd/code-server_access.log combined
</VirtualHost>
</IfModule>
```

Restart Apache:

```bash
sudo systemctl restart httpd
```

---

## 📱 Step 7: Access code-server in the Browser

Visit:

```
https://code.example.com
```

You should see:
- A valid HTTPS connection
- The code-server login prompt
- A full browser-based VS Code interface

---

## ✅ Bonus: Auto-renewal

Certbot installs an automatic renewal job. You can test it manually with:

```bash
sudo certbot renew --dry-run
```

---

## 🧠 Conclusion

This setup enables full development from a browser — no native VS Code installation needed. Everything runs on the EC2 instance, securely served via Apache and Let's Encrypt.

Happy coding!

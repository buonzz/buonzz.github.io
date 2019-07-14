---
layout: post
title: How to Dockerize a Laravel Application (2019)
date: 2019-07-14 00:00:00.000000000 +08:00
permalink: how-to-dockerize-laravel-application-in-2019
header-img: "img/20586158743_46b96ef40b_k.jpg"
subtitle: "Do you need to quickly setup an isolated copy of  Nginx, FPM, Redis and MySQL to run your Laravel Application?  or maybe, you want to quickly deploy your app in a fresh VM without having to install the server stack manually?"
tags:
    - laravel workflow
    - virtualized laravel
    - isolated testing
    - php docker deployment 
---

Here is few reason why you might need to do this:

<ul>
    <li>You wan an isolated copy of the application stack either for testing or reproducing a bug</li>
    <li>quickly install all server software needed by the project without having to install them one by one, manually..</li>
    <li>Easily deploy your application in any hosting provider</li>
    <li>Help new programmer in their onboarding process</li>
    <li>Reproduce the production environment in your staging and local machine</li>
</ul>

<h1>What is in the stack?</h1>
<ul>
    <li>Nginx - for web server</li>
    <li>PHP-FPM - for interpreting our PHP files</li>
    <li>MySQL - for storing our stuffs</li>
    <li>Redis - for session, cache and queue</li>
</ul>

this is a standard setup that you'll see in production-grade deployment of Laravel Application, and we are about to re-produce it in your local machine!

I assume you already have an existing Laravel application, and you want to "dockerize" it. So lets get started!


<h2>Create the docker-composer.yml file</h2>
in the root of your laravel project, create a docker-composer.yml file then paste the following
<script src="https://gist.github.com/buonzz/9bbbf9a29199afdc2374ab12cfd272b1.js"></script>

<h2>Adjust your .env file</h2>
then in your .env file, add the following entries to the end
<script src="https://gist.github.com/buonzz/6307b38a3ccc89d3035a2f1bdd642c2b.js"></script>
make sure you also update the .env.example file, so that other developers who will clone the repo have those variables

Few variables that you need to pay attention to is the following

<ul>

<li><b>NGINX_HOST_HTTP_PORT</b> - this the port where your app will be served, changed it to something else if you dont want to serve it in port 80</li>
<li><b>MYSQL_DATABASE</b> - is the database name, ensure it is the same as DB_DATABASE</li>
<li><b>MYSQL_USER</b> - is the mysql username, ensure it is the same as DB_USERNAME</li>
<li><b>MYSQL_PASSWORD</b> - is the mysql password, ensure it is the same as DB_PASSWORD</li>
<li><b>DB_HOST</b> - set this to "mysql"</li>
<li><b>CACHE_DRIVER, QUEUE_CONNECTION, SESSION_DRIVER</b> - set these to "redis"</li>
<li><b>REDIS_HOST</b> - set this to "redis"</li>
</ul>
and thats it!

<h2>Download Dockerfiles</h2>

Download this <a href="https://github.com/buonzz/dockerized-laravel-files/archive/master.zip" rel="nofollow" target="_blank">folder</a>

and then place the contents to a folder called "docker" in your project root directory

You should now have the following new files in your Laravel Project

```
docker-compose.yml
docker
```

<h2>Run the containers!</h2>

Now, all you need to do is run the following to setup a complete stack in your local machine without installing anything but docker engine!

{% highlight bash %}
docker-compose up -d
{% endhighlight %}

it might took a while at first, as the containers is being downloaded. The next time you hit up this command, it will be instantanous. It should display something like this

<pre>
Starting yourapp_php-fpm_1   ... done
Starting yourapp_workspace_1 ... done
Starting yourapp_redis_1     ... done
Starting yourapp_mysql_1     ... done
Starting yourapp_nginx_1     ... done
</pre>

wherein the <b>yourapp</b> is the folder of your laravel application.

<h2>How do I run composer install, migrations etc?</h2>

It is simple, just use the "workspace" container that comes with the stack, all the required tools is present inside this container for you to use (Composer, mysql client, git, vim, nodejs, python etc)

To login to the workspace container:

for MacOS/Linux
{% highlight bash %}
docker exec -it yourapp_workspace_1 /bin/bash
{% endhighlight %}

for Windows
{% highlight bash %}
winpty docker exec -it yourapp_workspace_1 bash
{% endhighlight %}

Once inside, you can perform all stuffs that you need, such as 
<blockquote>Dont forget to replace the yourapp with your real laravel project folder name</blockquote>

{% highlight bash %}
php artisan key:generate
php artisan migrate
php artisan db:seed
{% endhighlight %}

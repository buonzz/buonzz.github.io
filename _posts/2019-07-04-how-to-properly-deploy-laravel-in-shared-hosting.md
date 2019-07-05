---
layout: post
title: How to properly deploy Laravel in Shared Hosting (2019)
date: 2019-07-04 00:00:00.000000000 +08:00
permalink: how-to-properly-deploy-laravel-in-shared-hosting-2019
header-img: "img/20586158743_46b96ef40b_k.jpg"
subtitle: "Deploy your Laravel app in cPanel properly using Git and Composer "
tags:
    - best practices
    - cpanel
    - cheap laravel hosting
    - how to install laravel in cpanel 
---

<h1>So you want to deploy your Laravel app ( Laravel 5.8 a the time of writing) in cPanel?</h1>

<p>You might have your own reasons, but Laravel is best hosted in a VPS or cheap virtual machine like <a href="https://m.do.co/c/21ed578f8cd3" target="_blank" rel="nofollow">Digital Ocean</a> which offers as low as $5/month.</p>

<p>But since you are here, lets do it and deploy Laravel in cPanel - the right way!</p>

<h3>What do you mean by the right way?</h3>
<p>There is a lot of tutorials out there that use deployment strategies that will bite you in the long run, for example:
    <ul>
        <li>Some articles out there tells you to generate the vendor folder in your local machine then upload everything via ftp (ugh, what if your local machine PHP version, extensions and etc is different than in the server?)</li>
        <li>How do you deploy additional changes in codes? you'll re-upload everything again?</li>
        <li>How do you make additional changes in MySQL tables? you'll dabble in PHPMyAdmin again?</li>
        <li>What if some developers had directly edited the files in the server?</li>
        <li>Some tutorials tells you to move files around and place them to public_html and dabble with paths of the framework - now you need to also make the same tweak in your local? plus what if you need to upgrade the Laravel version?</li>
    </ul>
</p>

<p>as you can see, things can get too messy when more incremental changes is done in the code and more developers is touching the project...</p>


<h2>Step 1  - login to your cpanel and check/install requirements</h2>

<ul>
    <li>Navigate to https://cpanel.yourdomain.com/ or http://yourdomain.com:2083</li>
    <li>Login with your cPanel credentials</li>
    <li>Go into Advance section and click the terminal icon as shown below</li>
</ul>

<img src="/assets/laravel-cpanel-screenshot-1.png" style="width:250px;">
<blockquote>
If you dont see the Terminal Icon, that means your shared account doesn't have "Shell Access" turned on. You might need to ask your Hosting Provider turn it on for you. You'll need to execute migrations and fetch Composer dependencies.
</blockquote>

<p>
It will launch the terminal. This is  where you will spend most of your work, so you better get familiar with it. 
<img src="/assets/laravel-cpanel-screenshot-2.png">
</p>

<blockquote>
You can also SSH to your shared account by using your local SSH client. If that is the case, consult your hosting provider (SSH port is not usually open for public, so you might need to get whitelisted first).
</blockquote>

<h3>Check PHP Version</h3>
<p>
check the php version by executing the following command:
{% highlight bash %}
php -v
PHP 7.2.19 (cli) (built: Jul  1 2019 21:49:20) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
{% endhighlight %}
It should be PHP >= 7.1.3, but 7.2 is more than ideal
</p>


<h3>Check PHP Extensions</h3>
<p>
check the php extensions installed:
{% highlight bash %}
$ php -m
[PHP Modules]
bcmath
iconv
imap
intl
json
...

[Zend Modules]
{% endhighlight %}

You'll see a bunch of list like above, but what is important is you have the following in the list:

<ul>
    <li>bcmath</li>
    <li>ctype</li>
    <li>json</li>
    <li>mbstring</li>
    <li>openssl</li>
    <li>PDO</li>
    <li>pdo_mysql</li>
    <li>tokenizer</li>
    <li>xml</li>
</ul>

</p>

<h2>Install Composer</h2>

configure your shell environment to use composer

{% highlight bash %}
echo 'alias composer="php -d allow_url_fopen=On /home/username/composer.phar"' >> ~/.bashrc
source ~/.bashrc
{% endhighlight %}

<blockquote>
replace username with your own username
</blockquote>

download composer installer
{% highlight bash %}
cd ~
curl -k -O https://getcomposer.org/installer
{% endhighlight %}

install it
{% highlight bash %}
php -d allow_url_fopen=On installer
{% endhighlight %}

verify you have it
{% highlight bash %}
composer -V
Composer version 1.7.2 2018-08-16 16:57:12
{% endhighlight %}

<h2>Configure Git</h2>

<blockquote>Most cPanel installations comes with Git pre-installed, if it is not, you might need to ask your hosting provider to have it installed.
</blockquote>

if you dont have an SSH key yet configured for this account, you need to generate one. Please check first by executing the following command
{% highlight bash %}
cat ~/.ssh/id_rsa.pub
{% endhighlight %}

if it says cat: /home/user/.ssh/id_rsa.pub: No such file or directory, that means you dont have an ssh key yet. You can generate one by doing the following:
{% highlight bash %}
ssh-keygen -t rsa -b 4096 -C "cpanel username"
{% endhighlight %}

once done, execute this again
{% highlight bash %}
cat ~/.ssh/id_rsa.pub
{% endhighlight %}
and copy the outputted text to your clipboard, youll gonna need to paste it in your Git repo.

I assume your app is hosted in github, for example  https://github.com/username/repo-name/

Go into Settings Tab -> Deploy Keys in the side bar  https://github.com/username/repo-name/settings/keys

You'll see something like this

<img src="/assets/laravel-cpanel-deploykeys.png"/>

Click the "Add deploy key" button and paste the output of the command earlier  in the Key field(your ssh public key). Leave the "Allow write access" unchecked. 

<h2>Setup the app</h2>

Now, clone the repo to a folder named "app", it should be in /home/username/app

{% highlight bash %}
git clone git@github.com:username/repo.git app
{% endhighlight %}

go to the app folder and install the dependencies

{% highlight bash %}
composer install
{% endhighlight %}

while that is installing, create a new MySQL database in cPanel in DATABASES -> MySQL Databases section. take note of the database name, username and password.

copy the .env.example file to .env

{% highlight bash %}
cp .env.example .env
{% endhighlight %}

edit the .env file and configure it with your setting

{% highlight bash %}
nano .env
{% endhighlight %}

few key settings that is important are

{% highlight bash %}
DB_HOST=localhost
DB_CONNECTION=mysql
DB_PORT=3306
DB_DATABASE=yourdbname
DB_USERNAME=youruser
DB_PASSWORD=yourpass
APP_ENV=production
APP_DEBUG=false
{% endhighlight %}

generate the application key

{% highlight bash %}
php artisan key:generate
{% endhighlight %}

run the migrations

{% highlight bash %}
php artisan migrate
{% endhighlight %}

<blockquote>
If you received an error of "Specified key was too long; maxkey length is 1000 bytes", you need to check this article for the <a href="https://laravel-news.com/laravel-5-4-key-too-long-error" target="blank" rel="nofollow">fix</a>. Apply the fix in the repo, then issue "git pull origin master" so that this copy will receive the fix . Then go to PhpMyAdmin, drop all the tables there and try running the migrate command again.
</blockquote>


finally, set the permission of the storage folder so that the web server can write to it

{% highlight bash %}
chmod -R 775 storage
{% endhighlight %}

<h2>Make the app accessible to public</h2>

right now, the app is not accessible outside because we placed it under /home/user/app directory. The only folder that is accessible to public is /home/user/public_html
but we dont want to place all of our framework files into public_html folder. So what we gonna do is simply make a <a href="https://en.wikipedia.org/wiki/Symbolic_link" target="_blank" rel="nofollow">Symbolic link</a> to our /home/user/app/public folder.

to do this, backup first a copy of the public_html folder
{% highlight bash %}
mv  public_html public_html_old
{% endhighlight %}

create the symlink

{% highlight bash %}
ln -s /home/user/app/public /home/user/public_html
{% endhighlight %}

your folder structure should be similar to this
{% highlight bash %}
public_html -> /home/user/app/public
public_html_old
www -> public_html
{% endhighlight %}

now go ahead and visit your site, it should be working now!


<h5>Configure Cron Job</h5>

We are not finished yet!  if you are app needs to run some background processes, you might need to configure the cron job, to do this go to Advanced -> Cron Job menu in cPanel admin.

and add the following
{% highlight bash %}
cd /home/user/app/ && php artisan schedule:run >> /dev/null 2>&1
{% endhighlight %}

<img src="/assets/laravel-cpanel-cron.png">

You are done!


<h5>Workflow</h5>

Now that you have your app setup, how do you push new changes to it? Simple:

<ul>
<li>make changes in your local environment</li>
<li>push to remote repo</li>
<li>git pull in the cPanel</li>
</ul>

Or you can setup some tool, to automatically issue git pull for you by setting up a webhook in your Github repo. This is so that everytime someone pushes in your master branch, it will automaticaly deploy in your cPanel without you even logging in to terminal!

This is what actually Laravel Envoyer is doing, among other things such as managing releases and supporting hooks for each stage.



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


<>
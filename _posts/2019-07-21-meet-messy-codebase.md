---
layout: post
title: Meet "Messy CodeBase" - a purposely horribly written Laravel app
date: 2019-07-20 00:00:00.000000000 +08:00
permalink: meet-messy-codebase
header-img: "assets/IMG_3230-min.JPG"
subtitle: "Because there is no better way  telling on how a properly implemented Laravel app should be, other than giving an example of how a horrible one looks like..."
tags:
    - laravel best practice
    - real world app
    - devops
    - LaraveLPH
    - laravel bootcamp
    - php
---

<style>
.post-heading h1,.post-heading h2 { background-color: rgba(0,133,161,0.5); }
</style>


<h1>A case study about a horribly written Laravel Application</h1>

During my talk in a <a href="https://www.facebook.com/events/347298622600242/" target="_blank" rel="nofollow">Laravel Bootcamp</a> last July 20, 2019. I used this <a href="https://github.com/laravelbestpractices/messy-codebase/issues" target="_blank" rel="nofollow">codebase</a> to explain the best practices in writing Laravel applications. Here is a gist of my talk:

<iframe width="560" height="315" src="https://www.youtube.com/embed/meXZ8C5WJZo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<h2>Why not just go through a list of "best practices"</h2>

The fact is, I already did, by creating a this <a href="http://www.laravelbestpractices.com/">website</a>, but it will gonna be a boring talk to just read those text aloud during the presentation isn't it?

The thing about human in general is, we understand and remember things a lot better when you have to handle a bad situation and you started asking yourself "how these things supposedly work?".

For example, when you are driving and apprehended by a traffic enforcer in the road due to some traffic violation, you started to get curious of what are the traffic rules relevant to your violations.

So the situation "being apprehended" creates the spark of interest to the rules, that is in contrast to you being given a 23 page list of traffic rules and asked to memorize it.



<h2>Culmination of Common mistakes</h2>

When you visit the list of issues in this <a href="https://github.com/laravelbestpractices/messy-codebase/issues">link</a>, there is a sort of like laundry list of issues that this codebase has.  These are common issues (a bit exaggerated ones) that you/team might encounter in a real-world environment. For example 

<ul>
	<li>Wordpress developers tends to not use .env files when storing sensitive information</li> 
	<li>plain php developers tends to place blocking API calls and business logic inside view files</li> 
	<li>etc</li> 
</ul>

I would like to reiterate that mentioning those frameworks and methodologies doesn't mean they are less capable in writing quality codes, but the intent is to make it easier for them to adapt in the paradigm shift and thought process that Laravel requires.


<h2>A Story about an evolution</h2>

the MessyCodebase project is a story about how a spaghetti code evolved into a clean code. Being the <a href="https://www.laravelbestpractices.com">laravelbestpractices.com</a> as it's protagonist and messycodebase always trying to violate every known good principle.  It is a quest and adventure that places you (as a developer) can also undertake -- by cloning the repo, attempting to refactor it and coming up with a better version of it...
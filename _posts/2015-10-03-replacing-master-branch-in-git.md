---
layout: post
title: Replacing Master Branch in Git
date: 2015-10-03 16:59:31.000000000 +08:00
tags:
- laravel and neo4j
- neo4j basics
permalink: replacing-master-branch-in-git/
header-img: "img/16126940706_0e45f64602_k.jpg"
subtitle: "In a project managed by Git, there might be times that you need to completely trash the master branch and completely replace with another branch"
---

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- inline to posts -->
<ins class="adsbygoogle"
     style="display:inline-block;width:250px;height:250px"
     data-ad-client="ca-pub-0845730634111642"
     data-ad-slot="8010460709"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

In a project managed by Git, there might be times that you need to completely trash the master branch and completely replace with another branch. 

lets say you have a "v2" branch. you can replace the master branch with the following steps

Checkout to the v2 branch

{% highlight bash %}
git checkout v2
{% endhighlight %}

merge it with the master branch with "ours" strategy.

{% highlight bash %}
git merge -s ours master
{% endhighlight %}

Checkout to the master branch

{% highlight bash %}
git checkout master
{% endhighlight %}

merge the v2 branch to master

{% highlight bash %}
git merge v2
{% endhighlight %}

The old master branch is now gone and the contents of the master is the contents of v2 branch.

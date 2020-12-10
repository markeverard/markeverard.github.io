---
title: Output cache, a Christmas cautionary tale
date: 2013-12-30T17:00:45+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2013/12/30/outputcache-a-christmas-cautionary-tale/
dsq_thread_id:
  - "2082079059"
categories:
  - Episerver
  - Technology
---

The <a title="Improving Performance with Output Caching (C#)" href="http://www.asp.net/mvc/tutorials/older-versions/controllers-and-routing/improving-performance-with-output-caching-cs" target="_blank">output cache in ASP.NET</a> allows a developer to specify that the ASP.NET runtime cache the output of any dynamically generated page against a given policy (such as a time-based cache for 5 minutes). By default, ASP.NET will¬ execute the code to generate a HTML page each time the page is requested. If you are serving a static brochure-ware type page; that can mean your server repeatedly generating identical output for many different visitors. This is obviously an inefficient use of server resource, and a great case for specifying an output cache.

The output cache mechanism in ASP.NET is sophisticated enough to handle data input scenarios from an HTTP POST. In this case, regardless of the output cache policy the response will never be served from cache and will always be executed on the server, meaning that any user submitted data will always be processed.

Additionally you can provide values for the <a title="OutputCache attributes" href="http://msdn.microsoft.com/en-us/library/hdxfb6cy%28v=vs.100%29.aspx" target="_blank">VaryByParam</a> setting. This property allows you to specify GET request parameters (querystrings, protocols etc) that will be recognised by the output cache. That means you could have a dynamic page that is delivered from the output cache; but the cache recognises that a querystring parameter value will produce a different output. For example: /opportunity.aspx?id=2 & /opportunity.aspx?id=3 will have two versions stored and delivered from cache (well actually it will have _n_ cached versions, one will be created for every value of id requested). Be careful when using this as you can increase your application&#8217;s memory usage if you have a large parameter space.

The output cache can be set in a variety of ways, such as on a page level as a directive (WebForms) or as a controller attribute (MVC). The specific CMS platform we were using (EPiServer) allowed the output cache to be set on a global level through application configuration.

The output cache is simple to use and can have some big performance benefits, however it can also have some very dire consequences and produce some difficult to diagnose bugs. The following is a cautionary tale of one I just came across üôÅ

## The problem

The following shows the reported problem user journey, where there were concerns along that submitted data was being &#8216;mixed up&#8217;.

![OutputCache Journey](http://www.markeverard.com/wp-content/uploads/2013/12/OutputCache-Journey.png)

- User searches for an opportunity
- User views a details page about an opportunity
- User chooses to submit data about an opportunity
- User is redirected to the next step of the submission form, served from a different page
- User submits the 2nd page of data
- etc etc / end of journey 

## Can you guess what was happening?
The POST in step 4. creates a record in the database and then redirects the user to the second step of the form using the newly created record identifier as a key. The output cache kicked in for the GET request when the form was written to the browser; resulting in a html form being written from the cache. This meant the form was always created as below with the first user&#8217;s unique database key rather than the one passed in the querystring.

~~~
<form id="aspnetForm" action="/submit2.aspx?id=2&uid=3" method="post" name="aspnetForm">
~~~

The result of this meant that multiple users attempting the submission user journey within the output cache expiration period would end up being redirected to a page containing a submission form pointing to the same record in the database. As this was a low usage form, we didn&#8217;t see many mixed up records (good) but it was also much harder to identify (bad).

## Resolution
The following options were available:

* Add in the querystring parameters used by the problem pages to the VaryByParam property
* Disable the output cache

I decided the latter was the most sensible course of action for us as we had a lot of parameters used across the site, and also we currently don&#8217;t need any output cache. In fact, with anything but a very high-load EPiServer site you rarely do. The EPiServer CMS caching framework (which is at a lower database level) ensures a good level of performance. Joel Abrahamsson has written a really useful summary of the <a title="The EPiServer CMS Output Cache Explained " href="http://joelabrahamsson.com/the-episerver-cms-output-cache-explained/" target="_blank">output cache within EPiServer</a> and also the more <a title="How EPiServer CMS caches PageData objects" href="http://joelabrahamsson.com/how-episerver-cms-caches-pagedata-objects/" target="_blank">general caching framework in EPiServer</a> .

## Top tips / Tip tops for avoiding output cache pain
1. Be very sure that a high-level cache, such as the output cache is what you really need to improve performance.
2. Record in your development documentation that you are using an output cache and importantly why you are using it, so that fellow developers understand the development constraints.
3. Modify your test cases to specifically validate functionality that could be impacted by the output cache.

## TL;DR
Unnecessary performance optimisation caused data integrity issues.
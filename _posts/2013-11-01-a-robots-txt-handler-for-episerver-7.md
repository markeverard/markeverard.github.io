---
title: A Robots.txt handler for EPiServer 7
date: 2013-11-01T11:59:39+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2013/11/01/a-robots-txt-handler-for-episerver-7/
dsq_thread_id:
  - "1926515558"
categories:
  - Episerver
---
<a title="Get all of your technology needs at David Tec" href="http://www.david-tec.com/" target="_blank">David Knipe</a> put together a really nice module for EPiServer CMS 6R2 called <a title="EPiRobots for EPiServer CMS 6 R2" href="http://www.david-tec.com/2011/07/EPiRobots-A-generic-robotstxt-handler-for-your-EPiServer-CMS-6-R2-site/" target="_blank">EPiRobots</a> which allowed Site Administrators to manage and update a site&#8217;s robots.txt content. Unfortunately the module doesn&#8217;t work with CMS 7 🙁

I asked Dave about his plans for a version compatible with CMS 7 and there is apparently some work/ideas in the pipeline. However that news did not satisfy my **IMMEDIATE** requirement! Dave also mentioned that I could &#8216;do whatever I wanted with the source code&#8217;. So I&#8217;ve forked it!

I&#8217;ve upgraded the forked version to work against CMS 7. The main change was to remove an initialisation piece that added an explicit handler for the Robots.txt path through the EPiServer UrlRewriting API (which has been overhauled for CMS 7). The robots.txt content is now served from a standard MVC controller with a route being configured in an EPiServer initialization module. I&#8217;ve also rebuilt the Admin user interface using MVC and explicitly included the views as content within the NuGet package rather than <a title="EPiServer plugins served from a single .dll" href="http://labs.episerver.com/en/Blogs/Johano/Dates/2008/6/EPiServer-PlugIns-in-one-single-dll/" target="_blank">serving them from the .dll</a>. This is just my personal preference. Leveraging NuGet and the simplicity of the solution wins for me! It also means you can change the view look and feel if you so wish.

The source code is available at <https://github.com/markeverard/POSSIBLE.RobotsTxtHandler>

A package (now renamed from EPiRobots) will be available in the EPiServer Nuget Feed (as soon as its been approved) &#8211; [http://nuget.episerver.com/](http://nuget.episerver.com/en/?search=possible&sort=MostDownloads&page=1&pageSize=10) &#8211; search for POSSIBLE.RobotsTxtHandler
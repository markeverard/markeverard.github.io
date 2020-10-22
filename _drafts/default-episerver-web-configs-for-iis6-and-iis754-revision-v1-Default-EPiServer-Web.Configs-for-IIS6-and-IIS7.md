---
id: 4961
title: Default EPiServer Web.Configs for IIS6 and IIS7
date: 2017-07-11T16:33:01+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/54-revision-v1/
permalink: /2017/07/11/54-revision-v1/
---
I consider myself lucky to be running a local development environment using Windows 7 which means all of my development sites run off of IIS7 (unless of course, I running them through the built-in Visual Studio web server).

IIS7 is dramatically [different](http://www.code-magazine.com/Article.aspx?quickid=060103) to IIS6 and Microsoft went to great pains to provideÂ backwards compatibility (classic/integrated pipeline). However we&#8217;re now stuck in a technical limbo where we often end up running sites across both IIS versions.

Recently I&#8217;ve been working on some older EPiServer sites which are hosted on IIS6 boxes in production but I&#8217;ve had to develop locally on IIS7. Working across different environments like this is painful.

Whilst there are upgrade scripts and also a useful [technical note](http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-5/EPiServer-CMS-5-R2/Changes-Between-IIS6-and-IIS7/) about the differences between the configuration files on EPiServer World, its still all too easy to get to the stage where you can&#8217;t get elements of the site to work as intended and you feel you&#8217;ve got no option but to compare configurations files line by line to &#8216;spot&#8217; any differences. This especially difficult when considering the size of the [web.config files in EPiServer](http://world.episerver.com/Blogs/Magnus-Strale/Dates/2009/10/What-do-we-do-about-config-file-bloat/) and ASP.NET 3.5 in general.

Often I&#8217;ve just wanted to start from scratch with a default EPiServer web.config file targeted at either IIS6 or IIS7. If you ever find yourself in thisÂ same configuration management hell then this little tidbit may come in handy.

DefaultÂ EPiServer web.config files for both IIS6 and IIS7 can be found in your %PROGRAMFILES%EPiServerCMS<version-number>Application directory. These are called Web.Config.6 and Web.Config.7 respectively.

Obviously don&#8217;t edit these files, as they are no doubt used by the EPiServer Deployment Center when creating new local sites, but as a fallback to a configuration file that works &#8211; then go for it.

<div id="_mcePaste" style="position: absolute; left: -10000px; top: 0px; width: 1px; height: 1px; overflow: hidden;">
  MultiplexingMembershipProvider
</div>
---
id: 331
title: Using NuGet for EPiServer third-party libraries
date: 2010-11-04T13:37:17+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=331
permalink: /2010/11/04/using-nuget-for-episerver-third-party-libraries/
dsq_thread_id:
  - "1073095627"
  - "1073095627"
categories:
  - Episerver
---
<a title="NuGet " href="http://nuget.codeplex.com/" target="_blank">NuGet</a> (formerly known as NuPack) is an third party library package management system, heavily integrated with Visual Studio 2010. Its received a lot of attention since Microsoft first announced it, and for good reason. It enables you to simply include third-party libraries into your solution, where NuGet will do all of the heavy lifting and set-up for you.

This can be a real time saver over installing a library manually where you&#8217;d have toÂ download the library, unzip it, copy it to your solution, add reference in Visual Studio, and then possibly update the configuration. NuGet offers a better way&#8230;.

EPiServer developers already have a package manager of sorts viaÂ Â <a title="EPiServer Deployment Center" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-5/EPiServer-CMS-5-R2-SP1/EPiServer-CMS-Installation-and-Deployment-System-Overview/#EPiServerDeploymentCenter" target="_blank">EPiServer&#8217;s own Deployment Center</a> &#8211; which allows you to deploy pre-made .epimodule or .zip packages to existing sites. This is actually a similar concept to NuGet &#8211; but is implemented against IIS sites rather than integrated directly with Visual Studio, and to be honest not many developers go as far as to create deployment modules for their EPiServer contributions.

For me, the great advantage to NuGet is the ability to browse and search multiple package feeds for the latest modules, and then with a single click have them and their dependencies included in your solution.Â NuGet by default, points to a Microsoft feed (that anybody can contribute packages to), but it can also point to a private feed hosted on a network share or hosted through IIS (using an included Microsoft MVC web application).

This means that along with the main Microsoft public feed you could have:

  1. A company feed of approved packages to be used in standard company ASP.NET solutions.
  2. A public feed of packages specific to a particular product Â (anybody see what I&#8217;m getting at here&#8230;..)

So EPiServer, how about setting up your very own NuGet feed? ([It&#8217;s very simple](http://nuget.codeplex.com/wikipage?title=Hosting%20Your%20Own%20Local%20and%20Remote%20NuPack%20Feeds "Hosting your own NuGet feeds")). Along with the EPiServer code section, this would provide a central and consistent way of searching for and installing new EPiServer modules, rather than the increasing fragmented methods at the minute (within <a title="EPiServer World Blogs" href="http://world.episerver.com/blogs" target="_blank">blogs</a>, <a title="EPiCode" href="https://www.coderesort.com/p/epicode/wiki/WikiStart" target="_blank">EPiCode</a> and a growing number of <a title="EPiServer CodePlex projects" href="http://www.codeplex.com/site/search?query=episerver&ac=8" target="_blank">CodePlex projects</a>).

As a small encouragement I&#8217;ve put together (the very first) EPiServer NuGet package.

### An Example Package

Its very straightforward to <a title="Creating a NuGet package " href="http://nuget.codeplex.com/documentation?title=Creating%20a%20Package" target="_blank">create your own package</a> using the tools provided on the codeplex site

I&#8217;ve made a NuGet package for every developer&#8217;s favourite EPiServer third-party library &#8211; Page Type Builder (version 1.3). This is a pretty simple package which includes the two core PageTypeBuilder libraries, and references to two other NuGet packages (Castle.Core and StructureMap) that Page Type Builder depends upon.

I&#8217;ve temporarily hosted this package on my blog &#8211; so if any of you fancy trying it out, first <a title="NuGet download page" href="http://nuget.codeplex.com/releases/view/52016" target="_blank">download and install NuGet</a>, then from within Visual Studio, right-click on your references folder in a solution and click &#8216;add package reference&#8217;. Click settings and add <a title="NuGet demo feed" href="http://www.markeverard.com/nuget/feed.xml" target="_blank">http://www.markeverard.com/nuget/feed.xml</a> as your default package server. Trying to add a package reference will then get you a screen like this&#8230;..

[<img class="aligncenter size-full wp-image-345" title="nuget-pagetypebuilder" src="/assets/uploads/2010/11/nuget-pagetypebuilder.jpg" alt="nuget-pagetypebuilder" width="600" height="431" srcset="/assets/uploads/2010/11/nuget-pagetypebuilder.jpg 600w, /assets/uploads/2010/11/nuget-pagetypebuilder-300x216.jpg 300w, /assets/uploads/2010/11/nuget-pagetypebuilder-580x417.jpg 580w, /assets/uploads/2010/11/nuget-pagetypebuilder-320x230.jpg 320w" sizes="(max-width: 600px) 100vw, 600px" />](/assets/uploads/2010/11/nuget-pagetypebuilder.jpg)

If you now click the install Page Type Builder, NuGet will automatically download the package and the Castle.Core and StructureMap dependencies and add them to your solution. How easy is that!

NuGet is currently only in CTP, so its still rough around the edges, especially the UI. So if its a bit flakey at the minute then rest assured it will get better. One missing feature is the ability to resolve dependencies from other public feeds, meaning that I&#8217;ve also had to host Castle.Core and StructureMap packages rather than using the packages available on the public Microsoft feed.
---
id: 5311
title: Modifying Alloy Templates to work with EPiServer patches delivered via Nuget.
date: 2017-07-11T16:47:47+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/1782-revision-v1/
permalink: /2017/07/11/1782-revision-v1/
---
Along with their latest CMS release, EPiServer have also changed the model that they use to deliver hot-fixes to partner developers. Previously they were delivered as a singular hot-fix obtained from Developer Support or were rolled into more major service-pack releases. From now on, hot-fixes and maintenance releases will be delivered directly from the <a title="EPiServer Nuget Feed" href="http://nuget.episerver.com/" target="_blank">EPiServer Nuget feed</a>.

This is a seriously great improvement as it meansÂ fixes get pushed directly to us on a more regular basis, rather than us having to identify issues and obtain fixes via Developer Support. As of now, EPiServer have already <a title="EPiServer CMS 7 Patch 2" href="http://world.episerver.com/Articles/Items/EPiServer-7--Patch-2/" target="_blank">delivered 2 patch releases</a> of EPiServer.CMS.Core onto their Nuget feed (versions 7.0.586.4 and 7.0.586.8 &#8211; kudos also for the better assembly versioning).

However to take maximum advantage of the new delivery model, you have to have your solutions set up to reference the EPiServer libraries from Nuget. The demo Alloy templates are not set up in this way. I&#8217;ve put the steps to make them update-able via Nuget below. You should do similar to your CMS7 solutions too.

### 1) Remove/Change the Configuration build tasks &#8211; (sorry Ted)

The <a title="Alloy Templates for CMS7 " href="http://world.episerver.com/Articles/Items/Alloy-Templates-for-EPiServer-CMS-7/" target="_blank">Alloy templates have a method of chained configuration build tasks</a> to create a series of both generic and site specific configuration build transforms. This results in the configuration files in your site root being overwritten entirely on every build. Unfortunately this doesn&#8217;t work well with Nuget which will apply any package configuration transforms to config files in the site root &#8211; meaning you have to <a title=" Keeping configuration transforms after applying EPiServer 7 Patch " href="http://dhvik.blogspot.co.uk/2013/02/keeping-configuration-transforms-after.html" target="_blank">manually reapply any Nuget changes into the [Configuration]/Common/Web.Common.config file</a>. This doesn&#8217;t cut it for me, so I just removed the build tasks by deleting the Post-Build tasks in the .csproj. I&#8217;m sure there is a way to keep the chained transforms (if you like them) and have them built from the root config files but I&#8217;ll leave that for someone else ðŸ™‚

### 2) DeleteÂ [Libraries] folder from the installation

The Alloy solution is set up to reference the EPiServer libraries it needs directly from the [Libraries] folder within the solution. Instead we need to change this to so that the solution uses the EPiServer.CMS.Core and the EPiServer.Framework packages from EPiServer Nuget.

  * Delete the [Libraries] folder from the project
  * Remove all references to EPiServer from your project references. Also remove any references to StructureMap, log4net, NewtonSoft.Json and Castle.Core
  * Make sure you have the <a title="Installing EPiServer patches" href="http://world.episerver.com/Documentation/Items/Installation-Instructions/Installing-EPiServer-Patches/" target="_blank">EPiServer Nuget feed added to your local Nuget tools</a>.
  * Then you should run the following commands in the <a title="Finding and Installing a NuGet Package Using the Package Manager Console" href="http://docs.nuget.org/docs/start-here/using-the-package-manager-console" target="_blank">Nuget Package Manager Console</a> &#8211; (which is absolutely the best way to work with Nuget)

Then you should run the following commands to install the packages you need &#8211; these commands install specific versions of each package.

<pre class="brush: xml; title: ; notranslate" title="">Install-Package EPiServer.CMS.Core -Version 7.0.586.1
Install-Package EPiServer.Framework -Version 7.0.859.1
Install-Package Castle.Core -Version 3.1.0
Install-Package StructureMap -Version 2.6.1.0
Install-Package Newtonsoft.Json -Version 4.5.7
Install-Package log4net -Version 1.2.10
</pre>

Rebuild the solution and you should be back where you started (a working Alloy demo site) and also now set up to use Nuget to update to the latest patch releases. Incidentally I&#8217;m not sure why the EPiServer Nuget packages don&#8217;t correctly list their <a title="Nuget - specifying dependent packages" href="http://docs.nuget.org/docs/reference/nuspec-reference#Specifying_Dependencies" target="_blank">dependencies</a>. To me, EPiServer.CMS.Core is dependent on all of the packages listed above. Having these dependencies stated in the nuspec file would simplify the initial installation step, and no doubt help dependency management going forward.

### 3) Install the Updates from Nuget

Now you&#8217;re in a position to see just how easy applying a update is. Open your solution, go to the Nuget package manager console and type:

<pre class="brush: xml; title: ; notranslate" title="">Update-Package EPiServer.CMS.Core
</pre>

Voila &#8211; all of the latest EPiServer hot-fixes delivered straight into your solution via one command and now ready for you to build / validate and distribute to your client!
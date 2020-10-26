---
title: Find(ing) missing dependencies when installing New EPiServer Find
date: 2014-06-26T09:54:36+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2014/06/26/finding-missing-dependencies-when-installing-new-episerver-find/
dsq_thread_id:
  - "2796513570"
categories:
  - Episerver
---
Recently we installed the <a title="New EPiServer Find" href="http://world.episerver.com/Articles/Items/New-EPiServer-Find/" target="_blank">New EPiServer Find</a> so our editors could benefit from the sleek new interface from which they could view and optimise our user’s search journeys.

Installation on our DEV machines was simple and performed through Nuget. However, after deployment we noticed that our upstream environments were broken (obviously we didn’t deploy as far as production!)

The environments were throwing the following exception on Initialisation.

~~~csharp
`The system cannot find the file specified.<br />
at EPiServer.Find.UI.FindInitializationModule.Initialize(InitializationEngine context)`
~~~

## The cause
The EPiServer Find.UI is dependent on the following libraries

* `Newtonsoft.Json.dll`
* `System.Net.Http.dll`
* `System.Net.Http.Formatting.dll`
* `System.Net.Http.WebRequest.dll`
* `System.Web.Http.dll`
* `System.Web.Http.WebHost.dll`

These libraries are a part of MVC4 / WebAPI. I’m guessing that the Find UI uses WebAPI for delivering some of its goodness.

A solution is detailed <a title="Manually Installing References Required by the Web API Service" href="http://www.telerik.com/support/kb/data-access/details/manually-installing-references-required-by-the-web-api-service" target="_blank">here</a>, but in summary you have two choices:

1. _**The wrong way**_– Install MVC4 on your build server / target environments. The MVC4 installer will add these libraries to the GAC, so your application will be able to resolve them.
2. _T_**_he right way_ –** Include these dependencies as part of your application using Nuget. Install the `Microsoft.AspNet.WebApi.WebHost` package.

The right way means that your application is portable and is not dependent on system components being deployed on each server environment. Additionally the `EPiServer.Find.Nuget` package should explicitly list `Microsoft.AspNet.WebApi.WebHost` as a dependency. Put it on the backlog please Find team

Happy Finding!
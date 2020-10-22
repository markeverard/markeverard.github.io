---
id: 736
title: A Page Type security overview plugin for EPiServer
date: 2012-04-12T14:00:01+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=736
permalink: /2012/04/12/a-page-type-security-overview-plugin-for-episerver/
dsq_thread_id:
  - "1076701802"
  - "1076701802"
categories:
  - Episerver
---
I&#8217;ve been dealing with more of EPiServer&#8217;s security and access control functionality recently. See my recent post about creating an attribute based framework for <a title="Role and user based property security for EPiServer " href="http://www.markeverard.com/2012/02/13/role-and-user-based-property-security-for-episerver/" target="_blank">managing security at the property level</a> as a example.

When it comes to enterprise level security requirements,Â governance is the key. This means not just having the ability to define yourÂ security policy (through an admin panel for example) but also having a current and clear overview of your security setup.

Whilst EPiServer provides this for the creating page functionality (Set Access Rights page and the page-tree), it doesn&#8217;t offer this for Page Types. Certainly this is something we can improve upon so I&#8217;ve put together a simple Admin Plugin (<a title="Creating EpiServer Plugins using MVC" href="http://world.episerver.com/Blogs/Paul-Smith/Dates1/2011/8/Creating-EPiServer-AdminEdit-Plug-ins-using-MVC/" target="_blank">built using MVC</a> &#8211; why would you use anything else these days?)

The plugin lists each Page Type, whether it is available in edit mode, the users and roles who can create pages of that page type and also the allowable child page types for that page type.

A Nuget package (FortuneCookie.PageTypeSecurityOverview) built against EPiServer 6R2 is available from the <a title="EPiServer Nuget" href="http://nuget.episerver.com/" target="_blank">EPiServer Nuget feed</a>. The source code is available on <a title="FortuneCookie.PageTypeSecurityOverview on GitHub" href="https://github.com/markeverard/FortuneCookie.PageTypeSecurityOverview" target="_blank">GitHub â€“ https://github.com/markeverard/FortuneCookie.PageTypeSecurityOverview</a>

[<img class="aligncenter size-full wp-image-737" title="Page Type Security Overview" alt="Page Type Security Overview" src="http://markeverard.azurewebsites.net/wp-content/uploads/2012/04/pagetypesecurityoverview.png" width="700" height="463" srcset="https://www.markeverard.com/wp-content/uploads/2012/04/pagetypesecurityoverview.png 700w, https://www.markeverard.com/wp-content/uploads/2012/04/pagetypesecurityoverview-300x198.png 300w, https://www.markeverard.com/wp-content/uploads/2012/04/pagetypesecurityoverview-580x384.png 580w, https://www.markeverard.com/wp-content/uploads/2012/04/pagetypesecurityoverview-320x212.png 320w" sizes="(max-width: 700px) 100vw, 700px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2012/04/pagetypesecurityoverview.png)
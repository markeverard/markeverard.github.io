---
id: 736
title: A Page Type security overview plugin for EPiServer
date: 2012-04-12T14:00:01+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1076701802"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Security]
---
I&#8217;ve been dealing with more of EPiServer&#8217;s security and access control functionality recently. See my recent post about creating an attribute based framework for <a title="Role and user based property security for EPiServer " href="http://www.markeverard.com/2012/02/13/role-and-user-based-property-security-for-episerver/" target="_blank">managing security at the property level</a> as a example.

When it comes to enterprise level security requirements, governance is the key. This means not just having the ability to define your security policy (through an admin panel for example) but also having a current and clear overview of your security setup.

Whilst EPiServer provides this for the creating page functionality (Set Access Rights page and the page-tree), it doesn&#8217;t offer this for Page Types. Certainly this is something we can improve upon so I&#8217;ve put together a simple Admin Plugin (<a title="Creating EpiServer Plugins using MVC" href="http://world.episerver.com/Blogs/Paul-Smith/Dates1/2011/8/Creating-EPiServer-AdminEdit-Plug-ins-using-MVC/" target="_blank">built using MVC</a> &#8211; why would you use anything else these days?)

The plugin lists each Page Type, whether it is available in edit mode, the users and roles who can create pages of that page type and also the allowable child page types for that page type.

A Nuget package (FortuneCookie.PageTypeSecurityOverview) built against EPiServer 6R2 is available from the <a title="EPiServer Nuget" href="http://nuget.episerver.com/" target="_blank">EPiServer Nuget feed</a>. The source code is available on <a title="FortuneCookie.PageTypeSecurityOverview on GitHub" href="https://github.com/markeverard/FortuneCookie.PageTypeSecurityOverview" target="_blank">GitHub https://github.com/markeverard/FortuneCookie.PageTypeSecurityOverview</a>

[<img class="aligncenter size-full wp-image-737" title="Page Type Security Overview" alt="Page Type Security Overview" src="/assets/uploads/2012/04/pagetypesecurityoverview.png" width="700" height="463" />](/assets/uploads/2012/04/pagetypesecurityoverview.png)
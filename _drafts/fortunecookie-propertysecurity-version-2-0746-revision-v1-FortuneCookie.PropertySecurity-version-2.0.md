---
id: 5401
title: FortuneCookie.PropertySecurity version 2.0
date: 2017-07-11T16:49:59+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/746-revision-v1/
permalink: /2017/07/11/746-revision-v1/
---
The coding machine that is <a title="Lee Crowe on EPiServer World" href="http://world.episerver.com/System/Users-and-profiles/Community-Profile-Card/Croweman/" target="_blank">Lee Crowe</a> and myself have made a few additional changes to the <a title="Role and user based property security for EPiServer" href="http://www.markeverard.com/2012/02/13/role-and-user-based-property-security-for-episerver/" target="_blank">PropertySecurity attribute framework</a> I blogged about a few weeks ago.

[<img class="wp-image-750 aligncenter" title="Security" alt="" src="http://markeverard.azurewebsites.net/wp-content/uploads/2012/04/bpi75.jpg" width="360" height="240" srcset="https://www.markeverard.com/wp-content/uploads/2012/04/bpi75.jpg 600w, https://www.markeverard.com/wp-content/uploads/2012/04/bpi75-300x200.jpg 300w, https://www.markeverard.com/wp-content/uploads/2012/04/bpi75-580x387.jpg 580w, https://www.markeverard.com/wp-content/uploads/2012/04/bpi75-320x213.jpg 320w" sizes="(max-width: 360px) 100vw, 360px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2012/04/bpi75.jpg)

The changes in Version 2.0 are:

  1. It&#8217;s now absolutely dependent on PTB2 &#8211; you&#8217;ll see why below.
  2. PropertySecurity attributes can now be applied to PropertyGroups &#8211; this is why we need PTB2. You can now add a class level definition to a PropertyGroup definition
  3. PropertySecurity Attributes can now be applied to Tab class definitions &#8211; providing a shortcut to more granular management of property security.
  4. The ApplyToDefaultProperties functionality has been removed &#8211; this created some problems with the required validation settings &#8211; meaning it was possible to publish phantom pages with incomplete information.

The following hierarchical rules apply to attributes:

  * A property level [Authorize] attribute in a PropertyGroup will override any other setting
  * A property level [Authorize] attribute not on a PropertyGroup in a TypedPageData classÂ will tabs or class level settings
  * An [Authorize] attribute on a tab will be honoured next and take preference over a class level setting
  * A class level [Authorize] attribute on a tab will take preference only if the above aren&#8217;t matched

<p title="EPiServer Nuget">
  An updated Nuget package (FortuneCookie.PropertySecurity) built against .Net 4.0 / EPiServer 6R2 and PTB 2.0 is available <a title="EPiServer Nuget" href="http://nuget.episerver.com/" target="_blank">EPiServer Nuget feed</a>. The source code is available on <a title="FortuneCookie.PropertySecurity source code at GitHub" href="https://github.com/markeverard/FortuneCookie.PropertySecurity" target="_blank">GitHub â€“ https://github.com/markeverard/FortuneCookie.PropertySecurity</a>
</p>
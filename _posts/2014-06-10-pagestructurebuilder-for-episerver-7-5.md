---
id: 4022
title: PageStructureBuilder for EPiServer 7.5
date: 2014-06-10T10:00:45+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/?p=4022
permalink: /2014/06/10/pagestructurebuilder-for-episerver-7-5/
dsq_thread_id:
  - "2750949352"
  - "2750949352"
categories:
  - Episerver
---
If there was an award for the best EPiServer extension produced by a member of the World community; that didn&#8217;t gain the recognition it deserved. Of course, my own <a title="EPiServer Personalization Engine" href="http://www.markeverard.com/2011/05/13/episerver-personalization-engine/" target="_blank">Personalisation Engine</a> would win hands down 😉

Coming in a very close second would be one of Joel Abrahamsson&#8217;s masterpieces. This one lesser known than <a title="Page Type Builder is an open source extension for EPiServer CMS version 5 and 6" href="http://joelabrahamsson.com/tag/page-type-builder/" target="_blank">PageTypeBuilder</a>, <a title="Truffler has been renamed to EPiServer Find" href="http://joelabrahamsson.com/introducing-truffler-advanced-search-made-easy/" target="_blank">Truffler</a> or <a title="PowerSlice - List and create content easily" href="http://joelabrahamsson.com/powerslice/" target="_blank">PowerSlice</a>, but one that solves a common CMS challenge equally as elegantly.

<a title="Automatically organize EPiServer pages " href="http://joelabrahamsson.com/automatically-organize-episerver-pages/" target="_blank">PageStructureBuilder</a> was a solution Joel put together to automatically organise EPiServer pages, meaning you could define areas of your site that would ensure its content was arranged into a predefined structure. The obviously use-case here being &#8216;news&#8217; type pages that would be stored in a date hierarchy (Year > Month > Day > News Article).

The solution was originally built against CMS6 and PageTypeBuilder, so I&#8217;ve forked Joel&#8217;s original solution and upgraded it to work against EPiServer 7.5 and the native strongly typed page implementation.

Available on EPiServer <a title="PageStructureBuilder on the EPiServer Nuget feed" href="http://nuget.episerver.com/en/OtherPages/Package/?packageId=PageStructureBuilder" target="_blank">Nuget</a> now.

&nbsp;
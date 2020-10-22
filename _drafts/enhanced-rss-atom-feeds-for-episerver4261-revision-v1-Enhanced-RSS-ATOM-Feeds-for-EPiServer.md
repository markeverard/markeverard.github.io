---
id: 4271
title: Enhanced RSS / ATOM Feeds for EPiServer
date: 2016-08-06T14:56:11+00:00
author: Mark Everard
layout: revision
guid: http://markeverardcom.azurewebsites.net/?p=4271
permalink: /2016/08/06/4261-revision-v1/
---
I&#8217;ve released a new version of CHIEF2MORO.SyndicationFeeds. Along with upgrading to EPiServer 9, I&#8217;ve also taken the opportunity to add a few additional features based on some feedback and my own real usage.

### Advanced Filtering

The library now contains the IFeedContentFilterer interface which provides an extension for any custom filtering you may want to achieve (for example &#8211; removing items with empty description&#8217;s). The default implementation filters via the EPiServer FilterForVisitor filters (Published status, Access rights, Has a template), though ignores the HasTemplate rule for blocks, so they can still be exposed. It also filters on EPiServer categories and ContentType, both of which are selectable by an editor on an instance of the Feed PageType.

### Changing each Item&#8217;s summary in the feed.

You can now provide your own implementation of IFeedDescriptionProvider which is an interface that describes how a content item&#8217;s feed summary / description is derived. This allows you to provide your own implementation of where the items summary is stored. This may for example be from a common page property across all content types, or may be specific to each content type. By default, each item has a summary like ‘An src link to content with id = {content.ContentLink.ID} and name = {content.Name}’. The IFeedDescriptionProvider extension replaces the previous SetItemDescription delegate way of overriding the summary / description. The delegate is still available to maintain backwards compatibility but is marked as obsolete.

### It’s on Nuget

The source code is available at <a title="Chief2moro.SyndicationFeeds by markeverard on GitHub" href="https://github.com/markeverard/Chief2moro.SyndicationFeeds" target="_blank">https://github.com/markeverard/Chief2moro.SyndicationFeeds</a>. I am happy to receive feedback and pull requests though I need to make a formal and public apology to [Thomas Svensen](https://github.com/thomassvensen) who did indeed send me a pull request, which I ignored like an extremely bad open source steward&#8230;.. Sorry<img src="http://www.markeverard.com/wp-includes/images/smilies/frownie.png" alt=":(" class="wp-smiley" style="height: 1em; max-height: 1em;" /> 

A package (currently v2.0.0.0) is available in the EPiServer Nuget Feed – [http://nuget.episerver.com/](http://nuget.episerver.com/en/?search=possible&sort=MostDownloads&page=1&pageSize=10) – search for CHIEF2MORO.SyndicationFeeds

&nbsp;

&nbsp;
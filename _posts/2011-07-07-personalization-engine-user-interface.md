---
id: 514
title: 'Personalization Engine &#8211; User Interface'
date: 2011-07-07T16:58:29+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1085474239"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Personalization]
---
One of the things I wanted to provide with the <a title="Personalization Engine for EPiServer" href="http://personalization.codeplex.com/" target="_blank">PersonalizationEngine framework </a>was a series of simple User Interface elements , which would provide an easy way to demonstrate the Personalization Engine within front-end templates, and also provide developers with some simple examples of how to use the API.

<a title="Lee Crowe" href="http://world.episerver.com/System/Users-and-profiles/Community-Profile-Card/Croweman/" target="_blank">Lee Crowe</a> has stepped up to the mark and contributed these for me &#8211; Thanks Lee

These User Interface elements are contained in a separate NuGet package: FortuneCookie.PersonalizationEngine.UI (dependent on FortuneCookie.PersonalizationEngine) ,which is available to download the [EPiServer NuGet feed](http://nuget.episerver.com "EPiServer Nuget feed").

The packages contains:

1) A custom property, (using <a title="Mulitple Selection Custom Property" href="http://world.episerver.com/Blogs/Lee-Crowe/Dates/2011/5/Multiple-Selection-Custom-Property-Base-Control/" target="_blank">Lee&#8217;s  multiple selection custom property</a>)  &#8211; which allow you to select a limited set of defined Personalization Engine content providers from which to return content. This allows you to have PersonalizationEngine rules defined on a page-by-page basis.

2) Two dynamic content controls (using the <a title="Dynamic Content Plugin Attribute" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-6-R2/Dynamic-Content/" target="_blank">new DynamicContentPlugin attribute</a>) &#8211; one which just displays PersonalizationEngine results from the full rule-set, and one which uses the custom property described above to allow a limited sub-set.

### FortuneCookie.PersonalizationEngine 1.1

Additionally there is a new version of the core FortuneCookie.PersonalizationEngine, which contains some minor updates and enhancements

1) Added a new event that you can hook into to filter any results prior to them being returned from the PersonalizatioEngine. A good usage for this may be to filter by PageType definition in the case you don&#8217;t want your PersonalizationEngine results to contain a particular PageType

The example below will filter all Article pages from any content returned from a PagesWithPageNameContentProvider. The event is hooked on in Global.asax

~~~csharp
protected void Application_Start(Object sender, EventArgs e)
{
 PersonalizationEngine.OnContentProviderGetContent += PersonalizationEngine_OnContentProviderGetContent;
}

 private void PersonalizationEngine_OnContentProviderGetContent(ContentProviderEventArgs e)
 {
     if (e.ContentProviderType == typeof(PagesWithPageNameContentProvider))
          e.ContentProviderPages = e.ContentProviderPages.Where(p =&gt; p.PageTypeID != PageTypeResolver.Instance.GetPageTypeID(typeof(ArticlePageType)));
 }
~~~

2) Changed the call to DataFactory.Instance.GetPages, to an iterated call to DataFactory.Instance.GetPage in the CachedContentProviderBase class. Although the latter method is less performant &#8211; there is an [acknowledged bug](http://joelabrahamsson.com/entry/page-type-builder-131-released-some-fixes-for-r2#comments "DataFactory.GetPages bug") in CMS6R2 which means that PageTypeBuilder does not hook into the GetPages method result meaning that returned results are unTyped.

Both of these packages are now available in the <a title="EPiServer NuGet Feed" href="http://nuget.episerver.com" target="_blank">EPiServer Nuget Feed</a>
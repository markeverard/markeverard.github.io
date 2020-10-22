---
id: 501
title: 'Personalization Engine &#8211; Creating an IContentProvider'
date: 2011-05-17T12:00:28+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=501
permalink: /2011/05/17/personalization-engine-creating-an-icontentprovider/
dsq_thread_id:
  - "1078737652"
  - "1078737652"
categories:
  - Episerver
---
Last week I blogged about the <a title="EPiServer Personalization Engine" href="http://www.markeverard.com/blog/2011/05/13/episerver-personalization-engine/" target="_blank">Fortune Cookie Personalization Engine for EPiServer (CMS6 R2)</a>, which I&#8217;ve released as an open source project on <a title="Personalization Engine on Codeplex" href="http://personalization.codeplex.com" target="_blank">codeplex</a> and it&#8217;s also available on the <a title="EPiServer Nuget feed" href="http://nuget.episerver.com" target="_blank">EPiServer Nuget feed</a>. The overriding idea of the PersonalizationEngine is to provide a more automated way of serving personalized content, by matching up a Visitor Group with a means of providing EPiServer content (PageData) that is relevant to that group. This time I&#8217;m going to run through how as a developer you can create your own ContentProviders for use with the PersonalizationEngine.

### Example &#8211; building a StartPageChildrenContentProvider

This example content provider will provide a list of all child pages that live directly underneath the defined EPiServer start page (not a particularly useful personalized content provider &#8211; but a good example nontheless.)

  1. Create your class and implement the IContentProvider members. The only method we have to provide an implementation for is the GetContent method which returns a collection of PageData objects. <pre class="brush: csharp; title: ; notranslate" title="">public class StartPageChildrenContentProvider : IContentProvider
{
    public IEnumerable GetContent(string languageBranch)
    {
        throw new NotImplementedException();
    }

    public Guid VisitorGroupId { get; set; }
    public string ContentCriteria { get; set; }
}
</pre>

  2. Implement the public IEnumerable<PageData> GetContent(string languageBranch) method, in our case this uses the DataFactory API to return the Child pages of the StartPage. <pre class="brush: csharp; title: ; notranslate" title="">public IEnumerable GetContent(string languageBranch)
{
    return DataFactory.Instance.GetChildren(PageReference.StartPage, new LanguageSelector(languageBranch));
}
</pre>
    
    The above is obviously a simple implementation and doesn&#8217;t take into account any page access rights. If you wish the ContentProvider to reflect these then you should add them into the GetContent() method.</li> 
    
      * Mark the class up with a VisitorGroupContentProvider attribute. This attribute allows three arguments 
          * **DisplayName** &#8211; a simple description of the ContentProvider
          * **Description** &#8211; a description of what the ContentProvider does
          * **CriteriaEditModel** &#8211; the strongly typed model used to allow editor&#8217;s to input criteria for use by the ContentProvider &#8211; these must inherit from ICriteriaModel. Our simple case doesn&#8217;t need one and so we can either leave this attribute blank, or specify the DefaultCriteriaModel &#8211; I&#8217;ll blog more about CriteriaEditModels later on
        <pre class="brush: csharp; title: ; notranslate" title="">[VisitorGroupContentProvider(DisplayName = "StartPage children content provider",
Description = "Returns the immediate child pages the live underneath the website Start Page ",
CriteriaEditModel = typeof(DefaultCriteriaModel))]
public class StartPageChildrenContentProvider : IContentProvider
{
    ...
}
</pre>
        
        Once you have marked the attribute, the Personalization Engine will pick up your ContentProvider on startup and it will be available for Editors to use.</li> </ol> 
        
        ### Performance
        
        ContentProviders will generally use the underlying EPiServer API to return PageData objects. Although some calls such as GetChildren used in the above example will already hook into the EPiServer Caching framework and return cached objects, there are some other API calls that will directly hit the database (such as FindPagesWithCriteria). In these cases we must be more careful of performance, as the PersonalizationEngine could perform many GetContent calls on a single page.
        
        In these cases, instead of your ContentProvider directly implementing IContentProvider it should instead inherit from the CachedContentProviderBase class. This abstract class itself implements IContentProvider &#8211; but also provides one abstract method to override.
        
        <pre class="brush: csharp; title: ; notranslate" title="">protected override IEnumerable GetContentReferences(string languageBranch)

</pre>
        
        It is EPiServer best practise to **NEVER** directly cache PageData objects and to instead cache their corresponding PageReferences (see <a title="How EPiServer caches PageData objects" href="http://joelabrahamsson.com/entry/how-episerver-cms-caches-pagedata-objects for more information" target="_blank">http://joelabrahamsson.com/entry/how-episerver-cms-caches-pagedata-objects for more information</a>). The CachedContentProviderBase class will handle caching the PageReferences returned from this method and preferentially use that cache when returning any calls to GetContent(). The cache is set as a sliding cache of 20 minutes.
        
        Our implementation would now look like
        
        <pre class="brush: csharp; title: ; notranslate" title="">public class StartPageChildrenContentProvider : CachedContentProviderBase
{
    protected override IEnumerable GetContentReferences(string languageBranch)
    {
        var pages = DataFactory.Instance.GetChildren(PageReference.StartPage, new LanguageSelector(languageBranch));
        return pages.Select(p =&gt;; p.PageLink);
    }
}
</pre>
        
        and that&#8217;s all there is to it&#8230;&#8230;&#8230; 🙂
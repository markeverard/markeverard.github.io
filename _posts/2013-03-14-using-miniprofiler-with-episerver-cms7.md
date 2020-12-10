---
id: 1092
title: Using MiniProfiler with EPiServer CMS7
date: 2013-03-14T12:01:18+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/?p=1092
permalink: /2013/03/14/using-miniprofiler-with-episerver-cms7/
dsq_thread_id:
  - "1136699406"
  - "1136699406"
categories:
  - Episerver
---
<a title="MiniProfiler - a simple but effective mini-profiler for .NET and Ruby." href="http://miniprofiler.com/" target="_blank">MiniProfiler</a> is a great tool for allowing in-situ profiling to be performed directly on a production site; by your development team whilst hiding any detailed specific information from a standard end user. It was developed by <a title=" Sam Saffron  - Programming, Technology and the Art of Hacking" href="http://samsaffron.com/" target="_blank">Sam Saffron</a> who built it (and then open sourced it) to solve real-world profiling issues on <a title="Stack Overflow" href="http://stackoverflow.com/" target="_blank">StackOverflow</a>.

It&#8217;s been around for a while and has already been <a title="EPiServer and MiniProfiler" href="http://blog.gregbrant.com/post/EPiServer-and-MvcMiniProfiler-A-first-look.aspx" target="_blank">integrated with EPiServer 6</a> based sites. However it really comes into its own when you can hook it into application events that contain performance information that you really care about. MiniProfiler already comes with some integration good hooks into the MVC framework allowing you to profile each of your controller action methods, and also some useful <a title="Using MiniProfiler with EntityFramework" href="http://benjii.me/2011/07/using-the-mvc-mini-profiler-with-entity-framework/" target="_blank">hooks into EntityFramework</a> which allow to to see and time any database calls that EF makes for you.

The underlying API improvements in EPiServer CMS 7 allow us to easily integrate the new shinier ASP.NET tools and gain some real value from them. In the case of MiniProfiler &#8211; we can use the new <a title="EPiServer IContentRepository" href="http://world.episerver.com/Blogs/Johan-Bjornfot/Dates1/2012/8/EPiServer7-Working-with-IContentRepositoryDataFactory/" target="_blank">EPiServer IContentRepository interface</a> (whose usage replaces the DataFactory singleton) to build a concrete implementation which wraps the standard implementation to provide hooks into MiniProfiler. This means we can easily see what data related calls our EPiServer templates make on screen as the page is delivered. This is a great tool for assessing page performance and cache usage.

### POSSIBLE.ProfiledContentRepository

I&#8217;ve released a Nuget package that contains 95% of the code you need to see meaningful MiniProfiler output on your CMS7 site.

  * ProfiledContentRepository &#8211; an IContentRepository implementation contains MiniProfiler steps. An example of the GetChildren<T> IContentRepository method is shown below.

<pre class="brush: csharp; title: ; notranslate" title="">public IEnumerable&lt;T&gt; GetChildren&lt;T&gt;(ContentReference contentLink) where T : IContentData
{
    string stepName = string.Format("GetChildren&lt;{0}&gt;[{1}]", typeof(T).Name, contentLink);

    using (MiniProfiler.Current.Step(stepName))
    {
        return DataFactory.Instance.GetChildren&lt;T&gt;(contentLink);
    }
}
</pre>

  * Container initialisation &#8211; the ProfiledContentRepository instance is injected into the <a title="EPiServer 7 IoC setup" href="http://www.popkram.com/blog/2012/06/episerver-7-preview-container-support/" target="_blank">EPiServer IoC container via an Initialisation module</a> and then used as the default implementation for all content retrieval
  * Global application event hooks &#8211; MiniProfiler is started and stopped on each Http Request by registering against global application begin and end requests events (performed via the init method of an HttpModule).
  * Overridable display logic &#8211; You can choose any arbitrary logic to decide how and whether to show the MiniProfiler output to a site visitor. Usually you&#8217;d do this via the incoming request IP address or by checking for the presence of a specific Http header. By default the Nuget package will show the output to any Http request from an authenticated user matching the WebAdmin / Administrator roles. This behaviour can be modified by amending theÂ DisplayProfilerHandler.ShouldShow and the DisplayProfileHandler.ShowToUser delegates which you&#8217;d likely do on application start up or in an initialisation module. As an example &#8211; the following will set the module to only display to me (username mark.everard) when I&#8217;m visiting from a specific IP address.

<pre class="brush: csharp; title: ; notranslate" title="">DisplayProfilerHandler.ShouldStart= httpContext =&gt; httpContext.Request.ServerVariables["REMOTE_ADDR"].Equals("217.114.90.249");

DisplayProfilerHandler.ShowToUser= httpContext =&gt; httpContext.User.Identity.Name == "mark.everard;

</pre>

Ideally you want to start profiling early and only for users that will see the end information. This is the reason for the multiple delegates where you can set your behaviour as to whether to start profiling (which happens on the global BeginRequest event &#8211; that occurs before ASP.NET has worked out whether the request is from an authenticated user) and also on the PostAuthorizeRequest method that works out whether to continue profiling and can contain user specific logic. This is mentioned in more detail in <a title="user authentication and MiniProfiler" href="http://stackoverflow.com/questions/7234220/can-i-start-the-mvc-mini-profiler-in-the-postauthorizerequest-method" target="_blank">this Stack Overflow</a> post.

**For optimum performance when using this module in production you should override the default DisplayProfilerHandler.ShouldStart delegate. By default every request is profiled, and then the results thrown away when we can calculate whether the user is logged into EPiServer.**

### Installation

To use this &#8211; install the POSSIBLE.ProfiledContentRepository package from the EPiServer Nuget feed and **remember that you need to add the following line to your MasterPage / Layout file** just before the closing body tag. It is this call that actually outputs the profiling information.

<pre class="brush: csharp; title: ; notranslate" title="">@MiniProfiler.RenderIncludes();

</pre>

<img class=" wp-image-1162 aligncenter" alt="miniprofiler-example" src="http://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example.png" width="694" height="349" srcset="https://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example.png 991w, https://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example-300x151.png 300w, https://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example-768x386.png 768w, https://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example-720x362.png 720w, https://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example-580x291.png 580w, https://www.markeverard.com/wp-content/uploads/2013/03/miniprofiler-example-320x161.png 320w" sizes="(max-width: 694px) 100vw, 694px" /> 

Happy Profiling!!
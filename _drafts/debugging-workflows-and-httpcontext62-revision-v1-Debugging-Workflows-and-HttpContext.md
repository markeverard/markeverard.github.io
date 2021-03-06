---
id: 4951
title: Debugging Workflows and HttpContext
date: 2017-07-11T16:33:00+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/62-revision-v1/
permalink: /2017/07/11/62-revision-v1/
---
I stumbled across one of those &#8216;frustrating&#8217; bugs last week whilst setting up some simple chained <a href="http://world.episerver.com/Blogs/Joe-Bianco/Dates/2009/10/Enabling-the-EPiServer-CMS-Workflows/" target="_blank">out-of-the-box EPiServer workflows</a>.

The EPiServer UI reported that the workflow instance has successfully started, but no tasks or notifications were ever received and an immediate search for any running workflow instances reported that there were none in progress.

I wasn&#8217;t really sure where to start looking. I fired up SQL Profiler and could see that there were some workflow queries being passed, but no data was stored at the end of the request so something, somewhere along the line, was causing the transaction to be rolled back.

### Debugging Workflows

The workflow module in EPiServer is built upon the .NET 3.0 workflow functionality and allows custom workflows to be developed which handle typical EPiServer events.

Workflows are managed under their own runtime, which itself is hosted by a .NET application &#8211; in this case EPiServer. The runtime is configured in the web.config file, along with the persistence mechanism (SQL Server).

<pre class="brush: xml; title: ; notranslate" title="">&lt;workflowRuntime EnablePerformanceCounters=&quot;false&quot;&gt;
    &lt;Services&gt;
        &lt;add type=&quot;System.Workflow.Runtime.Hosting.DefaultWorkflowSchedulerService, System.Workflow.Runtime, Version=3.0.00000.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35&quot; maxSimultaneousWorkflows=&quot;5&quot; /&gt;
        &lt;add type=&quot;System.Workflow.Runtime.Hosting.SqlWorkflowPersistenceService, System.Workflow.Runtime, Version=3.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35&quot; UnloadOnIdle=&quot;true&quot; /&gt;
    &lt;/Services&gt;
&lt;/workflowRuntime&gt;
</pre>

Debugging can be enable by adding in and adjusting the following keys (which are commented out towards the bottom of the EPiServer web.config file).

<pre class="brush: xml; title: ; notranslate" title="">&lt;!--OPTIONAL Workflow Diagnostics - used for logging useful information for debugging purposes--&gt;
  &lt;system.diagnostics&gt;
    &lt;switches&gt;
      &lt;add name=&quot;System.Workflow.Runtime&quot; value=&quot;Off&quot; /&gt;
      &lt;add name=&quot;System.Workflow.Runtime.Hosting&quot; value=&quot;Off&quot; /&gt;
      &lt;add name=&quot;System.Workflow.Runtime.Tracking&quot; value=&quot;Off&quot; /&gt;
      &lt;add name=&quot;System.Workflow.Activities&quot; value=&quot;Off&quot; /&gt;
      &lt;add name=&quot;System.Workflow.Activities.Rules&quot; value=&quot;Off&quot; /&gt;
      &lt;add name=&quot;System.Workflow LogToTraceListeners&quot; value=&quot;1&quot; /&gt;
      &lt;add name=&quot;System.Workflow LogToFile&quot; value=&quot;0&quot; /&gt;
    &lt;/switches&gt;
  &lt;/system.diagnostics&gt;
</pre>

There are two choices of where to log any workflows messages, to file or to a TraceListener (aka the output window in Visual Studio). Full details of the configuration settings can be on found on <a href="http://msdn.microsoft.com/en-us/library/ms732240.aspx" target="_blank">MSDN</a>.

### HttpContext?

Setting up the workflow logging &#8211; quickly highlighted my issue. One of the methods in the workflow was throwing an exception which the EPiServer UI wasn&#8217;t reporting. The exact stack trace pointed to the application&#8217;s custom ProfileProvider which was explicitly trying to store items in the HttpContext.Current.Cache.

Remember that workflows are executed using their own runtime, which means they operate on a thread outside of the current web request. So trying to resolve the HttpContext will always return null. Problem identified!

### In summary:

  1. Debugging workflows can be enabled in web.config.
  2. Workflows operate under their own runtime.
  3. Never assume an HttpContext &#8211; be careful when rolling your own providers. Certain EPiServer services (<a href="http://world.episerver.com/Templates/Forum/Pages/Thread.aspx?epslanguage=en&id=37635" target="_blank">scheduled jobs</a>, workflows operate on a background thread).
  4. The EPiServer UI does not always tell you the full story&#8230;.
Happy workflowing!</ol>
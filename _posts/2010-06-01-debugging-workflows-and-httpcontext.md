---
title: Debugging Workflows and HttpContext
date: 2010-06-01T15:48:37+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1078927595"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Workflows]
---
I stumbled across one of those &#8216;frustrating&#8217; bugs last week whilst setting up some simple chained <a href="http://world.episerver.com/Blogs/Joe-Bianco/Dates/2009/10/Enabling-the-EPiServer-CMS-Workflows/" target="_blank">out-of-the-box EPiServer workflows</a>.

The EPiServer UI reported that the workflow instance has successfully started, but no tasks or notifications were ever received and an immediate search for any running workflow instances reported that there were none in progress.

I wasn&#8217;t really sure where to start looking. I fired up SQL Profiler and could see that there were some workflow queries being passed, but no data was stored at the end of the request so something, somewhere along the line, was causing the transaction to be rolled back.

### Debugging Workflows

The workflow module in EPiServer is built upon the .NET 3.0 workflow functionality and allows custom workflows to be developed which handle typical EPiServer events.

Workflows are managed under their own runtime, which itself is hosted by a .NET application &#8211; in this case EPiServer. The runtime is configured in the web.config file, along with the persistence mechanism (SQL Server).

~~~xml
<workflowRuntime EnablePerformanceCounters="false">
    <Services>
        <add type="System.Workflow.Runtime.Hosting.DefaultWorkflowSchedulerService, System.Workflow.Runtime, Version=3.0.00000.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" maxSimultaneousWorkflows="5" />
        <add type="System.Workflow.Runtime.Hosting.SqlWorkflowPersistenceService, System.Workflow.Runtime, Version=3.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" UnloadOnIdle="true" />
    </Services>
</workflowRuntime>
~~~

Debugging can be enable by adding in and adjusting the following keys (which are commented out towards the bottom of the EPiServer web.config file).

~~~xml
<!--OPTIONAL Workflow Diagnostics - used for logging useful information for debugging purposes-->
  <system.diagnostics>
    <switches>
      <add name="System.Workflow.Runtime" value="Off" />
      <add name="System.Workflow.Runtime.Hosting" value="Off" />
      <add name="System.Workflow.Runtime.Tracking" value="Off" />
      <add name="System.Workflow.Activities" value="Off" />
      <add name="System.Workflow.Activities.Rules" value="Off" />
      <add name="System.Workflow LogToTraceListeners" value="1" />
      <add name="System.Workflow LogToFile" value="0" />
    </switches>
  </system.diagnostics>
~~~

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
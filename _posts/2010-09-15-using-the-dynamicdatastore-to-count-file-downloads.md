---
id: 174
title: Using the DynamicDataStore to count file downloads
date: 2010-09-15T20:06:47+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=174
permalink: /2010/09/15/using-the-dynamicdatastore-to-count-file-downloads/
dsq_thread_id:
  - "1073095662"
  - "1073095662"
  - "1073095662"
categories:
  - Episerver
---
Increasingly CMS builds are required to deal with more than just standard page-based data and deliver an additional level of content metadata, such as the number of page views or the number of times a file has been downloaded.

The DynamicDataStore (introduced in EPiServer CMS6) provides developers with a convenient and easy-to-use persistence mechanism which is well suited to this type of content metadata.

One of the more simple requirements is to track the number of times a document is downloaded. The best place to handle this using ASP.NET is to create your own HttpHandler which will be responsible for serving the file and also updating the download count. This is better than any page based / codebehind methods as you can be sure that however the document is accessed (even via direct url) you&#8217;ll record the count. Remeber though, actually verifying that the file has been downloaded sucessfully is a whole different ball game &#8211; the count will give you just the number of times the file has been requested.

Creating a HttpHandler is simply a matter of creating a class that implements the IHttpHandler interface. However as we don&#8217;t want to alter any of the underlying behaviour for serving a file, but just supplement a call to the DDS. We&#8217;ll just inherit the existing EPiServer StaticFileHandler and override the ProcessRequestInternal and IsReusable methods.

<pre class="brush: csharp; title: ; notranslate" title="">using System.Web;
using DownloadCount.Persistence;

namespace DownloadCount
{
   public class DownloadCountHttpHandler : EPiServer.Web.StaticFileHandler
   {
       public DownloadCountHttpHandler()
       {
          ViewCountStore = new DdsDownloadCountStore();
       }

       protected IDownloadCountStore ViewCountStore { get; set; }

       protected override bool ProcessRequestInternal(HttpContext context)
       {
          var permanentPath = PermanentLinkUtility.MapRequestPathToPermanentLink(context.Request.FilePath);
          ViewCountStore.UpdateDownloadCount(permanentPath);
          return base.ProcessRequestInternal(context);
       }

       public override bool IsReusable
       {
          get { return true; }
       }
    }
}
</pre>

There are some EPiServer specifics to take into account as we&#8217;re dealing with files stored in the VPP using the VirtualPathVersioningProvider. We need a unique handle to each file to accurately record the download path so we&#8217;ll map the request path to an internal EPiServer permanent link ID. This ensures that the count will be maintained should the file be renamed through the File Manager interface. I&#8217;ve put together a simple utility class with a static method, that attempts to checks whether the requested path is a UnifiedFile (aka lives withing the VPP). If so, it returns the <a title="EPiServer permanent links" href="http://sdk.episerver.com/library/CMS6/Developers%20Guide/Advanced%20Features/UrlRewrite/Permanent%20Links.htm" target="_blank">PermanentLinkVirtualPath</a>.

<pre class="brush: csharp; title: ; notranslate" title="">using System.Web.Hosting;
using EPiServer.Web.Hosting;

namespace DownloadCount
{
   public static class PermanentLinkUtility
   {
       public static string MapRequestPathToPermanentLink(string filePath)
       {
          var file = HostingEnvironment.VirtualPathProvider.GetFile(filePath) as UnifiedFile;
          if (file == null) return filePath;
          return string.IsNullOrEmpty(file.PermanentLinkVirtualPath)
                   ? filePath
                   : file.PermanentLinkVirtualPath;
       }
   }
}
</pre>

The data we need to store is represented by the following object. The class only has two properties, an int to hold the total number of downloads and a string to represent the unique file path to the file. You&#8217;ll notice that the FilePath property is marked with the <a title="EPiServerDataIndex attribute" href="http://world.episerver.com/Blogs/Paul-Smith/Dates1/2009/11/Changes-in-Dynamic-Data-Store/" target="_blank">[EPiServerDataIndex] </a>attribute, which tells the DDS to map the property to an indexed database column. We&#8217;ll use this index to perform lookups when retrieving data from the store.

<pre class="brush: csharp; title: ; notranslate" title="">using EPiServer.Data.Dynamic;

namespace DownloadCount.Persistence
{
   public class FileDownloadCount
   {
      [EPiServerDataIndex]
      public string FilePath { get; set; }
      public int Count { get; set; }
   }
}
</pre>

Below is the service layer that is responsible for dealing with the DDS (following the <a title="Using a DynamicDataStore instance correctly" href="http://world.episerver.com/Blogs/Paul-Smith/Dates1/2010/1/Using-a-DynamicDataStore-instance-correctly/" target="_blank">recommended usage pattern</a>) . It implements the two members of the IDownloadCount interface, which provide means of accessing and updating the download count information.elpful [EDIT &#8211; updated as per Paul Smith&#8217;s helpful comment below] 

<pre class="brush: csharp; title: ; notranslate" title="">using System.Web;
using EPiServer.Data.Dynamic;
using System.Linq;

namespace DownloadCount.Persistence
{
   public class DdsDownloadCountStore : IDownloadCountStore
   {
      protected DynamicDataStore GetDynamicDataStore()
      {
         return DynamicDataStoreFactory.Instance.GetStore(typeof(FileDownloadCount)) ??
               DynamicDataStoreFactory.Instance.CreateStore(typeof(FileDownloadCount));
      }

      public void UpdateDownloadCount(string path)
      {
         path = UrlDecodePath(path);
         using(var store = GetDynamicDataStore())
         {
            var fileCount = store.Find&lt;FileDownloadCount&gt;("FilePath", path).FirstOrDefault()
                            ?? FileDownloadCountFactory.Create(path);

            fileCount.Count++;
            store.Save(fileCount);
         }
      }

      private static string UrlDecodePath(string path)
      {
         return HttpUtility.UrlDecode(path);
      }

      public int GetDownloadCount(string path)
      {
         path = UrlDecodePath(path);
         using (var store = GetDynamicDataStore())
         {
           var fileCount = store.Find&lt;FileDownloadCount&gt;("FilePath", path).FirstOrDefault();
           return fileCount == null ? 0 : fileCount.Count;
         }
      }
   }
}
</pre>

Finally, to use this handler we need to register it in the web.config file. Here I&#8217;m going to record counts for just .pdf and .doc files that are stored in the Globals VPP folder. You need to add one instance of the handler for each type of file you want to track. Don&#8217;t forget to chain the handlers appropriately (ie the existing wildcard handler should always be defined last).

<pre class="brush: xml; title: ; notranslate" title="">&lt;location path="Global"&gt;
 &lt;system.webServer&gt;
  &lt;handlers&gt;
   &lt;add name="webresources" path="WebResource.axd" verb="GET" type="System.Web.Handlers.AssemblyResourceLoader"/&gt;
   &lt;add name="countdocs" path="*.doc" verb="*" type="DownloadCount.DownloadCountHttpHandler, DownloadCount"/&gt;
   &lt;add name="countpdfs" path="*.pdf" verb="*" type="DownloadCount.DownloadCountHttpHandler, DownloadCount"/&gt;
   &lt;add name="wildcard" path="*" verb="*" type="EPiServer.Web.StaticFileHandler, EPiServer"/&gt;
  &lt;/handlers&gt;
 &lt;/system.webServer&gt;
 &lt;staticFile expirationTime="-1.0:0:0"/&gt;
 &lt;/location&gt;

</pre>

Accessing the latest count on page is pretty simple, again using the MapRequestPathToPermanentLink method defined earlier.

<pre class="brush: csharp; title: ; notranslate" title="">var store = new DdsDownloadCountStore();
var permanentPath = PermanentLinkUtility.MapRequestPathToPermanentLink(FilePath);
DownLoadCount.Text = string.Format("Downloaded {0} times", store.GetDownloadCount(permanentPath));

</pre>

Full source code is available zipped up <a title="DownloadCount source code" href="/assets/uploads/2010/09/FortuneCookie.DownloadCount.zip" target="_self">here</a>, or in the EPiServer World code section.
﻿---
title: WordPress XML-RPC ASP.NET Client
date: 2013-09-02T11:00:49+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2013/09/02/wordpress-xml-rpc-asp-net-client/
dsq_thread_id:
  - "1688439127"
  - "1688439127"
categories:
  - Technology
---
**UPDATED &#8211; 7th June 2014 &#8211; Additional methods added to client, listed below.**

WordPress offers an <a title="WordPress XML-RPC Support" href="http://codex.wordpress.org/XML-RPC_Support" target="_blank">XML-RPC</a> (AKA SOAP for PhP) API for content retrieval and update operations.

I was pretty surprised to find, or actually not find an ASP.NET client for a WordPress feed. Surely somebody must have faced a scenario of needing to export / migrate WordPress content into a .NET app? Apparently not, or if they have they haven&#8217;t shared any code 🙁

So I&#8217;ve started putting one together and put the code on <a title="POSSIBLE.WordPress.XmlRpcClient source code on GitHub" href="https://github.com/markeverard/POSSIBLE.WordPress.XmlRpcClient" target="_blank">GitHub</a> and <a title="POSSIBLE.WordPress.XmlRpcClient on NuGet" href="http://www.nuget.org/packages/POSSIBLE.WordPress.XmlRpcClient/" target="_blank">NuGet</a>. It uses the excellent <a title="A client and server XML-RPC library for .Net available on NuGet" href="http://www.nuget.org/packages/xmlrpcnet/" target="_blank">XML-RPC</a> library for .NET by <a title="Cook Computing" href="http://www.cookcomputing.com/blog/" target="_blank">Charles Cook</a>.

So far I&#8217;ve focused only on the read operations, as this suited my original goal of content export. Expanding to cover all of the available methods, including write and delete operations will be straightforward. I&#8217;m more than happy to take pull requests 🙂

## Usage
The WordPress XML-RPC feed by default lives in the root of your WordPress application (/xmlrpc.php), and so at a basic level all you need to provide to download content is a WordPress username / password and the domain that your WordPress instance lives on.

~~~csharp
const string baseUrl = "http://www.wordpress.com/";
const string username = "xxx";
const string password = "xxx";

using (var client = new WordPressXmlRpcClient(baseUrl, username, password))
{
     var postId = 23;
     var post = client.GetPost(postId);
     //etc
}
~~~

## Current API coverage (as of Nuget package 0.5)

The following <a title="XML-RPC WordPress API" href="http://codex.wordpress.org/XML-RPC_WordPress_API" target="_blank">methods</a> are supported.

* wp.getPost
* wp.getPosts
* wp.getMediaItem
* wp.getMediaLibrary
* wp.getTaxonomy
* wp.getTaxonomies
* wp.getTerm
* wp.getTerms
* wp.getUser
* wp.getUsers
* wp.getComment
* wp.getComments
* wp.getCommentCount
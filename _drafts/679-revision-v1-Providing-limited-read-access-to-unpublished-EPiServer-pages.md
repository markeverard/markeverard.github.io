---
id: 5441
title: Providing limited read access to unpublished EPiServer pages
date: 2017-07-11T16:51:00+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/679-revision-v1/
permalink: /2017/07/11/679-revision-v1/
---
EPiServer provides a few options out-of-the-box for a page approval process.

After some discussion at Cookie (and at the request of a client), we thought of a different approach

1. Allow limited (either time or usage) read access to EPiServer pages via an Access Token.  
2. Allow editors the ability to create / delete and view Access 

The approach

Access Tokens  
Put simply it&#8217;s a Guid that can be attached as a querystring variable to a request for an EPiServer page that can be inspected on each request to determine whether that visit is allowed access to the page

An access token is described by the following model.

These are stored using the DDS

Virtual Roles  
Virtual roles

1. Access tokens stored in 

Access tokens emailed to a email address.
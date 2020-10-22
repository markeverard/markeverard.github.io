---
id: 5511
title: 'An extremely useful Powershell command&#8230;..'
date: 2017-07-11T16:57:25+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/532-revision-v1/
permalink: /2017/07/11/532-revision-v1/
---
By default Powershell exceptions and stderr outputs don&#8217;t give you much information beyond the exception name and the position in the script where the exception occurred. What you really want is a full stack trace.

You can get more information by running the following command; immediately after you&#8217;re troubledÂ by a &#8216;troublesome&#8217; exception

> <pre>$error[0]|format-list â€“force</pre>
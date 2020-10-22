---
id: 6035
title: Api
date: 2018-03-28T20:14:24+00:00
author: Mark Everard
layout: revision
guid: https://www.markeverard.com/2018/03/28/6014-revision-v1/
permalink: /2018/03/28/6014-revision-v1/
---
Integration is often the most challenging aspect of any technical build, both from a development and cost perspective.

A successful integration depends on good information transfer, ultimately between your systems but firstly between your development teams.

The below is a list of of things that

  1. Point of Contact

Is there a point of contact for the API? Do you know where to turn to if you need any support whilst developing against or operating with an API. For mature platforms this could be online documentation and a live &#8216;status&#8217; page, for line of business API&#8217;s this might be &#8216;the single tech guy in IT&#8217;, either way you&#8217;ll no doubt need to talk to somebody so make you know who and how.

Functional review

An important point here, but one that can often get overlooked, especially in a creative environment. Can the API&#8217;s support the functionality your application is trying to offer? Somebody should check before the client sign-off any wireframes and certainly before ANY development commences.

Documentation

Is the API documented, either online or in a technical document? Is it recent or is the information out-of-date? The meaning of &#8216;well-documented&#8217; is subjective but in my view it should include at least the following

Protocol / Language

What is the protocol you should use to communicate with the API (HTTP etc) and what is the data format / language you should use to communicate?( SOAP, REST etc)

Endpoints

Where does the API live? Are there separate test and live systems? Are the use cases for each system explained?

Authentication

Is the API protected? What do you need to do to be able to authenticate with the API (Basic Auth, Oauth etc)

Methods

Is each method in the API explained, including any request parameters and expected responses? Are the expectations on data input (validations) described?

Error messages

Does the documentation explain how the API will notify and provide details of any errors, be that in validation of input or internal more fatal errors?

Examples

Are real code examples

Live Sandbox

Is there an interactive sandbox, where you can try out example responses and receive mocked responses. Online API management platfroms / frameworks often offer these directly out of the box (and they

If you have access to all of the ab

Data control
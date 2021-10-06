---
layout: post
status: publish
published: true
title: Return value or 0 if negative
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 343
wordpress_url: http://www.erichumphrey.com/2007/09/return-value-or-0-if-negative/
date: '2007-09-19 13:19:00 -0500'
date_gmt: '2007-09-19 19:19:00 -0500'
categories:
- postgres
- sql
tags: []
comments:
- id: 2
  author: tucek
  author_email: tucek@hellwach.at
  author_url: ''
  date: '2013-02-27 11:44:47 -0600'
  date_gmt: '2013-02-27 17:44:47 -0600'
  content: just what i was looking for. thank you!
---
<p>If you ever run across a situation that requires you to subtract two numbers and return the value if positive or 0 if negative, here are a couple of solutions.</p>
<p>1) Use IF:
<pre>value = x - y;<br />if value < 0<br />    return 0;<br />else return value;<br /></pre>
<p>2) Use UNION:
<pre>select max(value)<br />from (<br />    select x - y as value<br />    union select 0<br />) x</pre>
<p>I like this way since IF requires plpgsql in Postgres whereas UNION can be used with plain sql.</p>

---
layout: post
status: publish
published: true
title: Using .NET's TryParse() in SQL CLR
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 16
wordpress_url: http://www.erichumphrey.com/2010/06/using-nets-tryparse-in-sql-clr/
date: '2010-06-26 11:53:00 -0500'
date_gmt: '2010-06-26 17:53:00 -0500'
categories:
- ".net"
- c#
- clr
- sql
tags: []
comments:
- id: 8
  author: Graham
  author_email: graham.monkman@concertosupport.co.uk
  author_url: ''
  date: '2012-03-02 07:38:37 -0600'
  date_gmt: '2012-03-02 13:38:37 -0600'
  content: "Great easy to follow walk-thru.\n\nWould like just to add that depending
    on the state of the data, it can be worth checking that dtvalue is within the
    max and min of an sqldatetime (otherwise you get an error).\n\n                if
    (dtValue >= SqlDateTime.MinValue &amp;&amp; dtValue <= SqlDateTime.MaxValue) \n
    \                   return (DateTime)dtValue;"
---
<p>In my current position, I deal with a lot of data quality issues. This column should be a date, but we get an integer, string, NULLs, etc. As a rule of our processing, if we cannot convert the value to the appropriate data type, then we use a default value, or choice of values depending on the situation.</p>
<p>SQL Server already has the CAST and CONVERT functions built in. There is a shortcoming of these functions though. If the value cannot be converted, you get an error and your processing stops. For converting values, you have to pass through the table twice. Once to manually convert any known values that cannot convert into something that can or set them to NULL. Then you can run CAST or CONVERT on the values. We wanted to be able to do this in one step and this led us to .NET's TryParse() method.</p>
<p>Each basic data type in .NET has a TryParse() method that attempts to parse the value and returns a boolean if successful. The parsed value is passed back through an output variable. In my case, I don't really care about the boolean return value. I want a value if it was successful, otherwise give me a NULL. This is the syntax for creating the SQL CLR version.
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/_OEOq9aEto20/TCYzZWwVwDI/AAAAAAAAEuw/uTblcEvNgds/s1600/cs_ParseDateTime.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="130" src="http://3.bp.blogspot.com/_OEOq9aEto20/TCYzZWwVwDI/AAAAAAAAEuw/uTblcEvNgds/s400/cs_ParseDateTime.png" width="400" /></a></div>
<p>And the syntax for using it within SQL.
<div class="separator" style="clear: both; text-align: center;"><a href="http://2.bp.blogspot.com/_OEOq9aEto20/TCY1T2420dI/AAAAAAAAEu4/5fs7s-5jtHo/s1600/clr_ParseDateTime.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="217" src="http://2.bp.blogspot.com/_OEOq9aEto20/TCY1T2420dI/AAAAAAAAEu4/5fs7s-5jtHo/s400/clr_ParseDateTime.png" width="400" /></a></div>
<p>Why would I want NULL on failure you might ask. Well, if I get a NULL back, then I can easily check against NULL for success and use functions such as COALESCE to&nbsp;substitute&nbsp;a default value if the parsing failed. A good case for us to use this is when we have blank strings that need to convert into a default date. CONVERT cannot cast the value '' to a datetime and thus throws an error.</p>
<p>Now I can do things like the following to allow processing to complete if CONVERT hits a bad value.
<div class="separator" style="clear: both; text-align: center;"><a href="http://2.bp.blogspot.com/_OEOq9aEto20/TCY7cg2r9yI/AAAAAAAAEvA/niTYUMqD3GM/s1600/sql_ParseDateTime.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="318" src="http://2.bp.blogspot.com/_OEOq9aEto20/TCY7cg2r9yI/AAAAAAAAEvA/niTYUMqD3GM/s400/sql_ParseDateTime.png" width="400" /></a></div>
<p>Now the drawbacks of using this method. If you need to make sure you have all valid values beforehand, <b>this will cause those to fail silently</b>. An alternative would be to use this to check for invalid values. You can do something like this to find problematic values:
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/_OEOq9aEto20/TCY89DUDzII/AAAAAAAAEvI/-4_qQpjXIB0/s1600/sql_ParseDateTime2.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="332" src="http://4.bp.blogspot.com/_OEOq9aEto20/TCY89DUDzII/AAAAAAAAEvI/-4_qQpjXIB0/s400/sql_ParseDateTime2.png" width="400" /></a></div>
<p>This allows you to find all&nbsp;unconvertible&nbsp;values without SQL throwing an error as it would if you had tested with CONVERT(). We mainly use this function in the second situation. We want to know all values that would cause problems.</p>
<p>I have also made versions for Int, BigInt and Money following the same basic pattern. As with any CLR functions, make sure you know the security and performance&nbsp;implications&nbsp;when using it. Hopefully if you do a lot of data staging then converting this could make your life easier.</p>

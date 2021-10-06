---
layout: post
status: publish
published: true
title: SQLskills Immersion Event on Internals and Performance - Day 5
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 140
wordpress_url: http://www.erichumphrey.com/?p=140
date: '2011-02-25 21:17:42 -0600'
date_gmt: '2011-02-26 03:17:42 -0600'
categories:
- dba
- learning
tags: []
comments:
- id: 59
  author: Our February Immersion Event in Dallas, TX &#8211; Final Wrap-up | Kimberly
    L. Tripp
  author_email: ''
  author_url: http://wp.sqlskills.com/blogs/kimberly/our-february-immersion-event-in-dallas-tx-final-wrap-up/
  date: '2013-01-02 08:32:49 -0600'
  date_gmt: '2013-01-02 14:32:49 -0600'
  content: "[...] SQLskills Immersion Event on Internals and Performance &ndash; Day
    5 [...]"
---
<h3>Overview</h3>
<p>Today was the fifth and final day of the Internals &amp; Performance immersion event. I have really enjoyed learning from not only Paul and Kimberly, but my classmates as well. There were many smart people in that room and hearing each others' perspectives is a part of the class that I'm glad is there. I want to thank Paul and Kimberly for doing these events. I also want to thank my employer for sending me.</p>
<p>We spent the morning finishing up the Statistics module. More interesting stuff about helping the query optimizer pick the best plan. Indexing strategies was even more info about how to provide the optimizer the best info to get a good plan. It's amazing how much difference this can make.</p>
<p>The last part of the day was Paul rushing through Index Fragmentation. Sadly, the Indexing for Performance modules took up a lot of time, thus this module was shorten. Fortunately, I had seen the videos and what was skipped is in the practice virtual machine image that we got on the first day.</p>
<p>To finish up, Kimberly did a quick presentation on the affects GUIDs have on clustered indexes. If you haven't seen this presentation or <a href="http://sqlskills.com/BLOGS/KIMBERLY/post/Disk-space-is-cheap.aspx">read the blog post</a>, do so. You'll thank yourself if you are able to make the changes or prevent implementation in the first place.</p>
<p>Tonight was another night at the hotel bar, but it was my first time staying for it and socializing. It's great when instructors will stick around and socialize. You learn more about their experiences that way and you get to tell your own stories. Now I regret not sticking around the previous nights.</p>
<p>There's another event in Dallas in March which I probably won't get to go to. I really want to go to their HA &amp; DR class in Bellvue in August. Hopefully that will work out.</p>
<h3>Indexing for Performance (Statistics)</h3>
<ul>
<li>As of 2005, stat updates are based on column change counter. This means that a column has to change 20% before stats are auto&nbsp;updated.</li>
<li>(col = @v1 OR @v1 IS NULL) is bad. WITH RECOMPILE won't help</li>
<li><a title="Stored-Procedure-Parameters-giving-you-grief-in-a-multi-purpose-procedure" href="http://sqlskills.com/BLOGS/KIMBERLY/post/Stored-Procedure-Parameters-giving-you-grief-in-a-multi-purpose-procedure.aspx">Stored-Procedure-Parameters-giving-you-grief-in-a-multi-purpose-procedure.aspx</a></li>
<li>sp_executesql = force statement caching</li>
<li>Inaccurate stats can cause problems, filtered stats over ranges can help</li>
<li>Have jobs to run scheduled maintenance on filtered stats</li>
<li>Steve Kass has some interesting math background info for stats</li>
<li>Beware uneven data distributions</li>
<li>Don't trust the query estimate cost comparisons</li>
</ul>
<h3>Indexing for Performance (Indexing Strategies)</h3>
<ul>
<li>Don't index to the query plan, index to the query</li>
<li>OR is similar to UNION... you may be able to get better performance with UNION, UNION ALL even better if you can ensure no dups</li>
<li>Hash => tempdb</li>
<li>sp_updatestats will update stats if even 1 row has changed</li>
<li><a href="http://ola.hallengren.com">ola.hallengren.com</a></li>
<li>Relational data warehouse, heavily consider indexed views</li>
</ul>
<h3>Index Fragmentation</h3>
<ul>
<li>Clustered / Non-clustered fragmentation is the same</li>
<li>Binary search used for singleton lookup on a page</li>
<li>Readaheads need logically ordered pages</li>
<li>4MB largest readahead possible in 2008</li>
<li><a href="http://support.microsoft.com/kb/329526">http://support.microsoft.com/kb/329526</a></li>
<li>Narrow index keys = shallow index tree = faster lookups</li>
<li>Intra query parallelism deadlock</li>
</ul>

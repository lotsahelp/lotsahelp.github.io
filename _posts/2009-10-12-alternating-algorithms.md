---
layout: post
title: Alternating Algorithms
date: '2009-10-12 11:33:00 -0500'
categories:
- code optimization
---
<p><span style="font-family: inherit;">I picked this tip up a while back while reading a book about code quality. This was a trick back in the C days.</span>
<div><span style="font-family: inherit;"><br /></span></div>
<div><span style="font-family: inherit;">Typically alternating logic goes something like this:</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">int x = 0;</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">while ( x < rownum ) {</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp;if ( x % 2 == 0 ) {</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp; &nbsp;row.color = 'blue';</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp;} else {</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp; &nbsp;row.color = 'red';</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp;}</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp;x++;</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">}</span></div>
<div><span style="font-family: inherit;"><br /></span></div>
<div><span style="font-family: inherit;">That works, but you're doing a modulus function and an addition just to toggle between values.</span></div>
<div><span style="font-family: inherit;"><br /></span></div>
<div><span style="font-family: inherit;">Instead I use this:</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">int x = 0;</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">string[] colors = {'blue','red'};</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">foreach( row in table )</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">{</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp;row.color = colors[x];</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">&nbsp;&nbsp;x = 1 - x;</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;">}</span></div>
<div><span style="font-family: 'Courier New', Courier, monospace;"><br /></span></div>
<div><span style="font-family: inherit;">The </span><span style="font-family: 'Courier New', Courier, monospace;">x = 1 - x;</span><span style="font-family: inherit;"> will always toggle between 1 and 0, as long as no value other than 1 or 0 is assigned to it. Now you're just looking at an extra array allocation (you may already have one defined anyway) and a subtraction operation. You also lose the if/else block. Everything's in one loop block.</span></div>
<div><span style="font-family: inherit;"><br /></span></div>
<div><span style="font-family: inherit;">This was needed more in the days where cycles were rare and doing everything in as few opps mattered. Not so much today, but I think it is also cleaner code.</span></div>

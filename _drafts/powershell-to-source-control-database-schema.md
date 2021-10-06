---
layout: post
status: draft
title: PowerShell to Source Control Database Schema
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 20
wordpress_url: http://www.erichumphrey.com/?p=20
date: '2010-09-29 20:18:00 -0500'
categories:
- Uncategorized
tags: []
comments: []
---
<p>I work at a small shop that has neither the extra hardware nor extra time to setup and maintain a test environment. Yes this is bad practice and I&rsquo;m not fond of it, but we always seem to have bigger fish to fry. We are not an OLTP shop, but rather we do data analysis of other companies&rsquo; data.  Our analysts have the ability to modify views within the databases they are assigned. Every once in a while, something gets messed up and they need to revert back to a previous version.<br />
<h1>Options for Restoring a View</h1>
<h2>Restore From Backup</h2>
<p>Our first option is to restore a previous backup to a temporary location, get the view definition and recreate in production. This is time consuming as our datasets can get fairly large. It&rsquo;s also fairly silly to restore a whole database just for one view definition.<br />
<h2>Object Level Restore</h2>
<p>There are third party vendors that provide backup solutions that allow the ability to restore a single object from a backup; a great time and space saver as opposed to above. Unfortunately, these solutions cost money to purchase. Sometimes you just don&rsquo;t have the budget or the ability to get tools like this in your environment.<br />
<h2>Track Changes with DDL Trigger(s)</h2>
<p>We could implement DDL triggers on the database to track these changes. The downside to this approach is we don&rsquo;t get a diff of what changed between versions. We wouldn&rsquo;t be able to tell how far off the current version is from the last good one. An advantage is you can track who made the change and when.<br />
<h2>Source Control</h2>
<p>My preferred solution for this problem is storing changes nightly to source control. The advantage is we can track changes between versions and see how objects have changed over time. The current disadvantage with this system is it only tracks changes nightly, so if something changes more than once during the day, we lose the intermediate changes. We also do not know who made the change, which might not work in larger environments. In our case, we&rsquo;re good with restoring to yesterday&rsquo;s version, and we have a small choice of who to blame and we usually know who it was.<br />
<h1>Implementation</h1>
<p>Our implementation uses PowerShell, SQL Management Objects and Subversion to do all the work. We use Trac to give a nice interface into the Subversion repository. I use PowerShell &amp; SMO to iterate through all the objects, scripting each object out to its own text file. I then call the Subversion client to add any new files to source control, and then commit the changes. I scheduled the PowerShell script to run as a nightly scheduled task under Windows.<br />In order to track changes at the object level, one script per object was necessary. Subversion requires text files to be able to do the differencing. By default .NET strings and thus PowerShell strings are UTF16 and currently Subversion does not support higher than UTF8 as a text file. It would treat UTF16 as binary and you would lose the ability to diff. The script makes sure to encode the output file to UTF8 for compatibility.<br />So far this has worked well for us. Note that it does have to script every object out to a text file then Subversion does the necessary checks for additions and changes. The script currently does not handle object deletions.</p>
<p><a href="http://dl.dropbox.com/u/10945833/ScriptOutDb.ps1">Download the PowerShell script</a><br />
<h1>Links</h1>
<p>
<ul>
<li>PowerShell (http://technet.microsoft.com/en-us/scriptcenter/powershell.aspx)</li>
<li>SMO (http://msdn.microsoft.com/en-us/library/microsoft.sqlserver.management.smo.aspx)</li>
<li>Subversion (http://www.open.collab.net/products/subversion/whatsnew.html)</li>
<li>Trac (http://trac.edgewall.org/)</li>
</ul>

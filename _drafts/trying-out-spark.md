---
layout: post
status: draft
title: Trying out Spark
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 332
wordpress_url: http://www.erichumphrey.com/?p=332
date: '2019-03-22 14:59:09 -0500'
categories:
- Uncategorized
tags: []
comments: []
---
<p>So I've been diving back into the realm of SSIS and BIML lately and found out Azure Databricks, Azure Data Flow and SQL 2019 all use Spark for analytics and ETL so I figure I'd give it a whirl.</p>
<p>I started using this tutorial (https://dzone.com/articles/working-on-apache-spark-on-windows) from Kuldeep Singh on getting Spark running on Windows and I just want to include some of my own notes here.</p>
<p>OpenJDK doesn't work. You must use Oracle JDK 1.8.<br />
SET JAVA_HOME, SPARK_HOME &amp; HADOOP_HOME</p>

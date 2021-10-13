---
layout: post
title: Return value or 0 if negative
date: '2007-09-19 13:19:00 -0500'
categories:
- postgres
- sql
---
If you ever run across a situation that requires you to subtract two numbers and return the value if positive or 0 if negative, here are a couple of solutions.

1) Use IF:
    value = x - y;
    if value < 0
      return 0;
    else
      return value;

2) Use UNION:
    select max(value)
    from (
      select x - y as value
      union select 0
    ) x

I like this way since IF requires plpgsql in Postgres whereas UNION can be used with plain sql.

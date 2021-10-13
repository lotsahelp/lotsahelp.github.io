---
layout: post
title: Reset sequence values to max
date: '2008-02-25 14:29:00 -0600'
categories:
- sql
---
This function resets all sequences to the max value + 1 in their respective tables.
<pre><br />CREATE OR REPLACE FUNCTION util_reset_sequences () RETURNS SETOF VARCHAR as $$<br />DECLARE<br /> myrow RECORD;<br /> max_id INTEGER;<br /> seq_name VARCHAR;<br /> mytable VARCHAR;<br /> myquery VARCHAR;<br />BEGIN<br /> FOR myrow IN select * from information_schema.columns where column_default like 'nextval(%' LOOP<br />  mytable := myrow.table_schema || '.' || myrow.table_name;<br />  myquery := 'SELECT coalesce(max(' || myrow.column_name || '),0)+1 FROM ' || mytable;<br />  --RAISE NOTICE 'query = %', myquery;<br />  EXECUTE myquery INTO max_id;<br />  seq_name := SUBSTRING(substr(myrow.column_default, 10) FROM E'[[:alnum:]_\.]*');<br />  ----seq_name := SUBSTRING(myrow.column_default FROM '\\''([[:alnum:]_]*)');<br />  --RAISE NOTICE 'seq_name = %', seq_name;<br />  EXECUTE 'ALTER SEQUENCE ' || seq_name || ' RESTART ' || max_id;<br />  RETURN NEXT '"' || seq_name || '","' || myrow.table_name || '","' || myrow.column_name || '",' || cast(max_id as varchar);<br /> END LOOP;<br /> RETURN;<br />END;<br />$$ language 'plpgsql';<br /></pre>

---
layout: post
title: Recreate Foreign Keys in PostgreSQL
date: '2008-02-25 14:24:00 -0600'
categories:
- foreign key
- postgres
- sql
---
<p>Function I devised to allow recreating all foreign keys and switching if they cascade:</p>
<pre>CREATE OR REPLACE FUNCTION util_recreate_fks (allow_cascade bool) RETURNS bool AS $$<br />DECLARE<br />    constraint_cursor CURSOR FOR<br />        SELECT t.constraint_name, t.table_schema || '.' || t.table_name as table_name, --t.constraint_type,<br />            c1.table_schema || '.' || c1.table_name as table_name_pk, c1.column_name as column_name_pk,<br />            c2.table_name as table_name_fk, c2.column_name as column_name_fk<br />        FROM information_schema.table_constraints t,<br />            information_schema.constraint_column_usage c1,<br />            information_schema.key_column_usage c2<br />        WHERE t.constraint_name = c1.constraint_name<br />            AND t.constraint_name = c2.constraint_name<br />            AND t.constraint_type = 'FOREIGN KEY';<br />    _constraint RECORD;<br />    cascade_setting VARCHAR;<br />BEGIN<br />    IF (allow_cascade) THEN<br />        cascade_setting := 'CASCADE';<br />    ELSE<br />        cascade_setting := 'NO ACTION';<br />    END IF;<br /><br />    OPEN constraint_cursor;<br /><br />    LOOP<br />        FETCH constraint_cursor INTO _constraint;<br />        EXIT WHEN NOT FOUND;<br />        EXECUTE 'ALTER TABLE ' || _constraint.table_name || ' DROP CONSTRAINT ' || _constraint.constraint_name;<br />        EXECUTE '<br />            ALTER TABLE ' || _constraint.table_name || '<br />                ADD CONSTRAINT ' || _constraint.constraint_name || ' FOREIGN KEY (' || _constraint.column_name_fk || ')<br />                    REFERENCES ' || _constraint.table_name_pk || '(' || _constraint.column_name_pk || ')<br />                    ON UPDATE ' || cascade_setting || '<br />                    ON DELETE ' || cascade_setting || '<br />        ';<br />    END LOOP;<br /><br />    CLOSE constraint_cursor;<br /><br />    RETURN allow_cascade;<br />END;<br />$$ LANGUAGE 'plpgsql';<br /></pre>

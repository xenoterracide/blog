+++
title = "empty() function for postgresql in sql"
date = 2010-01-20T13:00:00Z
updated = 2010-01-20T13:00:01Z
categories = ["Database"]
tags = [ "database constraints", "stored procedures", "postgresql", "sql"]
blogimport = true 
type = "post"
aliases = ["/2010/01/empty-function-for-postgresql-in-sql.html"]
+++

In PostgreSQL the ASCII `NUL` or empty string `\0` is seen as `NOT NULL`. This is because postgres developers consider
any characters data. I know there are better discussions on it but I can't find them right now. Unfortunately the
programming language you are using probably doesn't see it the same way. There's a good chance that initialized
variables are set to `\0` and so when you try to insert from your language to a `NOT NULL` field with variables that
are seen as undefined in your language, postgres accepts it, and now you have fields that you probably consider to have
no data but are `NOT NULL`.

My function possibly would be better called `empty_or_whitespace` but in my mind any text field that contains only
whitespace is empty, and I'd rather have 1 function and 1 regex deal with both than have 2 functions have to be called
on every `insert` or `update`.

Here is the gist
```plpgsql
 CREATE OR REPLACE FUNCTION empty(TEXT)
RETURNS bool AS
        $$ SELECT $1 ~ '^[[:space:]]*$'; $$
        LANGUAGE sql
        IMMUTABLE;
COMMENT ON FUNCTION empty(TEXT)
        IS 'Find empty strings or strings containing only whitespace';
```
It returns true if an empty string is found. It really requires a knowledge of SQL, PostgreSQL's
[`CREATE FUNCTION`][createfunction], and Regular Expressions, to understand. Thanks to the wonderfully helpful
PostgreSQL community with perfecting it.

In order to use it to keep ASCII NULs and fields that someone has just entered whitespace into you need to add it to
your table as a constraint.

Here's an example `create table` using it.
```postgresql
 CREATE TABLE example (
        field TEXT
              NOT NULL
              CHECK ( NOT empty( field ))
 );
```
remember it returns true on null's and whitespace so you have to say `NOT emtpy( field )`.

[createfunction]: http://www.postgresql.org/docs/current/interactive/sql-createfunction.html

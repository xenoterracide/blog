+++
title = "postgresql initial setup (users and databases)"
date = 2009-03-18T05:22:00Z
updated = 2009-05-31T22:18:02Z
categories = ["Database"]
tags = ["postgresql"]
aliases = ["/2009/08/postgresql-initial-setup-authentication.html"]
blogimport = true 
type = "post"
+++

This guide assumes that you've managed to [install][install] and start the postgres server. I'm ignoring these because
they are well documented in several places, probably including your distribution, however, what to do next isn't.

First you need to connect to the Postgres database with psql as postgres, on gentoo* distributions you must be in the
Postgres group to do this. In the event this doesn't work you may have to su to the postgres unix user account somehow
and run psql from there.
```shell
psql postgres postgres
```
you now have connected to what is essentially the "root" user for postgres. as with all 'super user' accounts be
careful and know what you are doing. also, as with all 'super user' accounts you don't normally want to run as them,
so we need to create another user. I recommend creating the user with the same name as whatever shell account you may
be connecting to it from (if applicable) because then you don't have to specify the username when typing psql.
[`CREATE ROLE`][crole]

```postgresql
CREATE USER xenoterracide WITH PASSWORD 'YeahLikeIdTellYou';
```

next you need to give the user a database. like the user account if you make it the same name as the shell account you
won't have to pass it to psql. [`CREATE DATABASE`][cdb]

```postgresql
CREATE DATABASE xenoterracide WITH OWNER xenoterracide;
```
now you're done with the setting up your users account so quit psql by tying `\q` at the prompt. now you can simply log
in from your users shell by typing psql and SQL-ing away.

[install]: http://www.postgresql.org/docs/current/interactive/installation.html
[crole]: http://www.postgresql.org/docs/current/interactive/sql-createrole.html
[cdb]: http://www.postgresql.org/docs/current/interactive/sql-createdatabase.html

+++
title = "PostgreSQL initial setup (authentication) Part 2"
date = 2010-01-19T11:37:00Z
updated = 2010-01-19T11:44:20Z
categories = ["Database", "Security"]
tags = ["authentication", "postgresql"]
aliases = ["/2010/01/postgresql-initial-setup-authentication.html"]
blogimport = true 
type = "post"
+++

I ran into so problems and lack of information with my last [post on this topic][pt1]. Firstly my syntax for
```
local all all to local all all ident devel
```
doesn't seem to work in my current setup. It's possible that it has
something to do with the configuration of the Debian/Ubuntu server I was basing that against, and now my targets are
Arch Linux and Slackware.

So our goal here will be to provide an alternate user that can log in as postgres via ident. why would you want to do
this? maybe you're tired of su - to the postgres unix user after logging in as your administrative user (root?).

First we need to open our pg_ident.conf and add the following line.
```
admin root postgres
```
So as I said in the last post on this: "admin" is an arbitrary identifier, root is the system user, and postgres is the
database user. Now let's go ahead and make sure the postgres system user can still login.
```
admin postgres postgres
```
ok that's all we need to do in the `pg_ident.conf`. Go ahead and open `pg_hba.conf` and add this line above the default
3 lines in the file.
```
local all postgres ident map=admin
```
as a breakdown: `local` is for socket connections, `all` means all databases, `ident` means it's checking for local
users, and `map=admin` says look at the users in the admin identifier in `pg_ident.conf`.

Now you should be able to run psql dname postgres as root. You will not be able to use the db postgres user as any
system user not in the admin map.

[pt1]: /post/postgresql-initial-setup-users-and-databases/

+++
title = "More Security = Better. Wrong!"
date = 2009-08-22T19:54:00Z
updated = 2010-01-19T11:46:33Z
categories = ["Security", "Editorial"]
tags = ["security", "authentication", "passwords", "postgresql" ]
aliases = ["/2009/08/more-security-better-wrong.html"]
blogimport = true 
type = "post"
+++

So I just had a discussion on [#ubuntu-server on freenode][fn] about why my not having a password to connect to 
postgresql via a socket (read local cli) is insecure. So I asked them, how exactly is it that someone is going to get
this access? The answer "there are bad people on the Internet". I'm sure many people right now are agreeing with them
and thinking I'm crazy. Let's discuss my setup though shall we.

## Postgres

I'm using Ident, and not just sameuser ident. no I had to set a custom rule in the pg_ident.conf file for
this user to access because the systemuser != the db user. so just typing psql at the command line should you get access
still won't get you into the db. you have to know which user/database to connect to. But that's not that hard right? in
fact it's trivial.In addition you have to be a certain systemuser, only one works.

## User Access

There is only one user account on the system that can be logged into (it's not root or a generic name).
Only 2 people have the credentials to get shell access. One is my host, who happens to have physical and kvm access.
The other is me who has ssh access.

## SSH

I have ssh on a high port with no password authentication, or root, allowed.
So in order to passwordlessly access the database you would have to ssh into the system from a remote location or find
an exploitable bug in apache (the only other service listening, or the kernel) that allows you to switch to my non
apache user (meaning root access).

In addition, this system user had rw access to the entire website which includes a file containing the password to the
database in plain text. So let's say I created a `.pgpass` file or variables? what exactly is it that would keep
anyone who has access to this account from gaining access to the database? in fact wouldn't it just make it easier,
since pgpass has a known location and contains all connection info including the username/dbname string?

But why do I need passwordless access anyways? I wrote a script that dumps the db every hour to a git repo and then
pushes that to a remote.

Could I secure it further? yeah I could. I could make the script run as a user who can't log in at all and then put a
pgpass in that users account. I'd have to properly ACL the webroot to give the correct write and read access. But is
all this really necessary? maybe and I might do it, but at this point it's not nearly as important as it was for me to
get backups up and running, because regardless of all the security I implement, if I have no backup and someone finds
that loophole in one of the pieces of software and uses it, I might just be screwed. At least now I can restore the
site if it gets attacked.

P.S. I was having a problem with my backup not being run by cron, never did figure out why.

[fn]: irc://chat.freenode.net/ubuntu-server

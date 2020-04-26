+++
title = "PostgreSQL \\edit and Syntax Highlighting"
date = 2008-11-29T10:35:00Z
updated = 2008-11-29T11:05:20Z
categories = ["Database"]
tags = ["psql", "tips", "vim", "postgresql"]
aliases = ["/2008/11/postgresql-edit-and-syntax-highlighting.html"]
blogimport = true 
type = "post"
+++

If you've ever used `\edit` with PostgreSQL you've probably noticed that it's not using syntax highlighting that your
favorite editor most likely supports. I've wondered why this is for a while, Postgres uses vim on my system which
supports robust highlighting among other things.

I had a few things suggested to me, such as setting putting settings in my `.vimrc`, most of which would have forced
`filetype=sql` on files that weren't sql, and would have been active outside of psql.

When you have a question what's the first thing you should do? RTFM. The answer was in the man page all along.

> Tip:  psql searches the environment variables PSQL_EDITOR, EDITOR, and VISUAL (in that order) for an editor  to  use.
> If  all  of  them  are  unset, vi is used on Unix systems,notepad.exe on Windows systems.

so how does this solve the problem? well the only program that uses `PSQL_EDITOR` is Postgres, and most editors allow
you to specify options at startup. In the case of vim, if you want to have use `filetype=sql` for the default filetype
for `\edit` all you have to do is

```shell
export PSQL_EDITOR='vim -c "set ft=sql"'
```
of course if you don't use vim or you want to set some different options you'll have to read the documentation for your
editor, or operating system.

+++
title = "--sync should be sync"
date = 2008-12-21T15:32:00Z
updated = 2008-12-21T17:38:46Z
tags = ["Portage"]
blogimport = true
type = "post"
+++

`emerge sync` never should have become `emerge --sync`, of course 2 years ago I thought it was the right thing, it isn't though, live and learn. `sync` is currently an argument with the syntax of an option.

_What's the difference?_

An argument is what the command, or executable operates on. Some examples
* `cp file0 file1` copies file0 to file1.
* `ls` list the current working directory
* `ls dir0` lists directory dir0

Right, so how is `--sync` an argument and not an option?

Options effect how commands operate on arguments. For example `cp file0 file1` will overwrite file1 if it exists. `cp --interactive file0 file1` will prompt to overwrite if file1 exists. `ls -R` will list any files and directories off the current working directory recursively. `ls -R dir0` will list any files and directories off dir0 recursively.

_I still don't understand why sync isn't an option, it isn't a file so wouldn't it be an option?_

Well that's basically what I used to think, not a file not an argument. It's not true to understand better I suggest picking up a version control system (e.g. svn) and seeing how you can have arguments that aren't files. Example `svn checkout -r 1346` checkout revision 1346. See the `-r` switch is an option that operates on the checkout argument to the `svn` command.

_Starting to get it..._

`--sync` currently doesn't affect any target, doesn't change a thing but isn't a *default* either. Some options are just there for explicitness and are in effect unless another option is there.

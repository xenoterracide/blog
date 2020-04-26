+++
title = "cat that displays tabs at 4 spaces"
date = 2010-04-25T20:26:00Z
updated = 2010-06-29T10:09:35Z
tags = ["tutorial", "shell", "code"]
categories = ["Development"]
blogimport = true 
type = "post"
+++

So in the tabs vs spaces war I'm squarely on the side of use tabs. tabs have meaning. tabs allow people to set there
editor's to whatever space display width their eyes are comfortable with as opposed to what yours are (e.g. you like
2 spaces I like 4). However, I've heard the argument what about when you cat the file, etc. Well it is actually a bit
annoying... so let's fix it.

my requirements when I type `cat foo` if foo has tabs they will be displayed at 4 spaces, and I can still pass all cli
options as I normally would to cat. To do this we're going to create a shell function called `etcat()`
```bash
etcat() {
    env cat "$@" | env expand -t 4;
}
```
I'd like to note, that in order for this to work you *must* use either an absolute path or a call to `env cat` because
otherwise the shell will attempt to call cat as a recursive function. I put this in my .alias file and sourced it.

you can now put `alias cat="etcat"` in your aliases file, or run etcat. This is to protect you from any problems that
could otherwise be caused by overriding cat in scripts you don't control (not that I'm actually aware of any for this
function).

P.S. Thanks to @greybot on #bash for the incantation as I was having trouble with the `"$@"`

**Update** changed name of function

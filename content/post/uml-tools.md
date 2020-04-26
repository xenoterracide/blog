+++
title = "UML Tools"
date = 2012-09-05T06:00:00Z
updated = 2012-09-05T12:56:34Z
tags = ["uml"]
categories = ["Software Design"]
blogimport = true 
type = "post"
aliases = ["/2012/09/uml-tools.html"]
+++

### Why UML?

Many people appear to think that modelling is only for academic textbooks and school. Several months ago I worked on a
project that failed, for numerous reasons, but some of the reasons were mine. So I set out to figure what it was that
I didn't know, that would have allowed me to build this system I had been asked to.

After reading [Patterns of Enterprise Application Architecture][poeaa],
[Domain-Driven Design: Tackling Complexity in the Heart of Software][ddd], and
[Design Patterns: Elements of Reusable Object-Oriented Software][gof] and realizing that all of the great books used
UML. I realized that UML is a great visualization tool, and that it helped me understand the concepts they were talking
about. This didn't have to be entirely academic, and perhaps I could use these tools to communicate my idea's. So I've
set out to learn UML, and picked up copies of 
[UML Distilled: A Brief Guide to the Standard Object Modelling Language (3rd Edition)][ud] and although I find it to be
a decent reference guide, I didn't think it was quite what I was looking for. So I then picked up 
[Applying UML and Patterns: An Introduction to Object-Oriented Analysis and Design and Iterative Development (3rd Edition)][ooad]
, and am about halfway through it. What I really wanted was a book to explain the what, when, and why of UML, this book
seems to do that and more. It  covers a lot of subjects I've glanced over previously in a fair amount of depth, and
puts them together.

So how does using UML relate to my original issue? I determined after reading these books that what we had done poorly
in our "Agile" process was planning. Another common fallacy is that iterative development means don't plan. No what it
means is keep your planning to a minimum, code, code, code, plan, code, code, code, etc. If you spend more than a week
planning you're spending too much time, and your planning itself should be iterative, meaning go back and do more at
the beginning of each iteration. I also learned that part of, pre-coding, planning is modelling. When you're dealing
with a simple one or two objects modelling is likely overkill, but as your software complexity increases, the more
indispensable modelling becomes.

Armed with new theories of Object Oriented Analysis and Design, and a visual modelling language to assist I set out to
model the complex system. The initial tool I selected for modelling is fairly common, and I used it to build a few
diagrams, but as time went on my diagrams changed more and more, and became more complex, and I became more and more
frustrated with the tools ability to help me update the model. In spite of this as I worked through the model, and
distilled it, I realized that the model itself was working. Now I simply needed a better tool to model with, and more
time to build the system.

### What am I looking for?

I work entirely in a Linux Environment and do not have WINE set up because I abandoned Windows about 5 years ago now,
so whatever tool I choose has to work in Linux, and I will only do so through WINE if no adequate non wine solutions
present themselves.

I really need reasonable pricing because I'm still hemorrhaging money on student loan. Free is obviously ideal, but I
need to be reasonable, this thing should not cost more than my cell phone.

I don't really use IDE's such as Eclipse or Netbeans, so integration or as a plugin is not required, in fact I don't
want to run those to run this. </p><p>The first tool I used, I ultimately realized that it was making me do vector
graphic artist level tweaks to make it look good. So I require that it require as little human intervention as possible
to look good.

One of my colleagues is blind, and I'd like to be able to share my diagrams with him, so I require the diagram be able
to be output to a simple text format. Ideally a format generated from the diagram, and not the diagram generated from
the format, because while diagramming I am thinking visually.

Full UML 1.4 support is required for me to get off the ground, but I'd personally prefer UML 2.X support as it has a
few nice features such as sequence diagram conditionals.

The tool should be as intuitive (to me) as possible. I've found many of the tools require my brain to context switch,
or I simply have trouble finding out how to do the feature I'm looking for.

Once I discovered that some tools were capable of determining that some elements were related across diagrams I decided
that I should have this feature and it should do as much for me as it can.

The tool and it's UML should be aesthetically pleasing, this seems novel, but if I hate looking at the tool I'm not
going to use it.

### How am I testing?

Well in addition to simply checking the general requirements I have, as you can probably tell a lot of what I'm looking
for is subjective. So basically I spent some time modelling a simple blog. If I encounter any serious frustrations
creating diagrams the tool is discounted and I move on. Basically this is, largely, a usability and intuitiveness test,
if I have to think about the tool very much then it's getting in my way.

## FLOSS

### Dia

The first tool I used was [Dia][dia]. It is basically a free Visio clone. It's very flexible and allows me to create
almost any diagram I could want, though it only seems to support UML 1.4. I also found myself micromanaging the
relation lines, which only seemed to have one style and wouldn't path together in the right places. Dia requires that
you click a text edit palette button to change the name on something and it's finicky about it, which is just
frustrating. There was also no auto arrange feature. Ultimately I've found the unlimited flexibility to take my mind
off my task of modelling and put it more on drawing. I worked with this tool for a few weeks, but these were also weeks
where I was less familiar with UML, so I hit the limits later.

### UMLet

[UMLet][umlet] was the next UML tool I tried. I found it to be much simpler than Dia for the same purpose, and it
allows you to modify the elements that you can add in a purely textual way, and the file format stores them this way,
which would almost provide my textual format requirements. However I found it's support for sequence diagrams to be
limited. Also Class, and other diagrams would not resize if the text was too big. Lastly, although I could drag main
elements out of the sidebar to add, I found that I couldn't do so with relationship lines, which got frustrating. The
Class to edit class text requires a mental context switch which I found to be unpleasant.

#### Umbrello

Next I tried KDE's [Umbrello][umbrello]. The first thing I noticed is that it's hideous. KDE software is usually
amongst the most attractive on Linux in my opinion, but Umbrello does not follow the stereotype. The default Class is
red and yellow, this can be changed but unfortunately that's not really the only problem. I found the overall user
interface to be a little odd, perhaps it was just from using other tools that have some better interface design
decisions. This was the first tool that I found could do cross diagram associations, and it even has a seemingly nifty
feature which makes a relationship not it's own entity, and therefore you can edit it while editing the class.

### Violet

[Violet][violet] is actually one of the nicer open source editors, ultimately it made my top 2 pics on the Open Source
list. It appeared to be relatively easy to make nice diagrams in violet, without the interface getting in the way.
Unfortunately it doesn't allow diagrams to be inter related, nor does it offer any kind of a text export. I am not sure
whether I ultimately like the change mouse pointer create an element on every click until you reselect the normal
pointer, this is like an art tool.

### ArgoUML

[Argo UML][argo] is the closest to the proprietary tools I've tried. Unfortunately like all of the open source tools it
only support UML 1.4. It does support cross diagram relationships, and has an easy to use interface. It is actually
the originating source of one proprietary product Poseidon. However it did not support the text export. I decided to
keep looking into proprietary products to see if one had a significantly better interface than this. But if you must
have an Open Source product this is the one I suggest.

## Web / Cloud

### yUML

[yUML][yuml] is a partial solution to UML diagrams via a web interface. It allows you to generate most UML diagrams via
a simple text interface. I like this software, it doesn't give you much control over the arrangement of the diagrams,
only the semantics, but personally I think that's ok. Unfortunately it doesn't support sequence diagrams, but the
author has a reason for that.

### Web Sequence Diagrams

[Web Sequence Diagrams][wsd] is complementary to yUML, in that it provides the missing sequence diagrams, and combined
you should be able to make all the UML diagrams you really need.

### Creately
[Creately][creately] is a flash based visio clone. To some degree I found it even nicer than Dia, because the diagrams
were prettier, and mostly easier to manage. I gave up on this tool when I realized it was missing some diagrams, had
not text export, and especially because I found it incredibly difficult to add multiplicity to class diagrams.

## Proprietary

### Visual Paradigm

I tried [Visual Paradigm for UML][vpuml] and by tried, I mean I couldn't get it installed. I've been using Linux for
~10 years now, and half of that was on Gentoo, I even managed to get Oracle 11G working on Arch Linux, a video card
that no distro could detect working, and built my own Fork. It complained of not being able to access the X server,
but considering I could run other X programs from that shell, I would say that whatever it was trying to do it was
trying to do wrong. I tried both the installer and the "just a zip" version, and the latters binaries didn't spawn a
window. Needless to say I didn't proceed further with their software, already having a full host of alternate
solutions.

### Magic Draw

I was able to get [Magic Draw][md] installed, the hardest part was figuring out that they had sent my evaluation
license to my email, and my email had filed it as spam. After playing with it for a while, I found that certain pieces
of the diagram (again multiplicity) were hard to figure out how to add. In this case It was about how they'd named it.
I felt their interface was more complicated in general than necessary. The nicest thing about Magic draw is it has the
best auto arrange I've evaluated, and it does not allow elements to be placed on top of each other. However, I
eliminated it due to the other challenges of its use. 

### Poseidon

Ok, I didn't end up trying [Poseidon][poseidon] because by the time I got to it, I had idea's on what I was looking
for. A [quick look][pql] tells me that I'd have to pay more than my computer cost to get the auto arrange feature and
the plugins. To try the community version I'd have to register, and it appears sign up my credit card and cancel within
30 days. I personally believe that it's shady if you're making me remember to cancel and it's not a service.

### Enterprise Architect

[Sparx Enterprise Architect][ea] runs in [WINE][eaf]. I tried installing this on Wine 1.5.12, but either something went
wrong with my following of the instructions, or they are not complete. Much like Visual Paradigm it isn't really worth
my time to figure out why it didn't just work when I'm evaluating products. It not "just working" is a massive black
mark.

### Astah

Of course I save my recommendation for last. [Astah Professional][astah] is very similar to ArgoUML in UI design. In
addition to Argo's features it includes a working auto arrange for class diagrams, full UML 2.0 support, and a
plugin to upload to yUML (untested, but should allow the textual representation). In fact during my work with it I
found only a few things that I found limiting.

When using auto arrange on class diagrams any text on relation lines tends to get overlapped, but a little tweaking at
that point hasn't seemed hard, and will be easier than rearrange the entire diagram. Also auto arrange doesn't seem to
work for sequence diagrams. Hopefully both of these improve in future versions, but they're really minor, and I didn't
find anything that was significantly better at this.

You can overlap elements, that probably shouldn't be able to overlap, such as classes and lifelines. This allows for a
great deal of flexibility, but I also think that not allowing it would be a good idea, perhaps it could be a future
toggle-able option. As an issue it's pretty trivial,though made a bit worse by auto arrange not working in sequence
diagrams. 

The only thing I don't like about it, is how, like Argo and many of the others, I have to "change my mouse pointer" to
determine what new elements I'm creating, like selecting a palette. Existing model elements are drag and drop, but I
wish new elements were also drag and drop as it more matches my mental model. Most of the tools I tried were this way
though, only a couple being drag and drop.

Astah even appears to have a couple of reasonable pricing models subscription, and perpetual (neither of which cost
more than my phone), with cheaper for students and a free [community edition][ac]. They even have a program for open
source projects and community leaders called [Friends of Astah][af]. 

**Disclaimer:** I applied for "friends" and was accepted. I *was* asked to write a blog on Astah. The truth is this post
was already written with a winner decided before I even applied. I have refactored it a bit though to be a little more
in depth on Astah.

[af]: http://astah.net/friends-of-astah
[ac]: http://astah.net/editions/community
[astah]: http://astah.net/
[eaf]: http://www.sparxsystems.com/support/faq/enterprise-architect-WINE.html#one
[ea]: http://www.sparxsystems.com.au/
[pql]: http://www.gentleware.com/edcompare.html
[poseidon]: http://www.gentleware.com/products.html
[md]: http://www.nomagic.com/products/magicdraw.html
[vpuml]: http://www.visual-paradigm.com/product/vpuml/
[poeaa]: http://amzn.to/2mVIdv7
[ddd]: http://amzn.to/2nz1mWD
[gof]: http://amzn.to/2nz5ki6
[ud]: http://amzn.to/2mEjtpm
[ooad]: http://amzn.to/2nzjMqo
[dia]: https://live.gnome.org/Dia
[umlet]: http://www.umlet.com/
[umbrello]: http://uml.sourceforge.net/
[violet]: http://alexdp.free.fr/violetumleditor/page.php
[argo]: http://argouml.tigris.org/
[yuml]: http://yuml.me
[wsd]: http://www.websequencediagrams.com/
[creately]: http://creately.com

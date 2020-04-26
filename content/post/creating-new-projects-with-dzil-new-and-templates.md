+++
title = "Creating new projects with dzil new and templates"
date = 2010-07-30T18:00:00Z
updated = 2010-07-30T18:00:03Z
tags = ["Text::Template",  "perl", "Dist::Zilla"]
blogimport = true 
type = "post"
+++

[Here][catalyst] I talked about creating a new
catalyst project using a minting profile for Dist::Zilla. If you don't know how to create a minting profile read that
first. I'm sure once you've tried that you'll agree that having a little bit more than the basics in a newly minted 
dist would be a good thing.

first we need to create our profile.ini correctly
```ini
[GatherDir::Template]
    root = repo
    include_dotfiles = 1
```
(note: if you've got [`[DistINI]`][distini] plugin loaded you'll probably want to remove it) Now you can put any file
in the subdirectory repo of your profile (if you leave out `include_dotfiles = 1` then anything beginning with a `.`
won't be included), and it can be a template using [Text::Template][tt]. [Dist::Zilla][dzil] uses 
`{{ }}` for [Text::Template][tt] Delimiters.

Let's start with adding a `.gitignore` file (if you're using git) we can create`{profile}/repo/.gitignore`
```ini
/{{$dist->name}}*
.build
```
the `{{$dist->name}}*` will exclude the directories and archives `dzil` creates on release and `.build`, will of course,
ignore the .build dirctory.

Now for a more complex issue, creating a `Changes` file that has the the `{{$NEXT}}` variable in it to insert the date 
and such on build.
```
{{$dist->name}}
{{ '{{$NEXT}}' }}
    -
```
Obviously you can format your `Changes` file however you want.

Now we want to create a much more complicated `dist.ini`
```ini
{{
    $username = 'xenoterracide';

    $license = ref $dist->license;
    if ( $license =~ /^Software::License::(.+)$/ ) {
        $license = $1;
    } else {
        $license = "=$license";
    }

    $authors .= $_ for @{$dist->authors};
    '';
}}name    = {{$dist->name}}
author  = {{$authors}}
license =  {{$license}}
version = 0.1.0
copyright_holder = {{$dist->copyright_holder}}

[MetaResources]
bugtracker.web  = http://github.com/{{$username}}}/{{$dist->name}}/issues
repository.web  = http://github.com/{{$username}}/{{$dist->name}}
repository.url  = git://github.com/{{$username}}/{{$dist->name}}.git
repository.type = git

[ReadmeFromPod]
[@Filter]
-bundle = @Basic
-remove = Readme

[AutoPrereq]

[SubmittingPatches]
[PkgVersion]
[PodWeaver]

[NextRelease]
    format = %-9v %{MMM dd yyyy}d

[CompileTests]
[CriticTests]
[DistManifestTests]
[EOLTests]
[ExtraTests]
[HasVersionTests]
[KwaliteeTests]
[MetaTests]
[MinimumVersionTests]
[PodCoverageTests]
[PodSyntaxTests]
[ReportVersions]
[UnusedVarsTests]

[PruneFiles]
filenames = dist.ini
filenames = weaver.ini

[@Git]
push_to = my
tag_format = %v

[Git::CommitBuild]
```
All of the stuff between the first set of  `{{ }}` is boilerplate mostly taken from the `DistINI` plugin so that we can
use our settings from our `config.ini`, I really wish there were some convenience accessors for this. I also wish we
had an arbitrary stash we could use in `config.ini` so I wouldn't have had to hardcode my username in this. I think 
it's all fairly self explanatory beyond that. Of course you can set up your `dist.ini` anyway you want.
Also if you use this format you have to have your module's repo name on GitHub in camel case like it is on CPAN.

[tt]: https://metacpan.org/pod/Text::Template
[dzil]:  https://metacpan.org/pod/Dist::Zilla
[catalyst]: /post/announcing-distzillaplugincatalyst
[distini]: https://metacpan.org/pod/Dist::Zilla

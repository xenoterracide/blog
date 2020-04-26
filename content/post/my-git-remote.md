+++
title = "my git remote"
date = 2010-03-24T03:12:00Z
updated = 2010-03-24T03:12:09Z
tags = [ "git", "convention", "github"]
blogimport = true 
type = "post"
+++

So occasionally I like to fork a project on <a href="https://github.com/">GitHub</a> well the proper way to do it is to use GitHub's fork button which then clone's the repo into your account. this is great! I can than clone that locally develop it an push to it. Usually I do this push with <code>git push origin</code>. I can then ask the owner of the original repo that I forked from to 'pull' my changes in. Wait did I just say original? why am I calling my repo on github origin? it's probably not the origin repo... it's not the primary project repo... so I decided I needed a new convention. I now do <code>git remote add my uri://...</code> for my repo's on github and <code>git remote add origin uri://</code> for the projects authoritative repo. This also helps when I need to update my repo from the origin and then push them to my public forks.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>

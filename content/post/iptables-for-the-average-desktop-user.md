+++
title = "iptables for the average desktop user"
date = 2008-04-08T23:20:00Z
updated = 2009-05-16T00:52:43Z
tags = ["howto", "Linux", "security", "iptables", "linuxtips", "tips"]
blogimport = true 
type = "post"
aliases = ["/2008/04/iptables-for-average-desktop-user.html"]
+++

The Best guide for learning the basics of iptables is here [Linux 2.4 Stateful firewall design][stateful-fw-design]
for the most part it continues to apply to the 2.6 kernel. The only things that won't apply to your linux system will
be: emerge if you aren't on gentoo, and the kernel options which have changed since 2.4 and even a couple of times
during 2.6.

I'm not going to cover those here. If you need help building your kernel or installing `iptables` I suggest that you
consult with either the iptables home page or even better your distribution. Chances are it is already installed, and
may even be configured.

First Let's see if we have any rules.

_The following commands require root access, and can be run in a root shell, with sudo, or in a shell script by root_

```console
iptables -L -v
```

your output should look something like this if you have no rules

```console
Chain INPUT (policy ACCEPT 211 packets, 27413 bytes)
 pkts bytes target     prot opt in     out     source               destination
Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
Chain OUTPUT (policy ACCEPT 312 packets, 211K bytes)
 pkts bytes target     prot opt in     out     source               destination
```
 If it looks different no worries it just means that your distro has already installed rules.
 
 If so make sure **before continuing** to flush them. **First** check to make sure your policies are set to accept
 (you can see that in caps above) if they are anything else run 
 ```console
 iptables -P INPUT ACCEPT
 iptables -P FORWARD ACCEPT
 iptables -P OUTPUT ACCEPT
 ```
 now that your policies are clean you should flush the rules
 ```console
 iptables -F # flush all rules
 iptables -X # delete all chains
 ```
 all right now hopefully you are at a clean state. If you have ever used `iptables` for nat you may not be, but that is
 beyond the scope of this article.
 
 Now we need to create rules, since this is for normal desktop users you shouldn't have any services listening, also
 you shouldn't be routing anything.
 
 To disable routing with `iptables` (I'm ignoring the kernel setting for this)
 ```console
 iptables -P FORWARD DROP #  Set all forwarded packets to go bye bye if they reach the end of the chain.
 ```
 
 The output chain is good on accept for the normal user, only a masochist would want to write rules for it. You
 generally should trust your outbound traffic.
 
 Now to secure input
 ```console
 iptables -A INPUT -m state --state INVALID -j DROP                # This rule drops all packets with a bad state
 iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT  # accept any packets that have something to do with ones we've sent on outbound
 iptables -A INPUT -i lo -j ACCEPT                                 # accept any packets coming or going on localhost (this can be very important
 iptables -P INPUT DROP                                            # set all packets not matching these rules to drop
 ```

Various linux distro's may require you to do something special to save these rules so that they survive a reboot. 
Consult your distributions community.

If you have rules that you want to keep but also want to use mine I suggest putting mine first, you should do the `-A`
rules in reverse but use a `-I` for insert.

When dealing with iptables always take care when applying `iptables -P DROP`. You can be locked out of the machine or
the internet if you apply this without the appropriate rules in place.

That's it. your desktop should be secure from an attacker that you aren't allowing in. There are of course other things
that you can do to make it even more secure but those are beyond the scope of this tutorial.

[stateful-fw-design]: http://www.gentoo.org/doc/en/articles/linux-24-stateful-fw-design.xml

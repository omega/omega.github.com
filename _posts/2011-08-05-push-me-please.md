---
layout: default
title: "push-me-please"
tags:
    - tech
    - nodejs
    - code
    - github
---
So, once again, at work, I had a small itch, and I decided to scratch it :) We
have a build-system that does CI for us, and it relies on githubs wonderful
post commit hooks to know when to run CI. Then we have the ops team, who thinks
builder should be behind the firewall, and as such, not really reachable by
github. We convinced them to punch a tiny hole in the firewall, based on a post
I found with some IPs.

Then this week, we started seing commits not triggering CI anymore. At first
I was an arrogant bastard and blamed it on the devs of course, but once several
of them complained, I started looking into it. Turns out that GitHub has more
IPs pushing post-commit hooks now.

I tried to find some sort of source of IPs somewhere, but I couldn't. So
I wrote a small service again, of course :p

I called it "push-me-please", and it is quite simple. I configured a dummy-repo
with a post-commit hook to [postbin.org](http://postbin.org). The service keeps
pushing the "test hooks" button, and polling the feed from postbin, picking out
any new IPs. It also shows the ips on a webpage. Real simple, real quick, but
did the job. I discovered some new ips and new ranges I have to pester ops
about, to make them widen the hole a bit :)

The code can be found here:
[push-me-please](https://github.com/omega/push-me-please).

The current list of IPs:

* 173\.203.140.220
* 173\.203.140.221
* 207\.97.227.233
* 207\.97.227.234
* 207\.97.227.235
* 207\.97.227.236
* 207\.97.227.242
* 207\.97.227.254

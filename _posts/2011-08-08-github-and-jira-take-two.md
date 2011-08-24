---
layout: default
title: "GitHub  Jira"
tags:
    - tech
    - github
    - jira
    - nodejs
    - java
---
It didn't take long for me to notice small stupid things that needed fixing.
Now I have deployed 1.4 of the jira plugin and version N of git-indexer to
production internally.

## git-indexer changes

Yesterday I merged the new comment part, which uses the v3 api github provides
to fetch comments. For now it is pretty stupid, supports a max of 100 comments
pr repo etc, but it is better than the feed, which was bound to miss stuff.
This also gave me the propper HTML of the comments, and it gave me the
opertunity to format things a bit better on the JIRA-side.

## jira-plugin changes

I added gravatars, and format changes. Also I finally bothered googeling the
jackson error I kept getting whenever I changed the JSON feed, so now it does
not die if I decide to add more fields to the JSON. This results in me being
able to more easily update the two pieces independently, which is always great.
I *love* decoupling!

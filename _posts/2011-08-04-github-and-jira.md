---
layout: default
title: "GitHub and JIRA, sitting in a tree"
---
So this one time, at work, we used Subversion for source control managment.
That worked reasonably well when the number of devs was limited, and the number
of geographical locations was limited. Now we have a situation where we have
people in at least 3 different countries, some with quite some distance between
them. Subversion was becomming harder and harder to use for stuff like
branches, which we also wanted to do more of. So when we opened our office in
Bangkok, we decided that anything they needed, we would move to Git. We ended
up using github for git hosting, and that has worked quite well :)

Anyways, we also use JIRA for issue tracking, and since the dawn of man kind,
we have been putting Issue IDs in our commit messages. When using Subversion
all was well, JIRA (and later FishEye) worked flawlessly together with it, and
showed source code changes in JIRA with the issues.

If we had continued to use FishEye, it has eventually gotten some "ok"
git-support, but it is still a lot of stuff to maintain. We ended up not using
FishEye much. It was to cumbersome for code reviews, and too slow for source
code browsing alone. Now we just do code reviews as comments on github.

But that left us without links from JIRA -> github. That wasn't a fun
situation. Github has no search for private repos (and even the public one is
kinda sucky). The only git plugin I found was outdated, and crashed our jira,
needed manual configuration every time we added a repo (and we have 132 now and
counting, so thats a bit of a hassle).

It was time to break out the old java skills and develop a soliution fiting our
needs.

It ended up with two parts (for now :p)

## git-indexer

git-indexer is a node.js based app (my second!). It has several parts in it.

1. The first part polls our repo lists from github once every now and then, checks
   for new repos, adds them to our local DB.
2. The second part clones any repos we don't already have clones of
3. The third part pulls those repos once in a while, gets all the commits and
   looks for bug IDs in the messages. If it finds that, it stores that commit
   as an event assosiated with that Bug-ID
4. The fourth part polls the rss-feed (for now, will rewrite to v3 api later),
   looking for comments. If it finds a comment, it checks if it is connected to
   a commit that is connected to a bug-id. If so, it stores the commit as en
   event as well
5. The last part is a WebServer that takes a bug-id, and returns JSON of all
   events back

## JIRA-plugin (github-jira-plugin)

It's quite simple, it has one config option (WS url), and then adds an issue
tab panel where it lists out all commits and comments.

![GitHub tab panel in action](http://bit.ly/nyoAXQ)

## Summary and code

As you can see it is neither pretty nor complete at the moment. The comments
get fcuked in the RSS from GitHub, and there is no way to get old comments at
the moment. The comment issue can be resolved by rewriting parts of git-indexer
to the new v3 api from github, and that is on the TODO.

The code can be found here: [git-indexer](https://github.com/omega/git-indexer)
and [github-jira-plugin](https://github.com/startsiden/github-jira-plugin)

There are probably several issues with all of the above, but we deployed it to
our prod jira yesterday, and version 1.2 seems to have gotten rid of the worst
problems :p

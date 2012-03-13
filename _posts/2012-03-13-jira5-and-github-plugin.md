---
layout: default
title: JIRA5 and the Github-jira plugin
---
So [JIRA5](http://www.atlassian.com/jira) happened. I still used the old User
class, so I had to update the plugin. Since I only touch Java when I have to,
I decided to fix up a few things while in there, which resulted in quite a few
fixes:

* Split the list into several Actions.

  First of all this is the correct thing to do. Secondly, it allowed the
  commits and comments to be included propperly in the All tab of an issue.

* Adjust the design and markup to be in line with the rest of actions

  JIRA has some weird markup going on there, but at least now our stuff fits
  with the feel of the All tab.

Going forward there are a couple of things that I want to fix:

* Make a connection between github user and jira user.

  Most likely this will be resolved in git-indexer, by having it ask
  a different service for the correct jira (or in our case, crowd) username.

* Group commit comments under the commits

  Most of our users (or at least the ones bothering with feedback), want commit
  comments to show nested beneath the commit it self, not in the timeline like
  it is now. I get bonus points for making this a front end toggle on the
  github tab :p



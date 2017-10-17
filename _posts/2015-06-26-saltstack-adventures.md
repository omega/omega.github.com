---
layout: default
title: SaltStack adventures!
---
I like to dabble in new technology, to try to learn new things, to see what might be useful for me (or companies I work for). Lately operations at my current job (ABC Startsiden in Oslo, Norway) have opted to change to SaltStack for configuration management. I have been meaning to deploy some of my hobby projects on some new servers, so I figured I would try to use SaltStack to manage the configuration of these new servers.

> SaltStack software orchestrates and automates the visualization, build and management of any legacy or modern infrastructure. SaltStack is known as the fastest, most scalable systems and configuration management software for CloudOps, ITOps and DevOps.

This is from their front page. Sounds very promising, doesn’t it? I have some “CloudPRO” resources at a provider in Canada called [cloud at cost](http://cloudatcost.com/). It isn’t a great provider, but they let you buy server resources for a one time fee, which suits me right now :)

# First impression

I followed the [Walk through](http://docs.saltstack.com/en/latest/topics/tutorials/walkthrough.html), and got a salt-master running. I tried to use the gitfs provider, but couldn’t get pygit2 working with my ssh key and github at all. It gave me a “callback failed” error that wasn’t really helpful :( I traced the problem for a while, but I am not a very fluent C/C++ speaker, so I couldn’t figure out where the problem was, beyond that I managed to get libssh to work, but not libgit2. In the end, I just cloned the repo manually, and just pull from it manually for now. I only need to pull on the salt-master, so this isn’t too bad, since I don’t expect to be making too many changes on a day to day basis :)

Once I got the repo cloned and the salt-master and one minion going, I used it to install some of the basic stuff, like vim, on the minion automatically. Next up was getting my personal account set up, with my public key added automatically. This introduced me to pillars, which was also working great.

# Setting up nginx vhosts with sslmate

My next attempt was to get nginx running, and to add a vhost with ssl, and keys from sslmate. I once again turned to pillars, to store my sites there, and then just have the nginx states iterate over the pillar data to add sites to nginx.

## sslmate

I decided to not automate buying keys, and just issue the buy command on my mac, and add the `.key` file to the salt repo. I added a cronjob via salt though, that would download the certificates for me:

{% raw %}
```yaml
sslmate-apt-repo:
  pkgrepo.managed:
    - humanname: SSLMate apt repos
    - name: deb http://packages.sslmate.com/debian jessie main
    - dist: jessie
    - file: /etc/apt/sources.list.d/sslmate.list
    - key_url: https://sslmate.com/apt/jessie/sslmate.gpg
    - require_in:
      - sslmate

sslmate:
  pkg.installed:
    - require:
      - pkgrepo: sslmate-apt-repo
/root/.sslmate:
  file.managed:
    - contents_pillar: sslmate:api_key
    - contents_newline: True
/etc/sslmate/:
  file.recurse:
    - source: salt://sslmate/keys/
sslmate download --all --temp:
  cron.present:
    - identifier: sslmate-download-cron
    - minute: random
    - hour: 0
```
{% endraw %}

This way I just add all the `<domain>.key` files to `base/sslmate/keys/` in my repo, and then the cronjob will pick it up and download the certificates when they are ready. The `--temp` ensures that there will be some self-signed certs available immediately, so nginx can be configured and “work” until the proper certificates are in place.

I did have some struggle with my DNS setup for DNS domain verification, but it worked itself out in the end (my syncing of DNS settings wasn’t working properly to the slaves).

## nginx

Next up was nginx. I already had it running, so I just needed to add vhosts. I added pillars like these:

```yaml
web:
  sites:
    ‘mydomain.com':
      ssl: True
      location: http://localhost:3000 # Should this be specified somewhere else..?
      websockets: True
```

And then I added a state something like this:

{% raw %}
```handlebars
{% for hostname, data in pillar.get(‘web:sites’, {}).items() %}
```
{% endraw %}

This doesn’t work, which surprises me, since `salt-call pillar.get web:sites` works perfectly fine. Turns out the `get` in jinja isn’t the same `get`. Reading the docs explained this, but I didn’t read it carefully enough the first few times :) I ended up with this, which works.

{% raw %}
```handlebars
{% for hostname, data in pillar.get(‘web’).get('sites’, {}).items() %}
```
{% endraw %}

With that added, and then some nginx config file templates using jinja, I had a working setup, where it adds the vhost, and restarts nginx, all with ssl enabled automatically. That’s a victory in my book :)

# Next steps

I also setup mongodb (since my first app uses that, via the [meteor](http://meteor.com) framework), and the next step now is to figure out how to deploy the app itself. My first attempt will be dokku, a heroku-on-docker-thing, with the heroku-meteor build pack.

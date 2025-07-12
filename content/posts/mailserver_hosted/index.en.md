---
title: "Time to Build an Email Server"
subtitle: "self-hosted mailserver by poste.io"
date: 2025-07-05T14:14:45+08:00
lastmod: 2025-07-05T14:14:45+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: "这是一篇关于折腾自建邮箱服务器的碎碎念式记录。"
license: ""
images: ["largepic.jpg"]

tags: ["mailserver","howto","beginner","self-hosted mail","poste.io","DNS","domain email"]
categories: ["notes"]

featuredImage: "largepic.jpg"
featuredImagePreview: "largepic.jpg"

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
  keepStatic: false
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  enable: true
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
---



I’ve got a handful of domains, but only two I really use. For ages I piggy-backed on [Yandex](https://mail.yandex.ru/), yet “nice but paid” plus random bursts of Cyrillic finally wore me down. After years of this dance, I finally snapped: let’s run our own mail server.

<!--more-->

I first tried a single-node Stalwart setup. Spent days banging my head against it—domain verification failing, certificates sulking, nginx reverse-proxy spitting 502s. The frustration meter red-lined; I rage-quit Stalwart and moved on.

Next stop: poste.io. I leaned hard on [香菇肥牛](https://qing.su/)’s [tutorial](https://qing.su/article/poste-io-with-docker-on-linux.html). Man, that guy is good—following his steps shaved off half my gray hairs. If you want a full walkthrough, go read his post. Mine is just a patchy set of footnotes.

## TL;DR of the setup

No hand-holding, just the cheat-sheet. For the step-by-step, see the tutorial above (seriously, it’s great).

### 1. Server prerequisites

You need a VPS that lets you set **Reverse DNS (rDNS)**. I grabbed one from [RackNerd](https://my.racknerd.com/aff.php?aff=15010) because, hey, cheap. Ticket in, rDNS set, done.

Other providers work too — I’m just stingy.

### 2. DNS juggling

This part made me sigh at GoDaddy. Their UI feels clunkier every year, and mail servers need a bazillion DNS records. One typo and you’re re-reading lines until your eyes cross.

If my domains weren’t already parked there, I’d move DNS elsewhere yesterday.

Stick to the tutorial and it’s mostly painless. Then wait for propagation.

Quick crib sheet for any new domains (swap values at will):

| Type | Host | Value | Notes |
|---|---|---|---|
| MX | @ | mx.qing.su | Priority 10; must match your actual mail hostname |
| TXT | @ | v=spf1 a mx -all | |
| TXT | _dmarc | v=DMARC1; p=reject; rua=mailto:postmaster@example.com; ruf=mailto:postmaster@example.com | Change mailto to taste |
| TXT | s2025xxxxxxx._domainkey | k=rsa; p=Your_domain_key_xxxxxx | Paste what the server gives you |

I also set up `autoconfig`, `mta-sts`, `autodiscover`, plus [DNS SRV](https://www.cloudflare.com/learning/dns/dns-records/dns-srv-record/) records. Took forever to grok, but the gist is “help mail clients find the right door”.

### 3. Creating mailboxes

I immediately spun up my personal address and—rookie move—forgot the postmaster account, even though DNS keeps screaming for it. Oops.

Poste.io feels ancient: no 2FA, no modern security sprinkles. Rotate your passwords often, make them long and ugly, and pray.

## My side notes (or regrets?)

Still salty about Stalwart. It’s shinier, safer, feature-packed. But Docker + nginx reverse-proxy? My brain blue-screened. One day I hope some saint publishes a copy-paste guide for Stalwart behind nginx—until then, I’m parked on poste.io.

Running my own mail still gives me a stupid grin every time an email actually lands. Bonus: burner addresses for newsletters keep my main inbox blissfully spam-free.

Not all sunshine, though. Registering with Claude? Instant banhammer. I guess my domain is on some secret naughty list now. Sigh.

Next quest: bolt 2FA onto the webmail. Unless I magically level-up this year, that’s a “someday” project.

Here’s to smooth sailing—for you and for me.
---
title: "Hand-Rolled Password Manager"
subtitle: "A Password Manager Written in Rust — RPaWoMaster"
date: 2025-07-12T16:27:58+08:00
lastmod: 2025-08-16T22:00:00+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["command-line","Rust","password-manager"]
categories: ["tool-development","personal-project","experience-sharing"]

featuredImage: "chris-abney-ugQb6PFLPWI-unsplash.jpg"
featuredImagePreview: "chris-abney-ugQb6PFLPWI-unsplash.jpg"

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

【AI Summary】Under state-owned-enterprise secrecy rules, the author hand-rolled a CLI password manager, RPaWoMaster, in Rust—balancing relative security with convenience, and documented the full journey from Python to a standalone binary.

<!--more-->

## The Origin

Working in a state-owned enterprise, there are confidentiality requirements—mainly because the parent company has them, so they trickle down to us, and we’re forced to treat all kinds of security issues seriously.  
Passwords are the first big thing. Any password manager downloaded from the internet, as long as it has any networking capability, is outright banned for managing company passwords.  
The only approved path is to have the dev team whip one up themselves for everyone to use. Unluckily, none of our developers want to touch this—it’s pure extra work.

I, on the other hand, rely heavily on a password manager for daily password wrangling, so I had no choice but to write a simple [password manager](https://github.com/SidneyLYZhang/pysswordSz) in Python.  
It became my personal tool for managing company passwords. Later, when I saw colleagues still using encrypted Excel files for passwords, I was genuinely shocked—this is even less secure than before.  
Are these still the backbone engineers of our company?

So, I thought I’d promote my little Python password manager. But it’s Python, installable via pip.  
Most regular colleagues don’t even have Python installed, let alone know how to use pip. That pushed me to turn this small program into an executable so it could reach more people.  
I tried every Python packager out there; each one had problems—either the data got mangled, or the binary became ridiculously huge…

In the end, I forced myself to just write a native executable.

Why Rust? Because I haven’t touched Haskell in ages and I’m rusty. So I wavered between Rust and Racket, and Rust just felt smoother to write.  
That’s how this little program came to be.

## Is the Password Manager Secure?

There’s already plenty of discussion about this. From my understanding, today’s password managers can only offer relative security.  
Absolute security means absolute unusability—like quantum cryptography, where you first have to possess an entangled pair of particles.

Within this relative security, absolute isolation is the real deal. But that brings another problem: one password protected by another security code, which can’t be saved anywhere—  
isn’t that just turtles all the way down? So absolute isolation isn’t realistic either. Usability is also something I consider important for a password manager.  
That’s why I always expose the internal encryption and decryption routines as standalone features in every password manager I build, so users can layer on deeper, custom security schemes and choose their own security boundaries.

Additionally, strictly separating the master password from the two-factor authentication password (the one-time password), and rotating both every 90 days, keeps the risks compartmentalized.  
This reduces the chance that all passwords become useless at once.

Does this make it secure? Not entirely. The day RSA can be cracked with ease, this scheme falls apart—that’s the tragedy of passwords; security is always temporary.  
Still, for now, if you forget the core password and the OTP verification code, the passwords inside my manager are truly gone forever.  
So don’t write passwords anywhere visible, and don’t forget your passwords. That matters.

## RPaWoMaster

My password manager is called `RPaWoMaster`—short for Rust PassWord Master.

Functionally, it’s a pure CLI (command-line) program, so you’ll need some basic command-line skills. The usage itself isn’t complicated, though. One thing that sets it apart from other CLI password managers is that the `search` command in `RPaWoMaster` both finds and displays the password, cutting down on the time you spend juggling commands.  
Another difference is the file (or folder) encryption part. This requires a separate password—one password per encrypted file—and is completely independent of the main password-manager system.  
Usually, we use a GPG key for secure encryption; without that GPG key, decryption is impossible, forcing you to bring the key wherever you need to decrypt.  
I found a sneaky workaround: the re-encrypted RSA keypair can be saved alongside the encrypted file, allowing decryption without the core password vault. With this trick, we can nest more layers of encryption and gain more security. On the flip side, decrypting files only requires installing `RPaWoMaster`—convenience maxed out.

One thing still missing in the current `RPaWoMaster` [V0.2.0](https://github.com/SidneyLYZhang/rpawomaster/releases/tag/v0.2.0) release is clipboard support. If you need that, you’ll have to wait a few weeks—I haven’t had the time to polish and build that feature yet.

For more detailed usage, install it and run `RPaWoMaster --help`, or check the README in the [`RPaWoMaster`](https://github.com/SidneyLYZhang/rpawomaster) repo. You can also read the LLM-generated docs on Zread.ai / DeepWiki.com ( [zread]() | [deepwiki](https://deepwiki.com/SidneyLYZhang/rpawomaster) ).  
The Zread.ai docs are older, from V0.1.8—I haven’t figured out how to update them yet—so for fresher docs, look at DeepWiki.

Finally, on installation: for now, I recommend installing Rust first and then using `cargo` to build from source. You can also grab the source directly from GitHub via the latest release.  
I’m working on getting the program into the scoop and homebrew repositories so you can install it with those package managers, but within the next month or two I probably won’t have time to finish that.  
So please bear with me for a bit.

Since this tool is mainly for my colleagues, I just hand them the pre-built binaries along with a PowerShell install script.  
Anyone else wanting to use it will have to jump through a few more hoops for now.

## Some Final Musings

I chipped away at this little tool on and off for quite a while—over a month, from July to August 15. Right now only the core features are done, but it’s already usable.  
During this month, my biggest wish was to start using the new password manager myself as soon as possible.  
Now that I’ve finally achieved that, once I finish the clipboard feature, I can move on to something else.

It feels like these past weeks I’ve been building all sorts of small tools. Besides this password manager, I’m also working on a git plugin that auto-generates messages—whether for commits, tags, or changelogs—  
to quickly solve the message headache. Using git has always annoyed me on this point. Right now I use a tool called opencommit, but npm on Windows isn’t exactly pleasant.  
That’s the next personal tool on my list.

That’s all.

I hope every one of you has a happy day today.

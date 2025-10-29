---
title: "Mathematics and Programming"
subtitle: "Random thoughts on functional programming"
date: 2025-10-29T17:14:43+08:00
lastmod: 2025-10-29T17:20:43+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["Lean4", "Functional Programming", "Random Thoughts"]
categories: ["Random Thoughts"]

featuredImage: "Wan_aicreate.png"
featuredImagePreview: "Wan_aicreate.png"

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: true
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

{{< admonition type=quote title="[_@Source_ Linux.do post](https://linux.do/t/756775/20)" open=true >}}
Researching haskell, lean4, for mathematical abstraction.
{{< /admonition >}}

In a post about what programming language to learn, I saw someone mentioned Lean4 and Haskell. Honestly, my personal feeling is that unless you're in mathematics, don't even consider Lean4 first.
Lean4 can indeed be used as a general programming language, but overall code management and usage are somewhat unsatisfactory.
But as a formal verification tool for mathematical proofs (proof assistant), these are sufficient. In comparison, for understanding functional programming,
or implementing mathematical proofs in programs, Haskell might be more suitable, though it's also quite experimental.
There's also Racket (a more modern Lisp), which is also very meaningful for exploration and learning in functional programming.

Speaking of Lean4, we can't avoid Curry-Howard isomorphism (CH isomorphism for short). In Lean4, CH isomorphism is its foundation, but for most functional programming languages,
λ-calculus (lambda calculus) might be more fundamental. My personal feeling is that most functional programming languages have more complete support for λ-calculus, at most they also have good support for currying,
but when it comes to CH isomorphism, most languages only use it as the underlying type system rather than direct language API.
Moreover, CH isomorphism directly provides more convenience for "formal verification of mathematical proofs". Although CH isomorphism can also be very powerful in actual programming,
the mathematical thinking it requires is also more complex.

Relatively speaking, λ-calculus is much, much simpler. In terms of understanding, just having middle school math foundation is enough.
Currying, which is based on λ-calculus, is also not complicated. Precisely because this part is simpler, most functional programming languages also have comprehensive support in this area.
Whether it's Rust, Haskell, or Python, as long as they have functional programming capabilities, they can almost all handle it.

Back when I was still in school (20 years ago), on the path from mathematics → programming (with emphasis on mathematics),
for college students at that time (like me), maybe FORTRAN was used by more senior professors, younger teachers preferred Matlab or R,
but what schools taught more was C and Java. Functional languages were mostly introduced content in classes, like Lisp, OCaml, etc.
This was also the inertia of school education - FORTRAN's excellent mainframe computing capability was a necessary foundation for complex calculations back then, numerical computing was just a small hill before entering the door;
depending on the improvement of personal computer performance, Matlab and R could also handle some complex calculations and verification work on personal computers.
Especially R, not just for computing capability, but more for the figures in published papers - it was already like that back then.
But for beginners, school curriculum still preferred the long-standing C and Java.

Shifting to the present time and space, does a mathematician need to learn programming? With increasingly complex proofs,
increasingly specialized fields, mathematics itself has almost lost commonality across different subfields, even with the same foundation, the constructed prospects are still vastly different.
So, to move forward better, perhaps Lean4 is a necessary choice. Not necessarily to learn it now, but probably will need to learn it in the future.
If it's in applied fields, that is, mathematical applications, C or FORTRAN, these two antiques, might still be a hurdle - as far as I know,
there are still a large number of basic programs written in these two languages.
In industrial fields, Python is now very trendy, and Rust is claiming to rewrite everything. These are also worth learning choices.

If you must have a first choice?

Let me recommend, probably still Haskell - this is the best bridge connecting mathematics and programming;
if you're doing math competitions, prioritize Python, after all Python's ecosystem is richer, suitable for finding solutions to problems;
if you're already proficient in some programming language before university, then why not try the old tree with new branches - Racket,
or directly start learning Lean4, break through inherent cognition, don't hesitate.

Of course, these are just the random thoughts of an aging mathematics "youth".

Why not learn Coq but learn Lean4? Lean4's Mathlib has more and more unified mathematical domains, with more comprehensive support for mathematical symbols (types), this is the first reason;
second, Coq is still much worse than Lean4 in proof strategies, programs are more tiring to write, and readability is slightly insufficient.
Of course, it's not that you can't learn it - for example, if your advisor uses Coq, for graduation you have to learn it, right?

Oh right, remembered something interesting. Haskell Curry and William Alvin Howard successively perfected this logical isomorphism,
so this structure is called Curry-Howard isomorphism.
The Haskell programming language also took this name to commemorate Haskell Curry's contributions.
Currying is also to commemorate Haskell Curry, who promoted the mathematical logic of chained single-parameter functions to λ-calculus.
Curry is simply the earliest founder of functional programming languages, right? Or perhaps, the big devil of functional programming?
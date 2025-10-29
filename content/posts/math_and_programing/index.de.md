---
title: "Mathematik lernen und programmieren"
subtitle: "Gedanken zur funktionalen Programmierung"
date: 2025-10-29T17:14:43+08:00
lastmod: 2025-10-29T17:20:43+08:00
draft: false
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["Lean4", "Funktionale Programmierung", "Gedanken"]
categories: ["Gedanken"]

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

{{< admonition type=quote title="[_@Quelle_ Linux.do Thread](https://linux.do/t/756775/20)" open=true >}}
Erforschung von Haskell und Lean4 für mathematische Abstraktion.（研究haskell,lean4,用于数学抽象。）
{{< /admonition >}}

In einem Thread darüber, welche Programmiersprache man lernen sollte, sah ich, dass jemand Lean4 und Haskell erwähnte. Persönlich glaube ich, dass man Lean4 nicht in Betracht ziehen muss, es sei denn, man studiert Mathematik. Lean4 kann zwar als allgemeine Programmiersprache betrachtet werden, aber das gesamte Code-Management und die Nutzung sind nicht ganz zufriedenstellend. Als formale Verifizierung für mathematische Beweise (Beweisassistent) ist es jedoch ausreichend. Im Vergleich dazu ist Haskell zum Verständnis der funktionalen Programmierung oder der Implementierung mathematischer Beweise in Programmen besser geeignet, obwohl es ebenfalls sehr experimentell ist. Außerdem gibt es Racket (moderneres Lisp), das auch sehr praktisch und lehrreich für die Erforschung der funktionalen Programmierung ist.

Wenn es um Lean4 geht, kommt man nicht am Curry-Howard-Isomorphismus (kurz CH-Isomorphismus) vorbei. In Lean4 ist der CH-Isomorphismus die Grundlage, aber für die meisten funktionalen Programmiersprachen ist der λ-Kalkül (Lambda-Kalkül) wahrscheinlich grundlegender. Ich persönlich habe das Gefühl, dass die meisten funktionalen Programmiersprachen den λ-Kalkül besser unterstützen, höchstens auch noch eine gute Unterstützung für Currying bieten, aber beim CH-Isomorphismus kann man sagen, dass die meisten Sprachen ihn als unterliegende Ebene des Typsystems und nicht als direkte Sprach-API verwenden. Außerdem bietet der CH-Isomorphismus direkt mehr Komfort für die "formale Verifizierung mathematischer Beweise". Obwohl der CH-Isomorphismus auch in der tatsächlichen Programmierung sehr leistungsfähig sein kann, ist das mathematische Denken, das er erfordert, komplexer.

Im Vergleich dazu ist der λ-Kalkül viel, viel einfacher. Zum Verständnis reicht die mathematische Grundlage aus der Mittelschule aus. Das auf dem λ-Kalkül basierende Currying ist ebenfalls nicht kompliziert. Gerade weil dieser Teil einfacher ist, unterstützen die meisten funktionalen Programmiersprachen dies auch umfassender. Ob Rust, Haskell oder Python - solange sie funktionale Programmierfähigkeiten haben, können sie dies fast alle bewältigen.

Als ich noch zur Schule ging (vor 20 Jahren), auf dem Weg von Mathematik zu Programmierung (mit Schwerpunkt auf Mathematik), nutzten damals wahrscheinlich mehr Professoren FORTRAN, während jüngere Lehrer Matlab oder R bevorzugten, aber die Universitäten lehrten hauptsächlich C und Java. Funktionale Sprachen waren meist nur Einführungsthemen im Unterricht, wie Lisp, OCaml und ähnliches. Dies ist auch eine Art Trägheit im Bildungssystem. Die exzellente Rechenleistung von FORTRAN auf Großrechnern war damals die notwendige Grundlage für komplexe Berechnungen, und numerische Berechnungen waren nur ein kleiner Hügel vor dem Einstieg; dank der verbesserten Leistung von Personalcomputern konnten Matlab und R auch komplexe Berechnungen und Verifizierungsarbeiten auf dem eigenen Computer bewältigen. Besonders R war nicht nur wegen der Rechenleistung beliebt, sondern vor allem auch für die Bilder in wissenschaftlichen Veröffentlichungen - schon damals war das so. Aber für Anfänger bevorzugten die Lehrpläne der Universitäten weiterhin das seit vielen Jahren etablierte C und Java.

Wenn wir uns in die heutige Zeit versetzen, muss ein Mathematiker programmieren können? Die Beweise werden immer komplexer, die Fachgebiete immer spezialisierter, und die Lücke zwischen verschiedenen Teilgebieten der Mathematik wird so groß, dass sie die gemeinsame Grundlage fast untergräbt. Auch wenn die Grundlagen gleich sind, sind die konstruierten Perspektiven dennoch extrem unterschiedlich. Um also besser voranzukommen, ist Lean4 möglicherweise eine notwendige Wahl. Nicht unbedingt jetzt lernen, aber später vielleicht doch. Wenn es um Anwendungsbereiche geht, also angewandte Mathematik, sind C oder FORTRAN, diese beiden Antiquitäten, immer noch eine Hürde. Soweit ich weiß, werden heute immer noch viele grundlegende Programme in diesen beiden Sprachen geschrieben. Im industriellen Bereich ist Python jetzt sehr modern, und Rust behauptet, alles neu schreiben zu wollen. Auch das sind lohnende Lernmöglichkeiten.

Wenn es eine erste Wahl geben muss?

Wenn ich eine Empfehlung abgeben dürfte, wäre es wahrscheinlich immer noch Haskell, die beste Brücke zwischen Mathematik und Programmierung; wenn du an Mathematikwettbewerben teilnimmst, lerne lieber zuerst Python, da das Python-Ökosystem reicher ist und sich besser zur Suche nach Problemlösungen eignet; wenn du vor dem Studium bereits bestimmte Programmiersprachen beherrschst, dann probiere doch mal den alten Baum mit neuen Zweigen, Racket, oder beginne direkt mit dem Lernen von Lean4. Festgefahrene Denkweisen müssen durchbrochen werden, zögere nicht.

Natürlich sind das alles nur die Gedanken eines bereits gealterten mathematischen "Jünglings".

Warum also nicht Coq lernen, sondern Lean4? Erstens deckt Lean4 Mathlib mehr und einheitlichere mathematische Bereiche ab und unterstützt mathematische Symbole (Typen) umfassender. Zweitens ist Coq in den Beweisstrategien weit schlechter als Lean4, das Programmieren ist anstrengender und die Lesbarkeit etwas mangelhafter. Natürlich heißt das nicht, dass man es nicht lernen kann. Wenn dein Professor zum Beispiel Coq verwendet, musst du es für den Abschluss lernen, nicht?

Oh, richtig, mir fällt noch etwas Interessantes ein. Haskell Curry und William Alvin Howard haben diesen logischen Isomorphismus nacheinander vervollkommnet, daher wird diese Struktur Curry-Howard-Isomorphismus genannt. Die Programmiersprache Haskell wurde ebenfalls zu Ehren von Haskell Curry benannt. Auch Currying ist zu Ehren von Haskell Curry benannt, der die mathematische Logik von verketteten Einparameterfunktionen auf den λ-Kalkül ausweitete. Curry ist praktisch der früheste Begründer funktionaler Programmiersprachen, oder? Oder der große Teufel der funktionalen Programmierung?
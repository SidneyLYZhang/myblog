---
title: "Handgebastelter Passwort-Manager"
subtitle: "Ein in Rust geschriebener Passwort-Manager – RPaWoMaster"
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


## Der Ursprung

Ich arbeite in einem zentralen Staatsunternehmen, dort gibt es Geheimhaltungsvorschriften – hauptsächlich, weil die übergeordnete Einheit welche hat, und diese Vorgaben setzen sich bei uns fort, weshalb wir alle möglichen Sicherheitsfragen ernst nehmen müssen.  
Passwörter sind dabei das Erste und Wichtigste. Alle heruntergeladenen Passwort-Manager mit auch nur irgendeiner Netzwerkfunktion werden pauschal für die Verwaltung von Firmenpasswörtern verboten.  
Die einzige erlaubte Lösung: die Entwickler sollen selbst eins bauen, das jeder benutzen kann. Niemand aus der Entwicklung will sich damit befassen – ist ja zusätzliche Arbeit.

Ich hingegen bin ziemlich abhängig von einem Passwort-Manager im Alltag, also blieb mir nichts anders übrig, als in Python einen simplen [Passwort-Manager](https://github.com/SidneyLYZhang/pysswordSz) zu schreiben.  
Damit verwaltete ich meine persönlichen Firmenpasswörter. Als ich später Kollegen dabei erwischte, immer noch verschlüsselte Excel-Tabellen zu nutzen, war ich echt schockiert – das ist noch unsicherer als früher.  
Sind das noch die tragenden Säulen unserer Entwicklung?

Also dachte ich, ich werbe für meinen Python-Manager. Doch das kleine Programm ist Python, installierbar per pip.  
Viele normale Kollegen haben Python nicht einmal installiert. Darum überlegte ich, das Programm in eine ausführbare Datei zu packen, damit es in mehr Umgebungen läuft.  
Ich probierte alle Python-Packager aus; keiner funktionierte richtig – entweder krachten sie an kaputten Daten oder produzierten riesige Binaries…

Am Ende zwang ich mich, ein natives Programm zu schreiben.

Warum Rust? Weil ich Haskell ewig nicht angerührt habe und aus der Übung bin. Also schwankte ich zwischen Rust und Racket – Rust fühlte sich einfach flüssiger an.  
Daraus entstand dieses kleine Programm.

## Ist der Passwort-Manager sicher?

Darüber wird schon viel diskutiert. Meiner Einschätzung nach garantieren heutige Passwort-Manager nur relative Sicherheit.  
Absolute Sicherheit bedeutet absolute Unbenutzbarkeit – wie Quantenkryptografie, bei der man erst ein verschränktes Teilchenpaar besitzen muss.

Innerhalb dieser relativen Sicherheit ist absolute Isolation die einzig echte. Doch das führt zum nächsten Problem: ein Passwort wird durch einen weiteren Sicherheitscode geschützt, der nirgends gespeichert werden darf –  
ist das nicht ein unendliches In-einander-Verschachteln? Absolute Isolation ist also auch unrealistisch. Bedienkomfort ist für mich ebenfalls wichtig.  
Deshalb lasse ich in jedem meiner Passwort-Manager die internen Verschlüsselungs- und Entschlüsselungsroutinen separat als Feature außen, sodass manuell tiefere, eigene Sicherheitskonzepte eingebaut werden können – jeder wählt seine eigene Sicherheitsgrenze.

Zusätzlich trenne ich Master-Passwort und Zwei-Faktor-Einmalpasswort strikt und rotiere beide alle 90 Tage, um Risiken zu separieren.  
So sinkt die Wahrscheinlichkeit, dass auf einen Schlag alle Passwörter unbrauchbar werden.

Ist das nun sicher? Nicht wirklich. Sobald RSA mühelos geknackt werden kann, bricht dieses Schema zusammen – das ist das Traurige an Passwörtern: Sicherheit ist immer nur temporär.  
Doch vorerst: vergisst du das Kernpasswort und den OTP-Bestätigungscode, sind die darin gespeicherten Passwörter für immer weg.  
Also: nirgends sichtbar Passwörter notieren und nicht das Passwort vergessen. Das ist wichtig.

## RPaWoMaster

Mein Passwort-Manager heißt `RPaWoMaster` – Kurzform von Rust PassWord Master.

Funktional ist es ein reines CLI-Programm; du brauchst ein bisschen Kommandozeilen-Können, aber die Nutzung ist eigentlich simpel. Eine Besonderheit gegenüber anderen CLI-Managern: der `search`-Befehl in `RPaWoMaster` sucht und zeigt das Passwort in einem Schritt – weniger Hin-und-Her in der Shell.  
Ein weiterer Unterschied ist die Datei- (und Ordner-)Verschlüsselung. Hierfür braucht man ein separates Passwort – eine Datei, ein Passwort – und dieser Teil ist komplett unabhängig vom Haupt-Passwort-Manager.  
Normalerweise benutzt man einen GPG-Key zur sicheren Verschlüsselung; ohne diesen Schlüssel kann man nichts entschlüsseln und muss den Key überallhin mitnehmen.  
Ich habe einen Trick gefunden: das erneut verschlüsselte RSA-Keypair kann zusammen mit der verschlüsselten Datei abgelegt werden und funktioniert ohne die Kern-Datenbank. So lassen sich weitere Verschlüsselungsschichten einbauen und die Sicherheit erhöhen. Gleichzeitig reicht die Installation von `RPaWoMaster`, um Dateien zu entschlüsseln – Komfort 100 %.

Eines fehlt in der aktuellen `RPaWoMaster`-Version [V0.2.0](https://github.com/SidneyLYZhang/rpawomaster/releases/tag/v0.2.0) noch: die Zwischenablage-Funktion. Wer die braucht, muss noch ein paar Wochen warten – ich habe momentan keine Zeit, das zu optimieren und einzubauen.

Ausführlichere Nutzungshinweise: nach der Installation einfach `RPaWoMaster --help` aufrufen oder die README im [`RPaWoMaster`](https://github.com/SidneyLYZhang/rpawomaster)-Repo lesen. Alternativ gibt es LLM-generierte Dokumentationen auf Zread.ai / DeepWiki.com ( [zread]() | [deepwiki](https://deepwiki.com/SidneyLYZhang/rpawomaster) ).  
Die Zread.ai-Doku ist älter (V0.1.8) – ich weiß noch nicht, wie man dort aktualisiert – also für neuere Infos besser DeepWiki nutzen.

Zur Installation: im Moment empfehle ich, Rust zu installieren und dann via `cargo` aus den Quellen zu bauen. Oder man lädt die Quellen direkt von GitHub herunter; die neueste Version findet sich unter Releases.  
Ich arbeite daran, das Programm in die scoop- und homebrew-Repositories zu bringen, damit man es mit diesen Paketmanagern installieren kann – aber in den nächsten ein, zwei Monaten werde ich dafür vermutlich keine Zeit haben.  
Also bitte noch ein wenig Geduld.

Da das Programm hauptsächlich für meine Kollegen gedacht ist, kopiere ich ihnen fertige Binaries samt PowerShell-Install-Skript einfach rüber.  
Alle anderen müssen momentan einen kleinen Umweg in Kauf nehmen.

## Ein paar abschließende Gedanken

An diesem kleinen Werkzeug habe ich stückchenweise lange gearbeitet – gut einen Monat, von Juli bis zum 15. August. Im Moment sind nur die Kernfunktionen fertig, aber es ist benutzbar.  
In diesem Monat war mein größter Wunsch, endlich selbst mit dem neuen Passwort-Manager arbeiten zu können.  
Nun ist dieser Wunsch in Erfüllung gegangen; sobald die Zwischenablage fertig ist, kann ich mich anderen Dingen widmen.

In den letzten Wochen baue ich ohnehin eine ganze Reihe Kleinwerkzeuge: neben dem Passwort-Manager arbeite ich an einem Git-Plugin, das automatisch Nachrichten erzeugt – egal ob Commits, Tags oder Changelogs –  
um das Nachrichten-Problem blitzschnell zu lösen. Git nervt mich genau daran; momentan nutze ich opencommit, aber npm unter Windows ist auch nicht gerade angenehm.  
Das ist mein nächstes persönliches Projekt.

Das war’s.

Ich wünsche euch allen einen schönen Tag.
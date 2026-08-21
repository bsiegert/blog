---
title: "Jiu Jitsu mit dem Quellcode"
where: "FrOSCon 2026"
date: 2026-08-15T10:00:00+02:00
description: >
  Wollen wir mehr Leute, die an Open Source mitarbeiten? Ja klar! Aber da ist
  schon das erste Hindernis: git. Um git produktiv zu nutzen, muss man viele
  Konzepte lernen — den Index, Branches, Commits, Rebases und eine Vielzahl
  inkompatibler Workflows. Wenn mal was schiefgeht, ist der Rat oft alles zu
  löschen und von vorne anzufangen.

  Aber es geht auch einfacher: Ju-Jutsu (jj) ist eine neue Versionsverwaltung,
  die mit minimalen Konzepten auskommt und leicht zu lernen ist. (Bei mir hat
  es gerade mal einen Nachmittag gedauert, um mich in jj zurechtzufinden!) jj
  ist auf das Entwickeln an einem zentralen Repo optimiert. Als Storage-Format
  benutzt es git — man kann seinen Klon einfach zu jj „upgraden“, ohne dass die
  anderen Developer das auch müssen. Da jede Aktion sicher rückgängig gemacht
  werden kann, gibt es keine Sackgassen mehr. Und Branches brauchen keine
  Namen, so kann man kinderleicht auch mal etwas einfach ausprobieren. In
  diesem Talk möchte ich euch jj als Versionskontrolle kurz vorstellen und
  vorführen, wie entspannt Versionskontrolle sein kann.
---

Dieser Vortrag hat zwei Teile: Eine Einführung in jj, die Versionsverwaltung,
und einen etwas philosophischen Teil.

# 1

Es gibt eine schleichende Krise in Open Source Maintenance. Man könnte jetzt
das Bild aus XKCD zeigen, “someone thanklessly maintaining this bit of
infrastructure”. Es gibt wie ein Open Source der zwei Geschwindigkeiten: auf
der einen Seite die gehypten Projekte mit 20000 Sternen auf GitHub, hundert
Pull Requests am Tag und oft professionellen Maintainern. Auf der anderen Seite
ist ein grosser Teil von Open Source Einmann-Projekte.

Und dann passiert sowas wie bei XZ: jemand kommt und sagt, er/sie möchte
mitmachen und helfen, übernimmt Aufgaben und baut dann irgendwann Schadcode
ein.

Oder dieses Projekt [Name?], das bei “wichtigen” Projekten aushilft, und sich
dann das Recht vorbehält den Laden einfach zu übernehmen – sie nennen das
“Maintainer of last resort”.

Ich bin nicht hier, um euch zu sagen, KI sei die Lösung, denn das ist sie nicht.

Ein glaubhafter Weg, um hier rauszukommen, könnte sein, mehr User zu Helfern zu
machen. Manche Projekte haben zB Tickets als “Good first contribution”
markiert, das ist schon ganz gut. Über Community Management gibt es eigene
Konferenzen, auch einen hervorragenden Devroom auf der FOSDEM.

Jedenfalls: ein Hindernis für einen Neuling ist auf jeden Fall: git.

Die Verwendung eines VCS ist eine gute Unterscheidung zwischen IT-Profi und
nicht-Profi, insofern hat ein Newcomer vielleicht noch nie ein VCS benutzt.
Aber schlimmer noch, die typischen Hürden bei git sind:

- Vokabular (man muss die Verben wie Vokabeln lernen, sprechend ist da nichts)
- Die Angst etwas kaputt zu machen: Oh nein, ich habe irgend einen Befehl
  ausgeführt, jetzt werde ich alles löschen und einen neuen Clone machen, damit
  ich hier wieder rauskomme!
- so viel State!
  - Das Repository upstream
  - Das Repository auf meinem Computer
  - Branches mit irgend einem Namen – oder versehentlich ohne Namen, der
    gefürchtete “Detached Head”, den git dann irgendwann wegwirft
  - Der Index (alles was mit git add hinzugefügt wurde) – warum muss ich ein
    geändertes File adden???
  - und meine working copy
- So viele Workflows – soll ich mergen oder nicht? Rebase oder nicht?
- Konflikte!
- History editieren, Hilfe!

# 2

Und jetzt kommen wir zu einer Alternative zu git, die genau hier ansetzt und
die Punkte oben entscheidend verbessert: jj.

jj ist ein neues VCS, gezielt optimiert um Entwickler produktiver zu machen. Es
konzentriert sich ganz klar auf den Einsatz an deinem Computer, während das
Master Repository woanders liegt. Der Clou: es benutzt git als
darunterliegenden storage layer, so dass es perfekt mit git integriert. Man
kann auch einen existierenden git clone zu einem jj-Repo machen. (Es gibt
plugins für andere Repo-Formate, aber git ist der Default).


Der Hauptentwickler, Martin von Zweigbergk, war vorher einer der
Haupt-Contributor zu Mercurial (hg), von daher ist das UI von jj stark an
Mercurial angelehnt – das ist gut. Naja, das Vokabular muss man trotzdem
lernen.

Meines Erachtens sind die wichtigsten Vorteile von jj:
- man kann nichts kaputt machen, weil man jede Operation rückgängig machen kann
  (undo)
- man kann frei mit Commits herumspielen, muss man aber nicht
- es gibt eine begrenzte Zahl von Workflows, eigentlich nur zwei
- Konflikte kann man erst mal ignorieren – aber wie im echten Leben kann das zu
  Problemen führen!

# 3

Jetzt gehts los!

- `jj git init` legt ein neues, leeres Repo an -- oder konvertiert einen
  existierenden git-Checkout in ein jj-Repo.
- `jj git clone https://github.com/x/y` klont ein existierendes Repo.

Warum `jj git`? Das sind Kommandos des git-Plugins. Eines Tages wird es
vielleicht auch noch andere Repo-Formate geben. (Ich weiss noch eins, aber das
ist nicht open source).

Die vier wichtigsten Kommandos:

- `jj new` – neuer Commit 
- `jj edit` – in einen Commit wechseln
- `jj status` (`jj st`) – was ist im aktuellen Commit los?
- `jj log` – wie sieht es in meinem Repo aus?


{{< figure src="/talks/froscon2026/jj-log.png" title="Ausgabe von jj log" width="80%" >}}

Was sehen wir? 

- grafische Darstellung der Commits, die Hauptlinie ist vertikal durchgehend.
- Der aktuelle Commit ist das `@`
- Der aktuelle Commit ist leer und hat keine Beschreibung! Das kommt raus, wenn
  man `jj new` ausführt
- Jeder Commit hat eine ID am Anfang (mit Buchstaben) und am Ende (hex). 
  - Die vordere wird von jj benutzt und bleibt immer gleich, wenn man den
    Commit rebased oder sonst wie verschiebt oder bearbeitet.
  - Die hintere ist der git-Hash. Er ändert sich bei jeder Operation.
  - Man braucht nur die hervorgehobenen Buchstaben/Ziffern, um den Commit
    eindeutig zu beschreiben.
- Autor und Timestamp
- Die Commits, die schon publiziert sind (also zB auf GitHub) sind *immutable*
  und haben ein Karo vorn.
- Lokale Commits, die man noch bearbeiten kann, haben einen Kringel.
- Die meisten Commits vom Upstream werden hier nicht gezeigt, man kann das aber
  ändern.
- Einige Commits haben einen lila String. Das ist ein Bookmark oder ein Tag.
  (hier steht sql, aber es könnte auch main sein)

Der Graph hat alle Commits. Orphaned Commits, detached head, das ganze Zeugs
gibt es nicht. Jedes Ende ist ein Head, wie in Mercurial.

OK, ich möchte loslegen!

Zunächst: man kann keinen Branch auschecken wie in git. Denn es gibt in dem
Sinn keine Branches und auch kein Checkout! Statt dessen erstellt man einen
neuen, leeren Commit auf dem Bookmark, das einen interessiert.

```
jj new main
```

Profitipp: jetzt könnt ihr direkt die Beschreibung verfassen, und zwar vor einer Änderung!

```
jj describe
```

Oder direkt mit

```
jj new main -m "Create README"
```

Jetzt seid ihr *in* dem "Create README"-Commit. Jede Änderung, die ihr macht
(sogar neue Verzeichnisse!), landet in dem Commit.

```
bsiegert@hive11 ~/s/jjtest> vim README.md
bsiegert@hive11 ~/s/jjtest> jj st
Working copy changes:
A README.md
Working copy  (@) : wmrrxwmo 1f1d1d42 Create README
Parent commit (@-): zzzzzzzz 00000000 (empty) (no description set)
```

OK, jetzt bin ich fertig mit dem Commit, was jetzt?

```
jj new
```

Und wir sind im nächsten Commit.

Was, wenn mir diese Art zu arbeiten nicht geheuer ist? Wenn ich nicht alle
meine Tempfiles direkt in dem Commit möchte – sondern nur die Dateien, die ich
selbst hinzufügen will?

Dann macht man einfach noch einen leeren Commit ohne Beschreibung und arbeitet darin. Alle Änderungen, die in den "richtigen" Commit sollen, werden mit `jj squash` "nach oben geschoben". Standardmässig schiebt `squash` nach `@-`, dem Parent.

```
jj squash -i
#oder
jj squash Dateiname
```

```
bsiegert@hive11 ~/s/jjtest> jj st
Working copy changes:
A a
A b
A c
Working copy  (@) : spsznnuo 5132f41e (no description set)
Parent commit (@-): wmrrxwmo 1f1d1d42 Create README
bsiegert@hive11 ~/s/jjtest> jj squash a
Rebased 1 descendant commits
Working copy  (@) now at: spsznnuo 8af78aef (no description set)
Parent commit (@-)      : wmrrxwmo 6430a633 Create README
```

Beachtet mal das "Rebased 1 descendant commits". Rebases sind automatisch!
`jj rebase` gibt es, das benutzt man um Commits manuell zu schieben.

Aber was, wenn es Probleme gibt?

```
bsiegert@hive11 ~/s/jjtest> jj
@  spsznnuo bsiegert@example.com 2026-08-10 17:38:49 8af78aef
│  (no description set)
○  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 6430a633
│  Create README
│ ○  pznqpzpn bsiegert@example.com 2026-08-10 17:41:48 d95312e8
├─╯  (no description set)
◆  zzzzzzzz root() 00000000
bsiegert@hive11 ~/s/jjtest> jj rebase -r p -d w
Rebased 1 commits to destination
New conflicts appeared in 1 commits:
  pznqpzpn c17caa47 (conflict) (no description set)
Hint: To resolve the conflicts, start by creating a commit on top of
the conflicted commit:
  jj new pznqpzpn
Then use `jj resolve`, or edit the conflict markers in the file directly.
Once the conflicts are resolved, you can inspect the result with `jj diff`.
Then run `jj squash` to move the resolution into the conflicted commit.
bsiegert@hive11 ~/s/jjtest> jj
@  spsznnuo bsiegert@example.com 2026-08-10 17:38:49 8af78aef
│  (no description set)
│ ×  pznqpzpn bsiegert@example.com 2026-08-10 17:42:06 c17caa47 (conflict)
├─╯  (no description set)
○  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 6430a633
│  Create README
◆  zzzzzzzz root() 00000000
```

Ich kann weiter arbeiten wie bisher. Den Konflikt kann ich auch später noch lösen.
Aber vielleicht habe ich jetzt Panik. Ich will wieder zurück!

```
jj op log
@  ca50936eafb5 bsiegert@hive11.bentsukun.ch default@ 2 minutes ago, lasted 7 milliseconds
│  rebase commit d95312e8a6837da4903796842c8cba8b795a071d
│  args: jj rebase -r p -d w
○  ffdda5369496 bsiegert@hive11.bentsukun.ch default@ 2 minutes ago, lasted 6 milliseconds
│  rebase commit b4f9088027f7f90ffa8dd967ebe30175dd7b888e
│  args: jj rebase -r p -d zzz
○  6f3bd2f82a57 bsiegert@hive11.bentsukun.ch default@ 2 minutes ago, lasted 2 milliseconds
│  edit commit 8af78aef3ae49caf800142033d334a47e3f0970b
│  args: jj edit s
○  0d684df2ecd6 bsiegert@hive11.bentsukun.ch default@ 3 minutes ago, lasted 5 milliseconds
│  rebase commit a13abc5140274f1884a3bef66145ec765bf18b41 and descendants
│  args: jj rebase -d w
○  aefe0f80d86e bsiegert@hive11.bentsukun.ch default@ 3 minutes ago, lasted 3 milliseconds
│  snapshot working copy
│  args: jj rebase -d w
○  f837a72cd61a bsiegert@hive11.bentsukun.ch default@ 4 minutes ago, lasted 6 milliseconds
│  new empty commit
│  args: jj new zzz
○  df15ce90f30a bsiegert@hive11.bentsukun.ch default@ 5 minutes ago, lasted 12 milliseconds
│  squash commits into 1f1d1d42d284ed3e13cd5098403f7c2d50c6bd6e
│  args: jj squash a
○  9350afbe4a77 bsiegert@hive11.bentsukun.ch default@ 7 minutes ago, lasted 5 milliseconds
│  snapshot working copy
│  args: jj squash -i
○  b1765788c6a3 bsiegert@hive11.bentsukun.ch default@ 7 minutes ago, lasted 6 milliseconds
│  new empty commit
│  args: jj new
○  cd8aa17b4eaf bsiegert@hive11.bentsukun.ch default@ 9 minutes ago, lasted 2 milliseconds
│  abandon commit def61c723a8bc74b7e012875b95cf9e8af6e850b
│  args: jj abandon wt
○  16633ae2fb85 bsiegert@hive11.bentsukun.ch default@ 13 minutes ago, lasted 4 milliseconds
│  snapshot working copy
│  args: jj status
○  ed43d7c129e7 bsiegert@hive11.bentsukun.ch default@ 13 minutes ago, lasted 5 milliseconds
│  snapshot working copy
│  args: jj log
○  3f21fe70cad0 bsiegert@hive11.bentsukun.ch default@ 13 minutes ago, lasted 3 milliseconds
│  snapshot working copy
│  args: jj status
○  1603ec92852a bsiegert@hive11.bentsukun.ch default@ 15 minutes ago, lasted 4 milliseconds
│  rebase commit 85177d107726a4aaa9a4f5509c8d11dfcbea3402
│  args: jj rebase -r @ -d z
○  b385b7610379 bsiegert@hive11.bentsukun.ch default@ 16 minutes ago, lasted 4 milliseconds
│  new empty commit
│  args: jj new -m 'Create README'
○  fd2b0aa3f3ce bsiegert@hive11.bentsukun.ch 16 minutes ago, lasted 5 milliseconds
│  add workspace 'default'
○  000000000000 root()
```

Das hier ist das Log aller Dinge, die ich getan habe. Der Rebase war schlecht, also weg damit!

```
> jj undo
Undid operation: ca50936eafb5 (2026-08-10 17:42:06) rebase commit d95312e8a6837da4903796842c8cba8b795a071d
Restored to operation: ffdda5369496 (2026-08-10 17:41:48) rebase commit b4f9088027f7f90ffa8dd967ebe30175dd7b888e
Existing conflicts were resolved or abandoned from 1 commits.
bsiegert@hive11 ~/s/jjtest> jj
@  spsznnuo bsiegert@example.com 2026-08-10 17:38:49 8af78aef
│  (no description set)
○  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 6430a633
│  Create README
│ ○  pznqpzpn bsiegert@example.com 2026-08-10 17:41:48 d95312e8
├─╯  (no description set)
◆  zzzzzzzz root() 00000000
```

Man kann einen Commit mit Konflikten weiter schieben, es kann sein, dass die Konflikte dabei sogar verschwinden.

Wenn ich jetzt doch manuell resolven möchte? Oben stand es schon, `jj resolve`
oder einfach einen Texteditor aufmachen. resolve kann übrigens viele 3-way
Merge-Tools benutzen, oder ein eingebautes Tool ähnlich dem interaktiven Merge.

- vimdiff (in vim)
- kdiff3
- meld
- Mergiraf (das kann Syntax und daher viel automatisch mergen)

Zeit für den ersten Push!

Ich habe ein leeres Repo erstellt und pushe jetzt zum ersten Mal.

```
jj git remote add origin git@github.com:bsiegert/friendly-palm-tree.git

jj bookmark create main
Created 1 bookmarks pointing to pznqpzpn a1aa73b5 main | (no description set)

jj git push
Warning: Refusing to create new remote bookmark main@origin
Hint: Run `jj bookmark track main --remote=origin` and try again.
Nothing changed.

jj bookmark track main --remote=origin
Started tracking 1 remote bookmarks.

jj git push
Warning: Won't push bookmark main: commit a1aa73b5706d has no description
```

Ah! Ich habe das Bookmark versehentlich in den leeren Commit geschoben. Es soll natürlich eins höher.

```
jj bookmark move main --to @- --allow-backwards
Moved 1 bookmarks to wmrrxwmo 6430a633 main* | Create README

jj git push
Changes to push to origin:
  bookmark: main [add to 6430a6338999]
git: Enumerating objects: 4, done.
git: Counting objects: 100% (4/4), done.
git: Delta compression using up to 4 threads
git: Compressing objects: 100% (2/2), done.
git: Writing objects: 100% (4/4), 315 bytes | 315.00 KiB/s, done.
git: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
```

Wichtig zu behalten: Gepusht wird immer das Lesezeichen. Wenn ich also neue Commits nach main haben will, muss ich main vorher verschieben.

Das nächste Mal:

```
jj git fetch
```

Evtl rebasen: `jj rebase -r @ -d main` oder was auch immer

```
jj bookmark move main –to @
jj git push
```

Ich kann aber auch das hier machen, um einen PR zu bekommen. Mit `-c` und einer Change-ID:

```
jj git push -c @
Creating bookmark push-qqznvovkonwz for revision qqznvovkonwz
Changes to push to origin:
  bookmark: push-qqznvovkonwz [add to 630165d48039]
git: Enumerating objects: 5, done.
git: Counting objects: 100% (5/5), done.
git: Delta compression using up to 4 threads
git: Compressing objects: 100% (3/3), done.
git: Writing objects: 100% (3/3), 389 bytes | 389.00 KiB/s, done.
git: Total 3 (delta 2), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
remote: 
remote: Create a pull request for 'push-qqznvovkonwz' on GitHub by visiting:
remote:      https://github.com/bsiegert/BulkTracker/pull/new/push-qqznvovkonwz
remote: 
```

# 4

Ich möchte noch eine Sache zeigen, die ich sehr gern habe. Lokale Commits sind Spielzeug! Erst mal die beiden Spielcommits von vorhin loswerden:

```
> jj
@  pznqpzpn bsiegert@example.com 2026-08-10 17:57:15 a1aa73b5
│  (no description set)
│ ○  spsznnuo bsiegert@example.com 2026-08-10 17:38:49 8af78aef
├─╯  (no description set)
◆  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 main 6430a633
│  Create README
~

> jj abandon s p
Abandoned 2 commits:
  pznqpzpn a1aa73b5 (no description set)
  spsznnuo 8af78aef (no description set)
Working copy  (@) now at: uxkptunk 5db234ce (empty) (no description set)
Parent commit (@-)      : wmrrxwmo 6430a633 main | Create README
Added 0 files, modified 1 files, removed 0 files
bsiegert@hive11 ~/s/jjtest> jj
@  uxkptunk bsiegert@example.com 2026-08-10 18:09:41 5db234ce
│  (empty) (no description set)
◆  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 main 6430a633
│  Create README
~
```

Ich bekomme automatisch einen leeren Commit, denn der untere ist jetzt immutable.

Machen wir mal drei parallele Commits mit jeweils einem File darin -- `jj new` nimmt den Parent-Commit als Argument.

```
bsiegert@hive11 ~/s/jjtest> jj describe -m "dings"
Working copy  (@) now at: uxkptunk 0f4bcf72 (empty) dings
Parent commit (@-)      : wmrrxwmo 6430a633 main | Create README
bsiegert@hive11 ~/s/jjtest> echo "Hallo" > dings
bsiegert@hive11 ~/s/jjtest> jj new -m "bums" w
Working copy  (@) now at: vzqyukro a1127884 (empty) bums
Parent commit (@-)      : wmrrxwmo 6430a633 main | Create README
Added 0 files, modified 0 files, removed 1 files
bsiegert@hive11 ~/s/jjtest> echo "Hallo" > bums
bsiegert@hive11 ~/s/jjtest> jj new -m "dingsbums" w
Working copy  (@) now at: nmpplptx aca49e42 (empty) dingsbums
Parent commit (@-)      : wmrrxwmo 6430a633 main | Create README
Added 0 files, modified 0 files, removed 1 files
bsiegert@hive11 ~/s/jjtest> echo "Hallo" > dingsbums
bsiegert@hive11 ~/s/jjtest> jj
@  nmpplptx bsiegert@example.com 2026-08-10 18:14:48 ce645252
│  dingsbums
│ ○  vzqyukro bsiegert@example.com 2026-08-10 18:14:38 ab797d3e
├─╯  bums
│ ○  uxkptunk bsiegert@example.com 2026-08-10 18:14:19 44e73f90
├─╯  dings
◆  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 main 6430a633
│  Create README
~
```

Und jetzt einen neuen Commit mit der Summe aus allen!

```
bsiegert@hive11 ~/s/jjtest> jj new n v u
Working copy  (@) now at: ozqyktrp 94142bcd (empty) (no description set)
Parent commit (@-)      : nmpplptx ce645252 dingsbums
Parent commit (@-)      : vzqyukro ab797d3e bums
Parent commit (@-)      : uxkptunk 44e73f90 dings
Added 2 files, modified 0 files, removed 0 files
bsiegert@hive11 ~/s/jjtest> jj
@      ozqyktrp bsiegert@example.com 2026-08-10 18:15:06 94142bcd
├─┬─╮  (empty) (no description set)
│ │ ○  uxkptunk bsiegert@example.com 2026-08-10 18:14:19 44e73f90
│ │ │  dings
│ ○ │  vzqyukro bsiegert@example.com 2026-08-10 18:14:38 ab797d3e
│ ├─╯  bums
○ │  nmpplptx bsiegert@example.com 2026-08-10 18:14:48 ce645252
├─╯  dingsbums
◆  wmrrxwmo bsiegert@example.com 2026-08-10 17:38:49 main 6430a633
│  Create README
~
```

Das Feature ist interessant, wenn man mit Code review arbeitet. Die drei
Commits können simultan im Review sein, gleichzeitig kann ich weiter arbeiten.

Ich weiss nicht, wie ihr arbeitet, aber bei mir gibt es meist zwei Phasen:
zuerst schreibe ich Code, dann bastle ich so lange herum, bis er funktioniert
🙂 Für letzteres mache ich einfach so viele neue Commits, wie ich will. Bevor
ich anfange, Debug printfs einzufügen – neuer Commit her. Wenn das das Problem
nicht löst, weg damit (`jj abandon`)!

# 5

Wir haben jetzt eine kleine Tour durch Ju Jitsu unternommen und gesehen, wie es uns hilft.

Aber was ist mit unserer These vom Anfang? **Ist jj jetzt die Lösung gegen die
Contributor-Krise? -- Nein, natürlich nicht.** Es gibt natürlich keinen Ersatz
für vernünftiges Community-Management.

Aber wenn jemand vielleicht mit dem Gedanken spielt, bei Open Source
einzusteigen, und euch git vielleicht Angst macht – versucht es doch mal mit
jj.

Maintainer – legt vielleicht neuen Mitstreitern jj ans Herz 🙂


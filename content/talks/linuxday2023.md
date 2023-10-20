---
title: "pkgsrc statt Containern!"
where: "LinuxDay Dornbirn 2023"
slides: "/talks/linuxday2023/slides/"
date: 2023-09-30T17:37:10+02:00
description: >
  A talk in German about pkgsrc in general.
  Bootstrapping as a user (unprivileged) means that you can use
  pkgsrc like your very own virtualenv, except with any sort of
  package. I used nginx as an example.
---

# 1.

Es gibt da dieses Betriebssystem, und es ist nicht Linux.

NetBSD.

NetBSD besteht nicht nur aus einem Kernel, sondern aus einem Basissystem, in dem
auch eine Shell, ein Compiler, der X-Server, usw. ist. Aber zum Beispiel kein
Firefox.

Was mache ich jetzt, wenn ich aber Firefox installieren möchte?

Dafür gibt es pkgsrc, gesprochen „package source“. Wie der Name schon sagt, ist
es eine Sammlung von Quellpaketen, also Rezepten, um Software aus dem Quelltext
zu bauen.

Pkgsrc hat ungefähr 30’000 verschiedene Pakete für alle Arten von Software. Es
gibt jedes Quartal ein neues stabiles Release, das über die drei Monate hinweg
Sicherheits- und andere Updates bekommt.

Wo es jetzt interessant wird: pkgsrc unterstützt eben nicht nur NetBSD, sondern
viele verschiedene Betriebssysteme — viele verschiedene Unix-Varianten und eben
auch Linux und macOS.

Also kann man auf Linux pkgsrc für alle Arten Software benutzen, die nicht Teil
der Distro ist. Zum Beispiel, weil die Distribution ein CentOS mit Versionen von
anno dazumal ist.

Es gibt übrigens auch Binärpakete und so etwas wie apt (namens pkgin). Für das,
was ich im Folgenden berichten möchte, muss man aber aus dem Source bauen.

# 2.

Das Standardverzeichnis, in dem die installierte Software landet, ist
`/usr/pkg`.  Man kann aber jedes beliebige andere Verzeichnis angeben.

Standardmässig braucht man Root-Rechte. Man kann aber auch eine
„unprivilegierte“ Installation als normaler User anfangen.

Dann hat man:

⁃ alle Dateien sind in diesem Verzeichnis — Binaries, Libraries, die
  Paketdatenbank, Konfiguration.
⁃ Allerdings nicht ganz portabel: Man kann den Ordner nicht an eine andere
  Stelle verschieben (der Pfad steht an verschiedenen Stellen), aber man kann ihn
  sichern und wiederherstellen, oder auf einem anderen Rechner in denselben Pfad
  entpacken.

Trotzdem hat man schon einmal so etwas wie ein `virtualenv`: Die Pakete, die man
installiert hat, und ihre Abhängigkeiten in einem handlichen Verzeichnisbaum.

Nehmen wir als Beispiel `nginx`:

```
cd pkgsrc/bootstrap
./bootstrap --unprivileged --prefix=$HOME/linuxdaydemo
./cleanup

cd ../www/nginx
~/linuxdaydemo/bin/bmake package-install MAKE_JOBS=6
```

Das dauert jetzt ein bisschen :) ...

```
===========================================================================
The following directories are used by nginx-1.24.0nb10 and
have the wrong ownership and/or permissions:

	/Users/bsiegert/linuxdaydemo/var/db/nginx (m=0700, o=nginx, g=nginx)

===========================================================================
===========================================================================
The following files should be created for nginx-1.24.0nb10:

	/Users/bsiegert/linuxdaydemo/etc/rc.d/nginx (m=0755)
	    [/Users/bsiegert/linuxdaydemo/share/examples/rc.d/nginx]

===========================================================================
===========================================================================
$NetBSD: MESSAGE,v 1.3 2020/09/18 09:15:56 kim Exp $

Consider adding something like following lines to /etc/newsyslog.conf:

/Users/bsiegert/linuxdaydemo/var/log/nginx/access.log nginx:nginx 640 7 * 24 ZB /Users/bsiegert/linuxdaydemo/var/run/nginx.pid SIGUSR1
/Users/bsiegert/linuxdaydemo/var/log/nginx/error.log  nginx:nginx 640 7 * 24 ZB /Users/bsiegert/linuxdaydemo/var/run/nginx.pid SIGUSR1

===========================================================================

```

Danach kann ich in `~/linuxdaydemo/etc/nginx/nginx.conf` meine Einstellungen
vornehmen und den ganzen Tree auf alle meine Webserver verteilen.

Und damit gibt mir pkgsrc zumindest schon mal ein wichtiges Feature von einem
Docker-Container: die reproduzierbare Umgebung.

Aber man hat noch einige Vorteile:

⁃ einen guten Upgradepfad. Einfach pkgsrc-Tree aktualisieren und
  `pkg_rolling-replace` benutzen.

⁃ Vulnerability-Management!

  ```
  $ ~/linuxdaydemo/sbin/pkg_admin fetch-pkg-vulnerabilities
  $ ~/linuxdaydemo/sbin/pkg_admin audit
  Package perl-5.38.0 has a symlink-attack vulnerability, see https://nvd.nist.gov/vuln/detail/CVE-2011-4116
  Package perl-5.38.0 has a sensitive-information-disclosure vulnerability, see https://nvd.nist.gov/vuln/detail/CVE-2023-31484
  Package perl-5.38.0 has a sensitive-information-disclosure vulnerability, see https://nvd.nist.gov/vuln/detail/CVE-2023-31486
  ```

Viel Spass mit pkgsrc!
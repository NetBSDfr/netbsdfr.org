+++
date = '2025-08-29T08:30:00+01:00'
draft = false
title = 'netbsd-MICROVM'
+++

```
Hi,

It would be nice to have the MICROVM kernel available in binary form
for the upcoming NetBSD 11 releases.
The following commit does that:

Module Name:    src
Committed By:   imil
Date:           Tue Aug 26 05:08:57 UTC 2025

Modified Files:
         src/distrib/notes/common: contents
         src/etc/etc.amd64: Makefile.inc
         src/etc/etc.i386: Makefile.inc

Log Message:
Distribute the MICROVM kernel on amd64 and i386.

See sys/arch/{amd64,i386}/conf/MICROVM for usage instructions.


To generate a diff of this commit:
cvs rdiff -u -r1.187 -r1.188 src/distrib/notes/common/contents
cvs rdiff -u -r1.17 -r1.18 src/etc/etc.amd64/Makefile.inc
cvs rdiff -u -r1.70 -r1.71 src/etc/etc.i386/Makefile.inc

Thanks!
```
[source](https://releng.netbsd.org/cgi-bin/req-11.cgi?show=18)

Ce message de [pullup][1], je rêve de l'ecrire depuis 2 ans, date à laquelle j'ai [commencé à travailler][2] sur le [boot PVH][3] et plus généralement sur l'accélération du temps de boot du noyau _NetBSD_. L'objectif était d'en faire un noyau tellement rapide à démarrer qu'on pourrait l'utiliser de façon pratiquement transparante pour executer des applications totalement isolées, au contraire des containers.

C'est maintenant une réalité : [NetBSD 11][4] sera distribué avec un nouveau noyau, [netbsd-MICROVM][5], disponible pour architectures _amd64_ et _i386_. On pourra donc très simplement construire des machines virtuelles légères démarrant un système _NetBSD_ en quelques milisecondes pour répndre à tout type de service.  
La méthode la plus simple consiste en l'utilisation de [smolBSD][6], mais vous pouvez évidemment préparer votre propre système en démarrant le noyau comme expliqué [dans le fichier de configuration du noyau MICROVM][7].

[1]: https://www.netbsd.org/developers/releng/pullups.html
[2]: https://mail-index.netbsd.org/port-xen/2023/10/25/msg010460.html
[3]: https://xenbits.xen.org/docs/unstable/misc/pvh.html
[4]: https://www.netbsd.org/changes/changes-11.0.html
[5]: https://nycdn.netbsd.org/pub/NetBSD-daily/netbsd-11/latest/amd64/binary/kernel
[6]: https://github.com/NetBSDfr/smolBSD
[7]: https://github.com/NetBSD/src/blob/trunk/sys/arch/amd64/conf/MICROVM

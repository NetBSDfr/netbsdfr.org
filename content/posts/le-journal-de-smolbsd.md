+++
date = '2025-01-09T06:26:48+01:00'
draft = true
title = 'Le Journal De smolBSD'
+++

Il y a un peu plus d'un an votre serviteur eut cette idée -qui à l'époque me semblait- folle d'implémenter la fonctionnalité [boot PVH][1]_ dans le noyau de notre système d'exploitation favori. En quelques mots, il s'agit de pouvoir démarrer un noyau avec un hyperviseur ([QEMU][15] ou [Firecracker][7] par exemple) sans passer par la séquence _BIOS - bootloader_, mais en branchant directement sur un point d'entrée du noyau avec les informations nécessaires au démarrage passées par l'hyperviseur.  
Cette fonctionnalité a maintenant été intégrée au code source officiel du noyau [en décembre 2024][2].  

Suite à ces travaux, j'ai voulu comprendre pourquoi et comment Colin Perceval réussissait à faire démarrer un noyau _FreeBSD_ en 25ms là où _NetBSD_ ne parvenait pas à descendre sous les 200ms. Cela m'a emmené à porter le pilote [MMIO cmdline][3] du sieur Colin, grace auquel on s'affranchit du bus _PCI_ pour l'attachement des pilotes _VirtIO_. L'ajout de cette fonctionnalité avait en outre une condition, implémenter un bus "trivial", [pv(4)][4] spécialement dédié à l'attachement des pilotes ne nécessitant pas de bus _PCI_ ou _ISA_ sur machine _x86_.

Malgré cette nouvelle mécanique, impossible de passer la barre des 150ms, donc afin de comprendre où le noyau "perdait" du temps, j'ai porté une infrastructure de test, [tslog(4)][5], encore une fois l'œuvre de Colin.  
Grâce à cet outil, je compris que ces précieuses milisecondes étaient utilisées à **attendre** !
En effet, plusieurs appels à la fonction `DELAY(9)` sont présents dans le noyau, essentiellement pour :

* calculer la fréquence du _CPU_
* attendre que le port série n'aie plus de caractères en tampon

J'ai entrepris de me débarrasser de ces délais inutiles en implémentant diverses méthodes de récupération de la fréquence des processeurs, certaines empruntées à _FreeBSD_ et d'autres à _OpenBSD_. Pour le port série, il a "suffit" d'utiliser une fonction prévue à cet effet.

Quelques petites améliorations plus loin, le noyau était capable de démarrer en **moins** de 20ms sur une machine _i5_ de 2015.

Au delà de la simple performance, l'objectif de ce travail de fond était de proposer une version miniature de _NetBSD_: [smolBSD][6]  
L'idée de ce micro-système m'est venue à l'annonce de la sortie de [Firecracker][7], un _VMM_ (gestionnaire de machine virtuel, à ne pas confondre avec l'hyperviseur) Libre issu des laboratoires d'_Amazon Web Services_ qui équipe les infrastructures _serverless_ de _cloudiste_.  
Après [plusieurs][8] [expériences][9] avec un noyau _i386_ qui savait déjà démarrer directement via le paramètre `-kernel` de _QEMU_, je me suis jeté dans le vide pendant un de mes [lives Twitch][10] en annonçant que j'allais essayer d'ajouter cette fonctionnalité au noyau _amd64_. À ce stade, je ne comprenais pas encore qu'il s'agissait d'implémenter le [boot PVH][1], ni qu'en réalité la raison pour laquelle le noyau _i386_ savait booter directement provenait de son support de [MULTIBOOT][11], qui n'a jamais trouvé son chemin jusqu'à l'architecture 64 bits. J'ai récemment fini par implémenter _PVH_ également sur _i386_, ce qui permet désormais de construire un noyau _smolBSD_ pour les deux plateformes.

Préalable aux travaux sur le noyau, le projet [smolBSD][6] avait également démarré avec un noyau _i386_, les premières expériences consistaient à désactiver dans ce dernier les fonctionnalités inutiles dans un environnement virtualisé, expérience qui a donné naissance au programme [confkerndev][12] de l'ami Denis, qui permet de désactiver, dans le **binaire** du noyau, le support des drivers choisis.  
_smolBSD_ a ensuite évolué en un projet de création de micro-système d'exploitation basé sur _NetBSD_ pour aujourd'hui proposer plusieurs modèles de _microVM_, de la simple machine de dépannage au serveur web qui démarre en quelques milisecondes, en passant par un [OS NetBSD hybride][13] qui utilise [runit][14] au lieu d'`init` !

À noter pour finir que _smolBSD_ et en particulier son noyau fonctionnent sur [QEMU][15], et plus spécialement son type de machine [microvm][16], ainsi que [Firecracker][7]. Quand aux plateformes, au delà des suscitées _i386_ et _amd64_, les outils de création d'image de _smolBSD_ permettent de créer des _microVMs_ pour l'architecture _ARM_ `aarch64`, permettant ainsi l'utilisation de telles machines sur _Raspberry Pi_, _OrangePi_ ou encore... _MacBook M[123]_!

[1]: https://xenbits.xen.org/docs/unstable/misc/pvh.html
[2]: https://gnats.netbsd.org/57813
[3]: https://github.com/freebsd/freebsd-src/blob/main/sys/dev/virtio/mmio/virtio_mmio_cmdline.c
[4]: https://cvsweb.netbsd.org/bsdweb.cgi/src/sys/arch/x86/pv/
[5]: https://man.freebsd.org/cgi/man.cgi?query=tslog&apropos=0&sektion=0&manpath=FreeBSD+14.0-current&arch=default&format=html
[6]: https://smolbsd.org
[7]: https://firecracker-microvm.github.io/
[8]: https://imil.net/blog/posts/2020/fakecracker-netbsd-as-a-function-based-microvm/
[9]: https://imil.net/blog/posts/2023/netbsd-as-a-k8s-pod/
[10]: https://twitch.tv/imilnb
[11]: https://en.wikipedia.org/wiki/Multiboot_specification
[12]: https://gitlab.com/0xDRRB/confkerndev
[13]: https://github.com/NetBSDfr/smolBSD/tree/main/service/runbsd
[14]: https://smarden.org/runit/
[15]: https://www.qemu.org/
[16]: https://www.qemu.org/docs/master/system/i386/microvm.html

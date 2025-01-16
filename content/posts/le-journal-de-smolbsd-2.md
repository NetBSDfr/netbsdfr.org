+++
date = '2025-01-15T14:56:50+01:00'
draft = true
title = 'Le Journal De smolBSD, Episode 2'
+++

Ça y est ! je viens d'intégrer le code de gestion des périphériques virtuels [MMIO][1] dans [NetBSD current][2].  
Ce fut long, plus d'un an d'attente entre [ce mail][3] et [ce commit][2], mais il est désormais possible de compiler un noyau _NetBSD_ [officiel][4] avec le support _VirtIO_ [totalement virtual][5], et ainsi démarrer une machine virtuelle de type [QEMU/microvm][6] ou [Firecracker][7].

Mon travail n'est pas fini pour autant ! Il reste encore [une dizaine de patchs à venir][8], essentiellement destinés à accélérer le démarrage du noyau en mode virtualisé.  
Des versions binaires de noyaux prêts à l'emploi et bénéficiant des patchs en question sont disponibles [ici][9].

[1]: https://docs.oasis-open.org/virtio/virtio/v1.3/csd01/virtio-v1.3-csd01.html#x1-1800002
[2]: https://cvsweb.netbsd.org/bsdweb.cgi/src/sys/dev/virtio/arch/x86/virtio_mmio_cmdline.c?rev=1.1
[3]: https://mail-index.netbsd.org/tech-kern/2023/12/28/msg029394.html
[4]: https://cvsweb.netbsd.org/bsdweb.cgi/src/sys/arch/amd64/conf/MICROVM?rev=1.1
[5]: https://cvsweb.netbsd.org/bsdweb.cgi/src/sys/arch/x86/pv/
[6]: https://www.qemu.org/docs/master/system/i386/microvm.html
[7]: https://firecracker-microvm.github.io/
[8]: https://github.com/NetBSD/src/compare/trunk...NetBSDfr:NetBSD-src:nbfr_master
[9]: https://smolbsd.org/

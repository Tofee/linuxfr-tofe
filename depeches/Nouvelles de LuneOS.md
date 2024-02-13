Des nouvelles de LuneOS
=======================

Voici bien longtemps que l'équipe de webOS-ports n'avait fait de [nouvelle version de LuneOS](https://pivotce.com/2019/10/24/luneos-october-stable-release-eggnog-latte/). De nombreux changements de fond, de nouveaux téléphones et tablettes, des difficultés techniques mais aussi une petite équipe de développeurs expliquent ce développement qui avance selon le temps libre de chacun.

Pendant ce temps, des versions de test de LuneOS ont été régulièrement mises à disposition sur Github, dans le [dépôt "luneos-testing"](https://github.com/webOS-ports/luneos-testing). Cela permet aux personnes intéressées d'essayer LuneOS, tout en gardant à l'esprit que ce ne sont que des versions intermédiaires.

# Petits rappels

LuneOS est une distribution GNU/Linux pour téléphones mobiles et tablettes, héritière de feu webOS. Le projet est porté par l’équipe webOS-Ports, dont le but est de faire revivre webOS sur les matériels contemporains.

Le projet s’appuie sur Yocto, Halium et SHR. Il utilise OpenEmbedded comme environnement de compilation. Cette dernière version de LuneOS se base sur la version « Kirkstone » de Yocto.

À l’exception des blobs utilisés pour faire tourner les pilotes Android, l’ensemble de la distribution est libre : chacun peut, s’il le veut, recompiler sa propre image chez lui. 

# Fin du forum webOS Nation et autres tracassements techniques

Le forum "webOS Nation", qui regroupe des discussions autour de webOS au sens large (vieilles versions de webOS, mods, LuneOS...) a connu quelques turbulences ces dernières années. Devant une fermeture imminente du site web, un nouveau site  [webOS Archive](https://www.webosarchive.org) a été mis en place, avec notamment la majorité des [archives des forums de webOS Nation](https://forums.webosarchive.org/). Un [nouveau forum](https://forums.weboslives.eu) a été mis en place, reprenant globalement le style de webOS Nation.

Sans avoir une activité folle, la commaunté autour de webOS reste présente, notamment grâce à la présence aujourd'hui de webOS sur les téléviseurs.

## Mort du "builder" dédié à LuneOS

Suite à plusieurs avaries, le builder "Jenkins" a dû prendre une retraite bien méritée. Malheureusement cela implique pour l'équipe de webOS-ports de compiler les différentes images de LuneOS chez soi, ce qui n'est pas très pratique (sans être une catastrophe). Sur une machine raisonnablement puissante, la création d'une image de LuneOS depuis zéro demande plusieurs heures, il faut donc parfois s'armer de patience. Heureusement Yocto permet de mutualiser les résultats de compilation pour les architectures semblables, ce qui fait gagner beaucoup de temps.

# Les changements majeurs

Depuis la dernière version publique de LuneOS, beaucoup de changements de fond ont eu lieu. On retrouve entre autres:

## Passage de Qt 5 à Qt 6

LuneOS utilise maintenant Qt 6.5.2, l'une des dernière disponibles.

## Utilisation du compositeur Wayland de webOS OSE

Le compositeur Wayland propre à LuneOS _luna-next_ a été abandonné, au profit du compositeur Wayland de webOS OSE _surface-manager_. Cependant la partie graphique (codée en QML) a été conservée, et l'expérience utilisateur reste donc très similaire.
Cela nous permet de nous concentrer sur la partie GUI du compositeur, tout en bénéficiant des mises à jour venant de OSE relatives au coeur même du compositeur.

## Changement de moteur Chromium

Les apps webOS étant utilisant intensivement HTML/Javascript/CSS, l'affichage nécessite un moteur web bien à jour et optimisé. Jusqu'à la dernière version, LuneOS utilisait QtWebEngine, fourni par Qt, pour faire tourner les applications.
Cependant webOS-OSE fournit son propre moteur basé sur chromium, indépendant de Qt et optimisé pour une utilisation embarquée. LuneOS a donc également migré vers cette nouvelle infrastructure logicielle, adoptant ce composant _WebAppManager_.

## Migration générale vers les composants de webOS-OSE

Plus généralement, les composants hérités de feu Open WebOS ont été remplacés par leur équivalent dans webOS-OSE, plus récents et encore maintenus par LG. Pour cette version de LuneOS, la [version 2.23.0 de webOS-OSE](https://www.webosose.org/blog/2023/09/07/webos-ose-2-23-0-release/) est utilisée comme base.
Cette migration inclut notamment l'utilisation des "Enhanced ACG", un modèle de sécurité plus efficace utilisé pour la communication entre les services de webOS et les apps.

Ces changements, qui pour la plupart ne sont pas visibles des utilisateurs, apportent de multiples bénéfices pour LuneOS et ses développeurs:
* une réutilisation plus large du code de webOS-OSE (maintenu par LG), ce qui implique moins de maintenance côté webOS-ports
* meilleure stabilité des composants, qui sont utilisés depuis des années dans les télévieurs et appareils LG
* la rétro-compatibilité a tout de même pu être assurée pour les vieilles apps webOS, grâce à des modifications mineures dans certains composants
* plus de facilité pour mettre à jour les composants venant de webOS-OSE

# Téléphones et tablettes: vers plus de Linux "mainline"

Dans le domaine des distribution Linux pour téléphones et tablettes, on parle de noyau "mainline" pour désigner l'utilisation directe du code source venant du noyau Linux, par opposition à l'utilisation d'un code source dérivé et proposé par un constructeur. Ce dernier est souvent proposé dans une vieille version, avec une maintenance très limitée dans le temps.

Cependant, utiliser un noyau "mainline" est à double tranchant: d'un côté, on bénéficie des dernières avancées du noyau, et des dernières version des pilotes libres. Mais de l'autre, cela signifie devoir se passer des pilotes proposés par le constructeur (pour le son, le GPS, le modem...), qui souvent n'ont jamais été proposés à l'inclusion dans le code source principal de Linux.

Au final, dans le cas de LuneOS, 3 voies se dessinent lorsqu'il s'agit de faire tourner l'OS sur un téléphone ou une tablette:
1. le constructeur remonte ses changements dans le noyau Linux, et s'appuie sur un développement open-source: Pine64 et Purism sont deux exemples récents de cette approche. C'est le cas idéal pour LuneOS, où des pilotes open-source bien intégrés peuvent être utilisées pour faire fonctionner les composants matériels.
2. le constructeur ne propose qu'une version Android de ses pilotes et du noyau; ce dernier reste figé dans une même version, _relativement récente_, avec une maintenance minimale. LuneOS va alors utiliser Halium pour profiter des pilotes faits pour Android, tout en gardant le reste du système sur une pile logicielle "systemd/glibc" classique. Cette situation reste très présente, car l'immense majorité des téléphones et tablettes du marché tournent sur Android.
3. le constructeur propose une vieille version du noyau, non maintenue, et dont les limitations deviennent problématiques pour LuneOS. Dans ce cas, LuneOS va tenter d'utiliser un noyau plus récent, mais qui a un support partielle du matériel. Cette stratégie a souvent un résultat très mitigé, avec de gros manques fonctionnels. En gros, c'est la tentative de la dernière chance avant l'abandon du support de ce matériel.

## Le matériel Pine64

Comme évoqué plus haut, Pine64 cultive ses liens avec la communauté open-source, et incite les développeurs à proposer leurs OS sur leur matériel. On retrouve ainsi de nombreux OS comme PostmarketOS, Plasma Mobile ou encore Ubuntu Touch. LuneOS a pris le train en marche très tôt, et peut aujourd'hui s'installer sur le Pinephone, le Pinephone Pro ainsi que sur la tablette PineTab 2.
Pour le Pinephone et le Pinephone Pro, LuneOS nécessite maintenant l'installation préalable de [Tow-boot](https://pine64.org/documentation/PinePhone_Pro/Software/Bootloaders) sur le téléphone. Ce dernier est un dérivé de U-Boot, qui vise à standardiser et faciliter le démarrage des OS sur du matériel embarqué.

Comme ce matériel tourne avec une version très récente du noyau Linux, il est possible pour LuneOS de lancer Waydroid; cependant cette fonctionnalité nécessite encore beaucoup de travail.

The new close to mainline kernel for the Pine64 devices allows them to run things like Waydroid out of the box! [ https://waydro.id/ ]

All other supported Android devices are now based on Halium/Android 9.0. [ https://halium.org/ ]

So what is ahead for the near future?

Our focus will be on the mainline devices and emulator (qemux86-64), however we will try to keep support for the Android/Halium based targets as well.

* Upgrade to latest Chromium 108 released by LG recently
* Work on audio & multimedia infrastructure provided by webOS OSE to get it working in LuneOS
* Work on camera infrastructure
* Try to get a mainline kernel working for Tenderloin, Hammerhead, Mido and Tissot.
* Improve/add QML components and add new basic apps to be used such as Camera, Flashlight, Audio Player, Video Player
* Piggyback off some of the work done by the LG TV homebrew community (https://github.com/webosbrew/) [ I'll integrate that link ]
* Provide a GSI image for newer Android (9.0+) based devices, this would allow a standard image to boot on most modern Android devices v.s. building a device specific one for each device.

Download and Install [ generic link: https://webos-ports.org/wiki/Devices ]

Feel free to download the updated builds to get started. [ please specify the current supported device list ] Tenderloin, Hammerhead and Tissot remain our focus for now, but the emulators, Mako and Mido work too. Currently supported targets: PinePhone, PinePhonePro, PineTab2, Qemux86-64 (Virtualbox), all with mainline kernel. Tenderloin, Hammerhead, Tissot, Mido, Rosy, Mako (Android 9.0/Halium based with their respective Android kernels (3.4 and newer)). RaspberryPi 3 and RaspberryPi4 might work too, however we haven't tested this ourselves.

Please note that in order to use the latest stable builds Nexus 4 (Mako) and Nexus 5 (Hammerhead) you need to flash the CM 12.1 images first using CWM/TWRP. In order to do so, you might be required to do a "factory reset" or at least "wipe cache". CWM/TWRP will indicate when this is needed. After successfully flashing CM 12.1, make sure to boot it at least once before going back to CWM/TWRP to flash the latest LuneOS image! We have provided links to CM 12.1 for these 3 images on our device pages below. I guess we can remove this part.

Installation instructions for [ generic link: https://webos-ports.org/wiki/Devices ] TouchPad (Tenderloin), Nexus 4 (Mako), Nexus 5 (Hammerhead) and Emulator are on the wiki. And remember we don't do timelines.

[ I guess this line is out of date - any updates? ] Don't forget to contact us with any questions and feel free to join the discussion on the webOS Lives forums [ https://forums.weboslives.eu/t/luneos ]. Catch us on Twitter @webosports on IRC: Libera:#webos-ports, Telegram: https://t.me/luneos_dev or email webos.ports@gmail.com. Updated the IRC channel, added Telegram channel

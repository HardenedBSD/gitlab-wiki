# A propos de HardenedBSD

HardenedBSD est un fork de FreeBSD, fondée en 2014, qui met en œuvre
des technologies d'atténuation d'exploit et de durcissement. L'objectif
principal de HardenedBSD est de réaliser une réimplantation en salle 
blanche du correctif grsecurity pour Linux dans HardenedBSD.

Certaines des fonctionnalités de HardenedBSD peuvent être activées par 
application et par prison en utilisant secadm ou hbsdcontrol.
La documentation relative à ces deux outils sera abordée ultérieurement.

## Table des matières

* [Historique](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#history)
* [Fonctionnalités](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#features)
* [Options génériques du noyau](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#generic-kernel-options)
* [Durcissement du système générique](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#generic-system-hardening)
* [Randomisation de la disposition de l'espace d'adressage (ASLR)](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#address-space-layout-randomization-aslr)
* [PaX SEGVGUARD](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#pax-segvguard)
* [PAGEEXEC et MPROTECT (alias, NOEXEC)](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#pageexec-and-mprotect-aka-noexec)
* [SafeStack](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#safestack)
* [Contrôle d'intégrité des flux (CFI)](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#control-flow-integrity-cfi)
* [hbsdcontrol](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#hbsdcontrol)
* [Administration de la sécurité (secadm)](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#security-administration-secadm)
* [Contribuer à HardenedBSD](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#contributing-to-hardenedbsd)
* [Mise à jour d'HardenedBSD](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#updating-hardenedbsd)

## Historique

Le travail sur HardenedBSD a commencé en 2013 quand Oliver Pinter
et Shawn Webb ont commencé à travailler sur une implémentation de l'ASLR
(Randomisation de la disposition de l'espace d'adressage), basée sur la documentation publique de PaX,
pour FreeBSD. À l'époque, HardenedBSD était censé être une zone d'étape pour 
le développement expérimental du patch ASLR. 
Au fil du temps, alors que le processus d'intégration de l'ASLR à FreeBSD devenait
plus difficile, HardenedBSD est naturellement devenu une bifurcation (fork).

HardenedBSD a achevé la mise en œuvre de l'ASLR en 2015 avec la forme d'ASLR la plus forte de toutes les BSD.
Depuis lors, HardenedBSD est passé à la mise en œuvre d'autres technologies d'atténuation et de durcissement de l'exploitation.
OPNsense, un pare-feu open source basé sur FreeBSD, a intégré l'implémentation ASLR de HardenedBSD en 2016.
OPNsense a terminé sa migration vers HardenedBSD le 31 janvier 2019.

HardenedBSD existe aujourd'hui comme une bifurcation de FreeBSD qui suit de près le code source de FreeBSD.
HardenedBSD se synchronise avec FreeBSD toutes les six heures.
Certaines de ces branches, mais pas toutes, sont énumérées ci-dessous :

1. HEAD -> hardened/current/master
1. stable/13 -> hardened/13-stable/master
1. stable/12 -> hardened/12-stable/master

## Fonctionnalités

HardenedBSD a mis en œuvre avec succès les caractéristiques suivantes :

1. ASLR inspiré de PaX
1. NOEXEC inspiré de PaX
1. SEGVGUARD inspiré de PaX
1. Base compilée avec les exécutables indépendants de la position (PIEs)
1. Base compilée avec RELRO complet (RELRO + BIND_NOW)
1. Durcissement de certaines valeurs sysctl sensibles
1. Durcissement de la pile réseau
1. Renforcement de l'intégrité des fichiers exécutables
1. Durcissement du processus de démarrage
1. Durcissement de procs/linprocfs
1. Trusted Path Execution (TPE)
1. Randomisation des PIDs
1. SafeStack de base
1. SafeStack disponible dans les ports
1. Non-Cross-DSO CFI de base
1. Non-Cross-DSO CFI disponible dans les ports
1. Retpoline appliqué à la base et aux ports
1. Auto-initialisation des variables appliquée à la base et aux ports
1. Link-Time Optimizations (LTO) appliqué à la fois aux applications et aux librairies

## Options génériques du noyau

Toutes les fonctionnalités de HardenedBSD qui reposent sur 
le code du noyau requièrent l'option de noyau suivante :

```
options	PAX
```

En outre, l'option de noyau suivante n'est pas nécessaire,
mais expose des valeurs sysctl supplémentaires:

```
options PAX_SYSCTLS
```

Le durcissement générique du système peut être activé avec l'option de noyau suivante :

```
options	PAX_HARDENING
```

## Durcissement du système générique

HardenedBSD met en œuvre le durcissement du système générique avec l'option
`PAX_HARDENING` du noyau. Un grand nombre de ces mesures de renforcement visent
à limiter ce que les utilisateurs non root sont autorisés à faire.
Lorsque le noyau est compilé avec l'option `PAX_HARDENING` du noyau, certaines valeurs
`sysctl(8)` sont modifiés par rapport à leurs valeurs par défaut.

`procfs(5)` et `linprocfs(5)` sont modifiés pour éviter l'écriture arbitraire dans 
les registres d'un processus. Ce comportement est contrôlé par la valeur `sysctl(8)`
`hardening.procfs_harden`.

Les appels système `kld(4)` correspondants sont limités aux utilisateurs non emprisonnés,
à root uniquement. Tenter de lister les modules du noyau en utilisant `modfind(2)`,
`kldfind(2)`, et d'autres appels de système liés au KLD donneront lieu à une permission
refusée si elle est utilisée par un utilisateur non root ou emprisonné.

### Valeurs sysctl modifiés

Il s'agit des valeurs qui sont modifiés par rapport à leurs valeurs par défaut initiales
lorsque `PAX_HARDENING` est activé dans le noyau :

|                  Nœud                 |                                   Description                                  |   Type  | Valeur originale |              Valeur durcie             |
|:-------------------------------------:|:------------------------------------------------------------------------------:|:-------:|:--------------:|:---------------------------------------:|
| kern.msgbuf_show_timestamp            | Afficher l'horodatage dans msgbuf                                              | Entier | 0              | 1                                       |
| kern.randompid                        | PID aléatoire                                                                  | Entier | 0, lecture+écriture  | Aléa au démarrage et en lecture seule   |
| net.inet.ip.random_id                 | Attribuer des valeurs ID IP aléatoires                                         | Entier | 0              | 1                                       |
| net.inet.tcp.blackhole                | Ne pas envoyer de RST sur des segments vers des ports fermés                                    | Integer | 0              | 2                                       |
| net.inet.udp.blackhole                | Ne pas envoyer de port unreachable pour les connexions refusées                             | Integer | 0              | 2                                       |
| net.inet6.ip6.use_deprecated          | Permettre l'utilisation d'adresses dont la durée de vie préférée a expiré      | Entier | 1              |  0                                      |
| net.inet6.ip6.use_tempaddr            | Utiliser les adresses IPv6 temporaires avec SLAAC                              | Entier | 0              | 1                                       |
| net.inet6.ip6.prefer_tempaddr         | Préfère l'adresse IPv6 temporaire générée en dernier                           | Entier | 0              | 1                                       |
| security.bsd.see_other_gids           | Les processus non privilégiés peuvent voir des sujets/objets d'un différent gid| Entier | 1              |  0                                      |
| security.bsd.see_other_uids           | Les processus non privilégiés peuvent voir des sujets/objets d'un différent uid| Entier | 1              | 0                                       |
| security.bsd.hardlink_check_gid       | Les processus non privilégiés ne peuvent pas créer de liens en dur avec des fichiers appartenant à d'autres groupes | Entier | 0              | 1                                       |
| security.bsd.hardlink_check_uid       | Les processus non privilégiés ne peuvent pas créer de liens en dur vers des fichiers appartenant à d'autres utilisateurs  | Entier | 0              | 1                                       |
| security.bsd.stack_guard_page         | Insérez la page de garde de pile avant les segments pouvant être agrandis                         | Entier | 0              | 1                                       |
| security.bsd.unprivileged_proc_debug  | Les processus non privilégiés peuvent utiliser des moyens de débogage et de traçage des processus        | Entier | 1              | 0                                       |
| security.bsd.unprivileged_read_msgbuf | Les processus non privilégiés peuvent lire le tampon de messages du noyau                | Entier | 1              | 0                                       |

## Randomisation de la disposition de l'espace d'adressage (ASLR)

L'ASLR randomise la disposition de l'espace d'adressage virtuel d'un processus
en utilisant des deltas randomisés. L'ASLR empêche les attaquants de savoir où
se trouvent les vulnérabilités en mémoire. Sans ASLR, les attaquants peuvent facilement
créer et réutiliser des exploits sur tous les systèmes déployés. Comme c'est le cas pour toutes
les technologies d'atténuation des exploits, la technologie ASLR est censée contribuer à frustrer
les attaquants, bien qu'elle ne suffise pas à elle seule à stopper complètement les attaques.
La technologie ASLR fournit simplement une base solide pour la mise en œuvre de nouvelles technologies d'atténuation de l'exploitation. Une approche holistique de la sécurité (aussi appelée défense en profondeur) est la meilleure façon de sécuriser un système. En outre, l'ASLR est conçue pour aider à prévenir les attaques à distance, et non locales.

L'implémentation de l'ASLR de HardenedBSD est basée sur la conception et la documentation de PaX.
La documentation de PaX peut être trouvée [ici](https://git.hardenedbsd.org/hardenedbsd/pax-docs-mirror/-/blob/master/aslr.txt).

Le 13 juillet 2015, la mise en œuvre de l'ASLR de HardenedBSD a été achevée avec la pile complète
et la randomisation VDSO. Depuis lors, diverses améliorations ont été apportées, comme l'implémentation
de la randomisation de l'ordre de chargement des bibliothèques partagées. HardenedBSD est le seul BSD à
supporter la randomisation de la pile réelle. Cela signifie que le sommet de la pile est randomisé en plus
d'un espace de taille aléatoire entre le sommet de la pile et le début de la pile utilisateur.

L'ASLR est activé par défaut dans l'option `HARDENEDBSD` du noyau.
L'ASLR a été testé et on sait qu'il fonctionne sur amd64, i386, arm, arm64, et risc-v.
Les options pour l'ASLR sont les suivantes :

```
options PAX
options PAX_ASLR
```

Si le noyau a été compilé avec `options PAX_SYSCTLS`, alors la valeur sysctl
`hardening.pax.aslr.status` sera disponible.
Les valeurs suivantes détermineront l'application de l'ASLR:

1. 0 - Désactivation forcée
1. 1 - Désactivé par défaut. L'utilisateur doit activer les applications.
1. 2 - Activé par défaut. L'utilisateur doit désactiver les applications (par défaut.)
1. 3 - Activation forcée

### Mise en œuvre

L'ASLR de HardenedBSD utilise un ensemble de quatre deltas sur les systèmes 32 bits
et cinq deltas sur les systèmes 64 bits. De plus, sur les systèmes 64 bits, la compatibilité
32 bits est prise en charge par un ensemble de deltas différents. Les deltas sont calculés au
moment de l'activation de l'image (execve). Les deltas sont fournis à titre d'indice pour le
sous-système de mémoire virtuelle, qui peut encore modifier l'indice. Cela peut être le cas si
l'application demande explicitement une prise en charge de la super-page ou d'autres contraintes d'alignement.

Les deltas sont :

1. Base d'exécution PIE
1. Indice mmap pour les mappages non fixes
1. Haut et bas de la pile
1. Objet partagé dynamique virtuel (VDSO)
1. Sur les systèmes 64 bits, indice mmap pour les mappages `MAP_32BIT`

Le calcul de chaque delta est contrôlé par le nombre de bits d'entropie que
l'utilisateur veut introduire dans le delta. La quantité d'entropie peut être
modifiée dans la configuration du noyau et via les paramètres de démarrage (`loader.conf(5)`).
Par défaut, HardenedBSD utilise la quantité d'entropie suivante :

| Delta         | 32-bit  | 64-bit  | Compat  | Tunable                         | Compat Tunable                      | Option du noyau                   | Compat Option du noyau                |
|---------------|---------|---------|---------|---------------------------------|-------------------------------------|---------------------------------|-------------------------------------|
| mmap          | 14 bits | 30 bits | 14 bits | hardening.pax.aslr.mmap_len     | hardening.pax.aslr.compat.mmap_len  | PAX_ASLR_DELTA_MMAP_DEF_LEN     | PAX_ASLR_COMPAT_DELTA_MMAP_DEF_LEN  |
| Stack         | 14 bits | 42 bits | 14 bits | hardening.pax.aslr.stack_len    | hardening.pax.aslr.compat.stack_len | PAX_ASLR_DELTA_STACK_DEF_LEN    | PAX_ASLR_COMPAT_DELTA_STACK_DEF_LEN |
| PIE exec base | 14 bits | 30 bits | 14 bits | hardening.pax.aslr.exec_len     | hardening.pax.aslr.compat.exec_len  | PAX_ASLR_DELTA_EXEC_DEF_LEN     | PAX_ASLR_COMPAT_DELTA_EXEC_DEF_LEN  |
| VDSO          | 8 bits  | 28 bits | 8 bits  | hardening.pax.aslr.vdso_len     | hardening.pax.aslr.compat.vdso_len  | PAX_ASLR_DELTA_VDSO_DEF_LEN     | PAX_ASLR_COMPAT_DELTA_VDSO_DEF_LEN  |
| MAP_32BIT     | N/A     | 18 bits | N/A     | hardening.pax.aslr.map32bit_len | N/A                                 | PAX_ASLR_DELTA_MAP32BIT_DEF_LEN | N/A                                 |

Lorsqu'un processus bifurque, le processus enfant hérite des paramètres ASLR de ses parents, y compris les deltas.
Ce n'est qu'au moment de l'activation de l'image (execve) qu'un processus reçoit de nouveaux deltas.

Pour contrecarrer les attaques de type "heap spray", HardenedBSD randomise les piles par thread. En fait, chaque appel à `mmap(MAP_STACK)` est randomisé.
La randomisation de la pile par thread peut être désactivée sur une base par processus en basculant ASLR pour ce processus.

### Exécutable indépendant de la position (PIEs)

Afin d'utiliser pleinement l'ASLR, les candidatures doivent être compilées en tant que PIE
(Exécutable indépendant de la position). Si une application n'est pas compilée comme un PIE,
alors l'ASLR sera appliqué à tous sauf à la base d'exécution. Toutes les bases sont compilées
en tant que PIE, à l'exception de quelques applications qui demandent explicitement à être compilées statiquement.
Ces applications sont :

1. Toutes les applications dans /rescue
1. /sbin/devd
1. /sbin/init
1. /usr/sbin/nologin

La compilation de toutes les bases en tant que PIE peut être désactivée en définissant
`WITHOUT_PIE` dans `src.conf(5)`.

### Randomisation de l'ordre de chargement des bibliothèques partagées

Pour casser un ASLR à distance, il faut enchaîner plusieurs vulnérabilités, dont une ou plusieurs fuites d'informations.
Les vulnérabilités de fuite d'informations exposent les données qu'un attaquant peut utiliser pour déterminer la disposition
de la mémoire du processus. Les attaques par réutilisation de code, comme ROP et ses variantes, existent pour contourner les
mesures d'atténuation des exploits comme PAGEEXEC/NOEXEC.
Au fil des ans, de nombreux outils ont été développés pour la génération automatisée de gadgets ROP.
Ces outils s'appuient généralement sur des gadgets trouvés via des bibliothèques partagées et exigent
que ces bibliothèques partagées soient chargées dans le même ordre déterministe. En randomisant l'ordre
dans lequel les bibliothèques partagées sont chargées, les gadgets ROP ont plus de chances d'échouer.
La randomisation de l'ordre de chargement des bibliothèques partagées est désactivée par défaut,
mais peut être activée pour chaque application en utilisant secadm ou hbsdcontrol.

## PaX SEGVGUARD

L'ASLR a des faiblesses connues. Si une fuite d'informations est présente,
les attaquants peuvent l'utiliser pour déterminer la disposition de la mémoire et,
à terme, exploiter l'application avec succès.

PaX SEGVGUARD permet d'atténuer ces cas. SEGVGUARD enregistre le nombre de fois qu'une application
donnée a planté dans une fenêtre configurable et suspend l'exécution de l'application pendant un
temps configurable une fois que la limite de plantage a été atteinte.

L'option de noyau pour PaX SEGVGUARD est:

```
options PAX_SEGVGUARD
```

SEGVGUARD peut être configuré en modifiant la valeur `hardening.pax.segvguard` via sysctl.

## PAGEEXEC et MPROTECT (alias, NOEXEC)

[PAGEEXEC](https://git.hardenedbsd.org/hardenedbsd/pax-docs-mirror/-/blob/master/pageexec.txt)
et
[MPROTECT](https://git.hardenedbsd.org/hardenedbsd/pax-docs-mirror/-/blob/master/mprotect.txt)
comprennent ce que l'on appelle plus communément W^X (W ou X). Sa conception et sa mise
en œuvre dans HardenedBSD s'inspirent de PaX. PAGEEXEC empêche les applications de créer
des mappages de mémoire qui sont à la fois inscriptibles (W)
et exécutable (X) `mmap(2)` à la fois. MPROTECT empêche les applications de basculer les
mappages mémoire entre l'écriture et l'exécution avec `mprotect(2)`.
La combinaison de PAGEEXEC et MPROTECT empêche les attaquants d'exécuter le code injecté,
ce qui les oblige à utiliser des techniques de réutilisation de code comme ROP et ses variantes.
Les techniques de réutilisation de code sont très difficiles à rendre fiables, en particulier lorsque
de multiples technologies d'atténuation de l'exploitation sont présentes et actives.

Les fonctions PAGEEXEC et MPROTECT peuvent être activées avec les options
`PAX_NOEXEC` du noyau et sont activées par défaut dans le noyau `HARDENEDBSD`.
Si l'option `PAX_SYSCTLS` est également activée, deux valeurs sysctl seront créés, qui suivent la même logique que la valeur  sysctl `hardening.pax.aslr.status` :

1. `hardening.pax.pageexec.status` - Par défaut 2
1. `hardening.pax.mprotect.status` - Par défaut 2

### PAGEEXEC

Si une application demande le mappage de la mémoire via `mmap(2)`, et que la requête
de l'application contient `PROT_WRITE` et `PROT_EXEC`, alors `PROT_EXEC` est abandonné.
L'application pourra écrire dans le mappage, mais ne pourra pas exécuter ce qu'elle a été écrit.
Lorsqu'une application demande des cartographies W|X, il est plus probable que l'application écrive
sur la cartographie, mais ne l'exécute pas. C'est le cas de certains scripts Python :
le développeur demande simplement plus de permissions que ce qui est réellement nécessaire..

Le noyau conserve le concept de protection de PaX. HardenedBSD supprime `PROT_EXEC` de la protection
maximale lorsque `PROT_WRITE` est demandé.
Quand `PROT_EXEC` est demandé, `PROT_WRITE` est supprimé de la protection maximale.
Lorsque les deux sont demandés, `PROT_WRITE`est prioritaire et `PROT_EXEC` est supprimé de la demande
et de la protection maximale.

### MPROTECT

Si une application demande qu'un mappage en écriture soit modifié 
en exécutable via `mprotect(2)`, la demande échouera et fixera `errno` à
`EPERM`. Il en va de même pour un mappage exécutable qui devient accessible
en écriture via `mprotect(2)`. Les applications et les objets partagés qui
utilisent des relocalisations de texte (TEXTRELs) ont des problèmes avec la
fonction MPROTECT. Les TEXTRELS exigent que le code exécutable soit déplacé
à différents endroits de la mémoire. Pendant le processus de relocalisation,
la mémoire nouvellement allouée doit être à la fois inscriptible et exécutable.
Une fois le processus de relocalisation terminé, le mappage peut être marqué comme
`PROT_READ|PROT_EXEC`.

Certaines applications avec un JIT, notamment Firefox, choisissent de créer des mappages
de mémoire inscriptible qui ne sont pas exécutables, mais mettent à jour le mappage en
exécutable lorsque cela est approprié. Cela élimine le problème des mappages de mémoire
active qui sont à la fois inscriptibles et exécutables. Cela permet à des applications comme
Firefox de fonctionner avec PAGEEXEC, tout en ayant un problème avec MPROTECT.

En combinant à la fois PAGEEXEC et MPROTECT, HardenedBSD permet une forme stricte de W^X.
Certaines applications peuvent avoir des problèmes avec PAGEEXEC, MPROTECT, ou les deux.
En cas de problème, secadm ou hbsdcontrol peuvent être utilisés pour désactiver PAGEEXEC,
MPROTECT ou les deux pour une même application.

## SafeStack

SafeStack est une atténuation d'exploit qui crée deux piles : une pour les données qui
doivent être protégées, telles que les adresses de retour et les pointeurs de fonction ;
et une pile non sécurisée pour tout le reste. SafeStack promet une faible pénalité de
performance (généralement autour de 0,1 %).

Pour être efficace, SafeStack nécessite à la fois l'ASLR et le W^X. HardenedBSD satisfaisant
à ces deux conditions préalables, SafeStack a été considéré comme un excellent candidat à
l'inclusion par défaut dans HardenedBSD. A partir de HardenedBSD 11-STABLE, il est activé
par défaut pour amd64. SafeStack peut être désactivé en définissant
`WITHOUT_SAFESTACK` dans `src.conf(5)`.

A partir du 08 octobre 2018, SafeStack ne prend en charge que les applications et non les
bibliothèques partagées. De nombreux correctifs ont été soumis à clang par des tiers pour
ajouter le support manquant des bibliothèques partagées. En tant que tel, SafeStack est
toujours en cours de développement actif.

SafeStack est également disponible dans l'arborescence des ports de HardenedBSD.
Contrairement à PIE et RELRO et BIND_NOW, il n'est pas activé globalement pour
l'arborescence des ports. Certains ports connus pour bien fonctionner avec SafeStack
l'ont activé par défaut. Les utilisateurs sont en mesure de basculer le SafeStack en utilisant le
`config` du make cible. En outre, l'option SafeStack n'est applicable qu'à l'architecture amd64.
Tenter d'activer l'option SafeStack pour une construction de port non amd64 aboutira à un NO-OP.
L'option SafeStack ne sera tout simplement pas appliquée.

# Auto-Initialization des Variables

Dans HardenedBSD 13, nous avons activé une fonction de llvm appelée [automatic
variable initialization](https://reviews.llvm.org/D54604). Variables that would normally be uninitialized are zero-initialized. This helps prevent information leaks and abuse of code with undefined behavior.

Extrait de la documentation de llvm :

Cette fonctionnalité a pour but de rendre les comportements non définis moins douloureux, ce qui les personnes soucieuses de sécurité en seront très heureuses. Notamment, cela signifie qu'il n'y a pas de fuite d'information par inadvertance lorsque :

* Le compilateur réutilise les emplacements de pile, et une valeur est utilisée non initialisée.
* Le compilateur réutilise un registre, et une valeur est utilisée non initialisée.
* Les structures de pile / tableaux / unions avec padding sont copiés.

Pour une documentation plus complète, consultez le lien du premier paragraphe de cette section.

## Le Control-Flow Integrity (CFI)

L'intégrité des flux de contrôle (CFI) est une technique d'atténuation des exploits qui empêche 
le transfert non souhaité du contrôle des instructions de la branche vers des emplacements de 
mémoire arbitrairement valables. La mise en œuvre du CFI à partir de clang/llvm se présente 
sous deux formes : Cross-DSO CFI et non-Cross-DSO CFI. 
HardenedBSD 12 active le CFI non Cross-DSO par défaut sur amd64 et arm64 pour la base.

Le CFI a besoin d'un linker qui prend en charge l'optimisation du temps de liaison (Link Time Optimization, LTO).
A partir de 12, HardenedBSD est livré avec ld.lld par défaut
linker. ld.lld soutient LTO.

Le Non-Cross-DSO CFI ajoute des contrôles avant et après chaque branche d'instruction dans la demande elle-même.
Si une application charge les bibliothèques via `dlopen(3)` et résout les fonctions via `dlsym(3)` et
appelle ces fonctions, l'application sera interrompue. Certaines applications,
comme `bhyveload(8)` faire cela et ainsi avoir le système cfi-icall
désactivé, ce qui lui permet d'appeler des fonctions résolues via `dlsym(3)`. Ainsi, si 
un utilisateur constate qu'une application plante dans HardenedBSD 12, il doit déposer 
un rapport de bogue. Le schéma cfi-icall peut être désactivé lors de la construction de 
world en ajoutant une dérogation CFI dans le Makefile de cette application.

Notez que le Non-Cross-DSO CFI n'exige pas l'ASLR et le strict W^X.
Étant donné que Cross-DSO CFI conserve les métadonnées et les informations d'état,
Cross-DSO CFI nécessite ASLR et W^X pour être efficace.

Non-Cross-DSO CFI support has been added to HardenedBSD's ports
framework. However, it is not enabled by default. Support for CFI in
ports is still very premature and is only available for those brave
users who want to experiment.

A partir du 20 Mai 2017, Cross-DSO CFI fait l'objet de recherches actives.
Cependant, le support de Cross-DSO CFI n'est pas encore disponible dans HardenedBSD. 
La Cross-DSO CFI permettrait aux fonctions résolues par l'intermédiaire de
`dlopen(3)`/`dlsym(3)` pour fonctionner puisque la CFI pourrait être appliquée entre 
les limites des objets partagés dynamiques (DSO). Des progrès significatifs ont été réalisés 
au cours du premier semestre 2018 en ce qui concerne Cross-DSO CFI.
Les travaux de Cross-DSO CFI ont été interrompus en 2019 et 2020. Les travaux ont repris en 2021, en commençant par appliquer LTO aux bibliothèques (en plus de LTO déjà appliqué aux apps). Lorsqu'elles sont construites avec Cross-DSO CFI, certaines applications, comme les outils ZFS, se bloquent. Des travaux sont en cours pour déterminer la cause de ces plantages et les corriger.

## hbsdcontrol

`hbsdcontrol(8)` est un outil, inclus dans la base, qui permet aux 
utilisateurs de basculer entre les différentes mesures d'atténuation
des exploits pour chaque application. Les utilisateurs utiliseront généralement
hbsdcontrol pour désactiver les restrictions PAGEEXEC et/ou MPROTECT.
hbsdcontrol a une portée similaire à secadm et est préféré à secadm pour les systèmes
de fichiers qui prennent en charge des attributs étendus.

Contrairement à secadm, hbsdcontrol n'utilise pas de fichier de configuration.
Il stocke plutôt des métadonnées dans les attributs étendus du système de fichiers.
UFS et ZFS prennent tous deux en charge les attributs étendus. Les systèmes de fichiers en réseau,
comme NFS ou SMB/CIFS ne le font pas. secadm est la méthode préférée pour basculer l'atténuation
de l'exploitation uniquement dans les cas où les attributs étendus ne sont pas pris en charge.

Exemple d'utilisation de hbsdcontrol pour désactiver MPROTECT pour Firefox :

```
# hbsdcontrol pax disable mprotect /usr/local/lib/firefox/firefox
# hbsdcontrol pax disable mprotect /usr/local/lib/firefox/plugin-container
```

## Administration de la sécurité (secadm)

secadm est un outil, distribué via les ports, qui permet aux utilisateurs de 
basculer les mesures d'atténuation des exploits sur une base par application et par prison.
Les utilisateurs utiliseront généralement secadm pour désactiver les restrictions PAGEEXEC et/ou MPROTECT.

secadm comprend également une fonctionnalité connue sous le nom d'Integriforce. 
Integriforce est une implémentation de l'exécution vérifiée. 
Elle applique des signatures basées sur le hachage pour les binaires et leurs objets partagés dépendants.
Integriforce peut être configuré en mode liste blanche. Lorsqu'il y a au moins une règle Integriforce est activée, 
toutes les applications souhaitées et leurs objets partagés dépendants doivent également avoir des règles. 
Objets partagés dépendants doivent également avoir des règles. Si une application et ses objets partagés ne sont pas 
inclus dans l'ensemble de règles, l'exécution de cette l'exécution de cette application sera interdite. 
Ceci affecte également les objets partagés chargés
via [dlopen(3)](https://www.freebsd.org/cgi/man.cgi?query=dlopen&sektion=3&manpath=freebsd-release-ports).

Lorsqu'un fichier est ajouté au jeu de règles de secadm, secadm interdit toute modification de ce fichier. 
Cela inclut la suppression, l'ajout, la réduction ou toute autre modification du fichier. 
En effet, secadm suit les fichiers sous son contrôle en utilisant l'inode. Modifier le fichier pourrait changer l'inode, 
ou le libérer en cas de suppression, modifiant ainsi implicitement le jeu de règles de secadm. 
Pour protéger l'intégrité du jeu de règles chargé, secadm protège également les fichiers qu'il contrôle.

Ainsi, lors de la mise à jour des ports ou des paquets installés, il faut faire attention.
Videz le jeu de règles avant d'installer les mises à jour. Le jeu de règles peut être rechargé après la mise à jour.

### Téléchargement et installation de secadm

secadm ne fait actuellement pas partie de la base, bien que cela soit prévu dans un futur proche. 
secadm peut être installé soit via le dépôt de paquets :

```
# pkg install secadm-kmod secadm
```

ou en utilisant les ports de HardenedBSD :

```
# cd /usr/ports/hardenedbsd/secadm
# make install clean
# cd /usr/ports/hardenedbsd/secadm-kmod
# make install clean
```

### Configuration de secadm

Par défaut, secadm recherche un fichier de configuration à l'adresse suivante
`/usr/local/etc/secadm.rules`. Dans le cadre de cette documentation,
ce fichier sera simplement référencé comme `secadm.rules`. secadm ne
n'installe ni ne gère le fichier `secadm.rules`. Il lit simplement le fichier,
s'il existe, et transmet les données analysées au module du noyau. secadm peut être configuré 
soit via la ligne de commande, soit avec `secadm.rules`. Les deux outils 
secadm et `secadm.rules` contiennent des pages de manuel. Une fois installé, les utilisateurs
peuvent consulter la page de manuel secadm à la section 8 et secadm.rules à la section 5.

`secadm.rules` doit être dans un format que libucl peut analyser, car secadm
utilise libucl pour analyser `secadm.rules`.

Un exemple de `secadm.rules`  ressemblerait à ceci :

```
secadm {
	pax {
		path: "/usr/local/lib/firefox/firefox",
		mprotect: false,
	},
	pax {
		path: "/usr/local/lib/firefox/plugin-container",
		mprotect: false,
	},
}
```

Une fois secadm configuré, il peut être lancé via la commande système 
[rc(8)](https://www.freebsd.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) :

```
# sysrc secadm_enable=YES
# service secadm start
```

### Toutes les options de configuration de secadm

Voici les options disponibles pour pax :

| Option           	| Besoin	| Type		| Description									|
|-----------------------|---------------|---------------|-------------------------------------------------------------------------------|
| path			| Nécessaire	| Chaîne de caractères	| Chemin entièrement qualifié de l'exécutable					|
| aslr			| Optionnel	| Booléen	| Bascule ASLR									|
| disallow_map32bit	| Optionnel	| Booléen	| Basculer la possibilité d'utiliser le MAP_32BIT [mmap(2)](https://www.freebsd.org/cgi/man.cgi?query=mmap&sektion=2&manpath=freebsd-release-ports) sur les systèmes 64 bits	|
| mprotect		| Optionnel	| Booléen	| Basculer les restrictions mprotect					|
| pageexec		| Optionnel	| Booléen	| Basculer les restrictions pageexec				|
| segvguard		| Optionnel	| Booléen	| Bascule SEGVGUARD								|
| shlibrandom		| Optionnel	| Booléen	| Basculer la randomisation de l'ordre de chargement des bibliothèques partagées				|

Exemple de configuration pour pax:

```
secadm {
	pax {
		path: "/usr/local/lib/firefox/firefox",
		mprotect: false,
	},
	pax {
		path: "/usr/local/lib/firefox/plugin-container",
		mprotect: false,
	},
}
```

Voici les options d'integriforce disponibles :

| Option           	| Besoin	| Type		| Description									|
|-----------------------|---------------|---------------|-------------------------------------------------------------------------------|
| path			| Nécessaire	| Chaîne de caractères	| Chemin entièrement qualifié de l'exécutable ou de la bibliothèque partagée.			|
| hash			| Nécessaire	| Chaîne de caractères	| hachage [sha1(1)](https://www.freebsd.org/cgi/man.cgi?query=sha1&sektion=1&manpath=freebsd-release-ports) ou [sha256(1)](https://www.freebsd.org/cgi/man.cgi?query=sha256&sektion=1&manpath=freebsd-release-ports) du fichier						|
| type			| Nécessaire	| Chaîne de caractères	| Type de hachage. Soit "sha1" ou "sha256".					|
| mode			| Nécessaire	| Chaîne de caractères	| Soit "soft" ou "hard". En mode doux, si le hachage ne correspond pas, un avertissement est imprimé dans le syslog et l'exécution est autorisée. En mode hard, si le hachage ne correspond pas, une erreur est imprimée dans syslog et l'exécution est refusée |

Exemple de configuration d'integriforce :

```
secadm {
	integriforce {
		path: "/bin/ls",
		hash: "7dee472b6138d05b3abcd5ea708ce33c8e85b3aac13df350e5d2b52382c20e77",
		type: "sha256",
		mode: "hard",
	}
}
```

## Contribuer à HardenedBSD

HardenedBSD utilise GitLab en auto-hébergement pour le contrôle des sources et les rapports de bogues. 
Les utilisateurs peuvent soumettre des rapports de bogue pour le code source de base d'HardenedBSD.
[ici](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/issues) et pour les ports
[ici](https://git.hardenedbsd.org/hardenedbsd/ports/-/issues). Lorsque 
vous soumettez un rapport de bogue, veuillez inclure les informations suivantes:

* La version de HardenedBSD
* L'architecture
* Si le rapport concerne une panique du noyau, le backtrace de la panique
* Étapes pour reproduire le bogue

### Processus de développement de HardenedBSD

HardenedBSD utilise trois dépôts pendant le processus de développement :

| Dépôt		| Objectif						|
|-----------------------|-------------------------------------------------------|
| [hardened/current/master](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/tree/hardened/current/master)		| Dépôt principal de développement				|

Branches de développement HardenedBSD :

| Branche           			| Dépôt		| Mises à jour binaires| Objectif						|
|---------------------------------------|-----------------------|---------------|-------------------------------------------------------|
| hardened/current/master		| HardenedBSD		| amd64, arm64	| Branche principale de développement (14-CURRENT)			|
| hardened/13-stable/master		| HardenedBSD		| amd64		| développement pour 13-STABLE					|
| hardened/12-stable/master		| HardenedBSD		| amd64		| développement pour 12-STABLE 					|

## Mise à jour de HardenedBSD

HardenedBSD n'utilise pas
[freebsd-update(8)](https://www.freebsd.org/cgi/man.cgi?query=freebsd-update&sektion=8&manpath=freebsd-release-ports).
Au lieu de cela, HardenedBSD utilise un utilitaire connu sous le nom de hbsd-update. 
hbsd-update n'utilise pas de deltas pour publier les mises à jour, mais distribue plutôt le système d'exploitation 
de base dans son ensemble. Ne pas utiliser de deltas entraîne une surcharge de bande passante, 
mais est plus facile à maintenir et à mettre en miroir. hbsd-update s'appuie sur des enregistrements TXT signés DNSSEC 
pour distribuer les informations de version.

Mises à jour pour les branches stables maintenues (13-STABLE, 12-STABLE) proviennent du 
dépôt HardenedBSD-STABLE. Mises à jour pour CURRENT
(hardened/current/master) proviennent du dépôt HardenedBSD.

hbsd-update est configuré via un fichier de configuration placé à
`/etc/hbsd-update.conf`. hbsd-update fonctionne au niveau des branches, 
ce qui signifie qu'il suit les branches de l'arbre source de HardenedBSD. 
Ainsi, la mise à jour d'une version majeure à une autre nécessite de changer 
les variables dnsrec et branch dans le fichier `hbsd-update.conf`. Par exemple, le fichier `hbsd-update.conf`
pour la branche hardened/current/master du dépôt HardenedBSD :

```
dnsrec="$(uname -m).master.current.hardened.hardenedbsd.updates.hardenedbsd.org"
capath="/usr/share/keys/hbsd-update/trusted"
branch="hardened/current/master"
baseurl="http://updates.hardenedbsd.org/pub/HardenedBSD/updates/${branch}/$(uname -m)"
```

Et comme autre exemple, le `hbsd-update.conf` pour la branche hardened/13-stable/master du repo HardenedBSD :

```
dnsrec="$(uname -m).master.13-stable.hardened.hardenedbsd.updates.hardenedbsd.org"
capath="/usr/share/keys/hbsd-update/trusted"
branch="hardened/13-stable/master"
baseurl="http://updates.hardenedbsd.org/pub/HardenedBSD/updates/${branch}/$(uname -m)"
```

Ainsi, la génération d'une différence entre les deux fichiers de configuration en résulterait :

```
--- hbsd-update_current.conf
+++ hbsd-update_13-stable.conf
@@ -1,4 +1,4 @@
-dnsrec="$(uname -m).master.current.hardened.hardenedbsd.updates.hardenedbsd.org"
+dnsrec="$(uname -m).master.13-stable.hardened.hardenedbsd.updates.hardenedbsd.org"
 capath="/usr/share/keys/hbsd-update/trusted"
-branch="hardened/current/master"
+branch="hardened/13-stable/master"
 baseurl="http://updates.hardenedbsd.org/pub/HardenedBSD/updates/${branch}/$(uname -m)"
```

[retour au début](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_fr#)
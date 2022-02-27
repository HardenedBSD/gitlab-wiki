# secadm

* Auteurs: Shawn Webb <shawn.webb@hardenedbsd.org>, Brian Salcedo <brian.salcedo@hardenedbsd.org>, Loic <loic.f@hardenedbsd.org>
* Copyright (c) 2014, 2015 Shawn webb <shawn.webb@hardenedbsd.org>
* Licence: 2-Clause BSD License

https://git.hardenedbsd.org/hardenedbsd/secadm

## Introduction

secadm est un projet visant à remplacer l'intégration
mac_bsdextended(4)/ugidfw(8) que le projet HardenedBSD a fait pour
le durcissement ASLR, SEGVGUARD, et PTrace. Le projet secadm sera
implémenté comme une entrée de ports personnalisés dans le repo
HardenedBSD/freebsd-ports. Le portage sera composé de trois parties
: un module noyau qui intègre le framework MAC, une bibliothèque
partagée qui communique entre le noyau et l'espace utilisateur, et
une application qui consomme la bibliothèque partagée.

Le module MAC fonctionnera sur la base d'une prison. Il
communiquera avec l'espace utilisateur via un noeud sysctl. Le
module MAC devrait s'accrocher à l'appel execve() pour définir des
drapeaux de sécurité/de durcissement par processus, tels que
l'activation de ASLR ou SEGVGUARD. Chaque jail gère ses propres
règles. Les règles appliquées dans une prison n'ont pas
d'interaction ou d'impact sur les autres prisons.
La bibliothèque partagée sera nommée libsecadm et agira simplement
comme une couche de communication entre les applications de
l'utilisateur et le sysctl.

La bibliothèque partagée effectue la même vérification de l'intégrité
et de la conformité pour toutes les modifications de règles, y compris la suppression de règles, que le module MAC.

L'application userland sera nommée secadm. Elle utilisera libsecadm et libucl. Les règles seront écrites en json afin de permettre une
format de fichier de configuration qui est lisible et analysable à
la fois par les humains et les machines. L'utilisation du format
json permettra également une plus grande flexibilité et un contenu
dynamique. On peut imaginer secadm déployé dans une appliance de
sécurité où les jeux de règles sont créés et mis à jour via une
API de service Web.

secadm prend en charge le basculement des restrictions ASLR, mmap(MAP_32BIT), SEGVGUARD, SHLIBRANDOM, PAGEEXEC et MPROTECT. À partir de la version 0.2, secadm introduit également une nouvelle
fonctionnalité Integriforce. Integriforce garantit l'intégrité des
fichiers exécutables avant leur exécution.

## A propos de la version 0.3.0

La version 0.3.0 est une réécriture complète de secadm. Vous
remarquerez que des commandes comme `secadm set` et `secadm list` ne
fonctionnent plus. Elles ont été remplacées par `secadm load
/path/to/file` et `secadm show` respectivement. De plus, des règles
individuelles peuvent être ajoutées et supprimées avec les commandes
`secadm add` et `secadm del`. Les règles peuvent être activées et
désactivées avec les commandes `secadm Activer` et `secadm désactiver`.

Les drapeaux qui peuvent être passés à `secadm add pax` sont :

* A, a: Activer, désactiver ASLR
* B, b: Activer, désactiver la protection de mmap(MAP_32BIT)
* L, l: Activer, désactiver SHLIBRANDOM
* M, m: Activer, désactiver MPROTECT
* P, p: Activer, désactiver PAGEEXEC
* S, s: Activer, désactiver SEGVGUARD
* O, o: Activer, désactiver le dépassement des règles FS-EA basé sur hbsdcontrol

Par défaut, `secadm show` affichera le jeu de règles actif dans un
format abrégé. secadm s'intègre maintenant avec libxo pour fournir
une sortie du jeu de règles dans les formats JSON, UCL, ou XML.
Spécifiez un format différent en utilisant l'option -f de `secadm
show`. Par exemple, `secadm show -f ucl`.

## Ordre d'évaluation des règles

Lorsque le noyau est compilé avec l'option noyau PAX_CONTROL_EXTATTR, l'ordre d'évaluation est secadm puis hbsdcontrol. Cela garantit que
les paramètres de hbsdcontrol sont toujours prioritaires. Pour que les règles de secadm soient prioritaires, utilisez l'indicateur O pour cette règle (prefer_acl est l'option longue).

## Exigences

* Version HardenedBSD 1200055 ou supérieure :
  - `sysctl hardening.version` devrait montrer 1200055 minimum
* Noyau HardenedBSD compilé avec les options PAX_CONTROL_ACL
* textproc/libucl
* textproc/libxo

## Installation et utilisation

```
# make
# make depend all install
```

Pour lister les fonctionnalités par application que votre version de secadm supporte :

```
# secadm list features
```

Pour charger le module noyau secadm :

```
# kldload secadm
```

Copiez l'exemple de jeu de règles au bon endroit :

```
# cp etc/secadm-desktop.rules.example /usr/local/etc/secadm.rules
```

Modifiez vos règles :

```
# vi /usr/local/etc/secadm.rules
```

Activez-les. Veuillez noter que la définition d'un nouveau jeu de
règles entraînera la suppression des règles précédemment chargées.

```
# secadm load /usr/local/etc/secadm.rules
```

Pour vérifier que votre jeu de règles a été chargé avec succès :

```
# secadm list
```

Pour purger les règles :

```
# secadm flush
```

Installation dans une prison
------------------------
La bibliothèque partagée libsecadm et l'application secadm userland doivent toutes deux être installées ou accessibles dans chaque jail individuellement afin d'appliquer les politiques de sécurité à l'intérieur du jail.

Remarque : si les prisons sont configurées pour utiliser une basejail en lecture seule, l'installation manuelle de libsecadm.0.so dans le répertoire /usr/lib de la basejail est nécessaire.

Rédaction des règles d'application
=========================

secadm supporte actuellement l'activation de ASLR, SEGVGUARD, le
durcissement de mprotect(exec), et sur certains builds HardenedBSD,
le durcissement de PAGEEXEC. Dans le répertoire etc, vous trouverezsecadm.rules.sample, qui montre comment écrire des règles.

Vous pouvez utiliser le mot-clé prefer_acl pour que la règle de
secadm prenne le pas sur les paramètres basés sur les attributs
étendus du système de fichiers.
secadm utilise libucl pour analyser son fichier de configuration. En l'état actuel des choses.

Pour l'instant, l'ordre des règles n'a pas d'importance, mais cela pourrait changer avec le temps, à mesure que nous ajoutons de nouvelles fonctionnalités. L'exemple de fichier de configuration est dans un format JSON détendu, bien que libucl supporte différentes syntaxes. Veuillez vous référer à la documentation de libucl pour vous aider à apprendre les différentes syntaxes possibles.

```
secadm {
	pax {
		path: "/bin/ls",
		aslr: false,
		segvguard: false
	},
	pax {
		path: "/bin/pwd",
		mprotect: true,
		pageexec: true,
		prefer_acl: true
	}
}
```

## Integriforce

secadm version 0.2 prend en charge une nouvelle fonctionnalité,
appelée Integriforce. Cette fonctionnalité permet de contrôler
l'intégrité des fichiers exécutables. Si une règle existe pour un
fichier donné, le hachage de ce fichier tel que défini dans la
règle est comparé au hachage du fichier. Si les hachages ne
correspondent pas, l'exécution peut être interdite, selon les
paramètres de configuration. Integriforce est une fonction
optionnelle, mais puissante. Integriforce ne supporte actuellement
que SHA1 ou SHA256.

OTE : Les fichiers qui sont sous la gestion d'Integriforce ne
peuvent pas être modifiés ou supprimés. L'ensemble de règles devra
être nettoyé avant d'être mis à jour.
modifier ou supprimer le fichier.

### Configurer Integriforce

Dans l'objet racine du fichier de configuration, secadm recherche unobjet integriforce. Dans l'objet integriforce, ajoutez un tableau de fichiers. Dans le tableau de fichiers, placez un tableau d'objets, où chaque objet contient les options suivantes :

1. mode (chaîne de caractères, par défaut "hard") : S'il est défini,
il doit être égal à "soft" ou "hard". Le mode doux signifie que
l'exécution est autorisée si les hachages ne correspondent pas, mais
un message d'avertissement est imprimé. Le mode dur affiche un
message d'erreur et interdit l'exécution.
1. files (tableau d'objets) : Chaque objet doit contenir les
champs suivants :
   1. path (chaîne) : Le chemin vers l'exécutable.
   1. hash (chaîne) : Le hachage (sha1 ou sha256) de l'exécutable.
   1. type (chaîne) : Soit "sha1", soit "sha256".
   1. mode (facultatif, chaîne de caractères, hériter par défaut)
      : Le mode d'application pour ce fichier.

Exemple de configuration :

```
secadm {
	integriforce {
		path: "/bin/ls",
		hash: "873e49767e36f80a8814f41c90c98d888c83eeb4fe2fcab155b5ecb6cc6b67f6",
		type: "sha256",
		mode: "hard"
	}
}
```

### Application stricte Autoriser l'inscription

Lorsque des règles Integriforce ont été ajoutées, `secadm` peut
être placé en mode d'autorisation stricte des applications
(whitelist). `secadm` interdira l'exécution d'applications et le
chargement de bibliothèques partagées n'ayant pas d'entrée
correspondante dans la configuration d'Integriforce.

Pour activer le mode liste d'autorisation stricte des applications :

```
# secadm set -W
```

Pour désactiver le mode liste d'autorisation stricte des applications :

```
# secadm set -w
```

Pour activer le mode strict de liste d'autorisation des applications
dans le fichier de configuration `secadm` :

```
secadm {
	whitelist_mode: true
}
```

L'ajout de secadm lui-même et des bibliothèques partagées dont ildépend à la configuration d'Integriforce est fortement recommandé.
Sinon, la configuration de secadm ne peut pas être modifiée sans
redémarrer le système au préalable.

## Exécution du chemin de confiance (TPE)

Trusted Path Execution empêche les utilisateurs d'exécuter directement
des binaires dans des répertoires non fiables. Un répertoire de
confiance est défini comme un répertoire accessible en écriture
uniquement par root et appartenant à root. L'implémentation actuelle
de TPE ne gère pas l'exécution indirecte de scripts.

Les options TPE qui peuvent être définies :

1. `enable`:
   * Requis. Type : Booléen
   * Description : Active les protections TPE.
1. `all`:
   * Facultatif. Type : Booléen
   * Description : Activer le TPE pour tous les utilisateurs.
1. `invert`:
   * Facultatif. Type : Booléen
   * Description : Inverse la logique de l'ID de groupe (GID).
1. `gid`:
   * Facultatif. Type : Nombre entier
   * Description : ID du groupe pour lequel le TPE est appliqué.

L'activation de TPE via la ligne de commande :

```
# secadm tpe -TA
```

Configurer TPE dans le fichier de configuration `secadm` :

```
secadm {
	tpe {
		Activer: true,
		all: true,
		invert: false,
		gid: 1000
	}
}
```

## Note sur la stabilité de l'ABI et de l'API

L'ABI et l'API de l'userland et du kernel sont en plein
développement. Bien que nous ayons pris soin de garder à l'esprit
les changements et fonctionnalités futurs, l'API et l'ABI ne sont
pas stables et peuvent changer de version en version. Si vous prévoyez de développer des applications tierces qui utilisent
libsecadm, vous le faites à vos risques et périls. Si vous pensez
avoir besoin de fonctionnalités supplémentaires ou d'une
modification d'une fonctionnalité existante, veuillez déposer un
rapport de bogue dans le traqueur de problèmes de secadm sur
l'instance GitLab auto-hébergée de HardenedBSD.
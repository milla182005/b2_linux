# TP3 Dév : IDS

**Le but de ce TP est de développer un outil (sommaire) qui remplirait une des tâches que nous les admins on a besoin.**

> Là encore je vous demande de faire preuve d'esprit, et de voir au-delà du sujet pour déceler les problématiques cools, et les problèmes algorithmiques rigolos.

Vous allez développer un **IDS** :

- c'est pour *Intrusion Detection System*
- détection d'intrusion
- surveille des fichiers du disque et vérifie qu'ils n'ont pas changé
- surveille éventuellement d'autres trucs

## Sommaire

- [TP3 Dév : IDS](#tp3-dév--ids)
  - [Sommaire](#sommaire)
- [0. Setup](#0-setup)
- [I. Instructions générales](#i-instructions-générales)
- [II. Instructions IDS](#ii-instructions-ids)
- [III. Marche à suivre](#iii-marche-à-suivre)
  - [1. Conseils pour commencer et avancer](#1-conseils-pour-commencer-et-avancer)
  - [2. Rendu attendu](#2-rendu-attendu)
- [III. Bonus](#iii-bonus)

# 0. Setup

➜ **Vous créerez un dépôt git dédié à l'hébergement de votre programme**

- le README de rendu du TP (dans votre dépôt pour les TPs Linux) contient juste le lien vers ce dépôt dédié

➜ **En Python évidemment à priori**

- mais vous êtes libres sur le choix du langage en vrai

➜ **Solo ou Duo**

- à vous de voir ce que vous préférez
- en binôme c'est cool, on va plus vite, plus loin, on confronte les idées, et git est là pour bosser à 2

# I. Instructions générales

![App](./img/app.svg)

➜ **Linux-friendly**

- vous développez un outil qui est destiné à être exécuté sur des machines Linux
- faites vos tests avec une VM (pas un conteneur)

➜ **Output**

- l'output doit être à peu près joli et lisible

➜ **Options**

- votre programme doit supporter des options au lancement, au moins un `--help`
- je vous donne + de détails sur les options qui doivent être dispos dans la section dédiée à chaque sujet

➜ **Fichier de conf**

- peu importe l'outil que vous avez choisi de dév, il doit utiliser un fichier de conf
- le fichier de conf sera écrit en JSON
- il contiendra des trucs différents suivant le sujet que vous choisissez, détails dans la partie dédiée
- le fichier de conf qui est lu par défaut doit être dans le path standard sous les OS Linux : `/etc/`
  - il faut un sous-dossier qui porte le nom du programme
  - s'il n'existe pas, votre programme le crée au premier lancement

➜ **Logs**

- votre outil doit log ce qu'il fait, toutes les actions qu'il réalise
- vous utilisez [des niveaus de logs](https://en.wikipedia.org/wiki/Syslog#Severity_level) : `INFO`, `WARNING`, `ERROR`
- vous utilisez un timestamp au format standard : [ISO 8601](https://fr.wikipedia.org/wiki/ISO_8601)
  - [voir ici pour le faire en Python](https://stackoverflow.com/questions/2150739/iso-time-iso-8601-in-python)
- le fichier de log doit être par défaut dans le path standard sous les OS Linux : `/var/log/`
  - il faut un sous-dossier qui porte le nom du programme
  - s'il n'existe pas, votre programme le crée au premier lancement

➜ **Un README**

- un beau README qui explique comment se servir de l'outil
- il doit comporter :
  - une section `Installation` qui indique comment installer votre outil
    - faut installer des paquets ? des dépendances ? Docker ?
    - vous donnez les commandes, faites ça clean, vous avez lu quelques README sur Github vous-mêmes non ?
  - une section `Usage` qui montre un exemple simple d'utilisation
    - en début de section vous montrez une exécution du `--help`
    - un exemple d'utilisation simple
  - une section `Configuration`
    - qui montre ce qu'on peut mettre dans le fichier de conf

➜ **Une unité systemd**

- il faudra remettre un fichier `ids.service` qui permettra de lancer votre programme comme un service système
  - le service lancera une commande spécifique de votre programme
  - j'indiquerai avec l'emoji 🚩 la commande que doit lancer le service
- et aussi `ids.timer` pour que le service soit déclenché à intervalles réguliers
  - l'IDS déclenchera des `check` à intervalles réguliers

➜ **Un code clean SVP**

- nommage de fonctions, de variables
- éclatez votre code en plusieurs fichiers
- pas ré-écrire le même code 1000x
- etc.
- une bonne fonction
  - elle fait moins de 20 lignes
  - si ta fonction est (beaucoup) plus longue, alors tu dois pouvoir la split en plusieurs fonctions et ça aurait plus de sens

➜ **Le but c'est pas d'appeler des commandes shell**

- vous devez *coder* le programme qui fait les trucs, et pas juste appeler `ls` pour connaître les propriétés d'un fichier

# II. Instructions IDS

![Watchin](./img/watching.png)

Un IDS est un détecteur d'intrusion (*Intrusion Detection System*). Ici on parle d'un programme qui va surveiller les fichiers du disque dur pour savoir s'ils ont été modifiés.

> *Il peut aussi surveiller d'autres choses, comme les ports en cours d'utilisation, la liste des processus, les syscalls passés, etc.*

On utilise vraiment ce genre d'outils en contexte réel, car **certains fichiers ne sont normalement plus jamais modifiés après leur configuration initiale.** Toute modification peut être considéré comme suspecte.

> *Par exemple, on surveille `/etc/shadow`, ou le fichier de configuration du serveur SSH `/etc/ssh/sshd_config`.*

On va donc développer ici un ptit `ids.py` qui surveille l'état de certains fichiers sur le disque et deux trois autres trucs.

➜ **Utilisation du programme `ids.py`**

- toutes les actions doivent générer au moins une ligne de logs indiquant que la commande a été appelée
- `ids.py build`
  - construit un fichier JSON qui contient un état des choses qu'on a demandé à surveiller
  - ce fichier est stocké dans `/var/ids/db.json`
- `ids.py check`
  - vérifie que l'état actuel est conforme à ce qui a été stocké dans `/var/ids/db.json`
  - si quelque chose a changé, alors il faut le signaler
  - retourne un rapport au format JSON indiquant
    - juste `{"state":"ok"}` si rien a bougé par rapport à `/var/ids/db.json`
    - sinon affiche un rapport de ce qui a changé en commençant par `{"state":"divergent"}`
  - 🚩 cette commande doit être appelée dans le service `backup.service`

➜ **Fichier de conf**

- contient une liste de fichier à surveiller
- contient une liste de dossiers à surveiller : on surveille tous les sous fichiers aussi
- indique si on souhaite surveiller les ports en écoute ou non

➜ **Le fichier `/var/ids/db.json`** doit contenir

- heure et date du `build` (quand ce fichier a été généré)
- l'état des fichiers à surveiller
  - hash SHA512
  - hash SHA256
  - hash MD5
  - date de dernière modif du fichier
  - date de création du fichier
  - propriétaire du fichier
  - groupe propriétaire du fichier
  - taille du fichier
- un rapport sur les ports TCP/UDP en écoute

# III. Marche à suivre

## 1. Conseils pour commencer et avancer

➜ **Prenez un peu la main sur les libs/méthodes dont vous allez avoir besoin**

- regardez comment lire/écrire du JSON depuis Python
- regardez comment consulter les propriétés d'un fichier ou d'un dossier

➜ **Commencez simple**

- un simple programme qui lit les propriétés du fichier `/etc/shadow` et les enregistre dans un fichier JSON
- passez ensuite à un programme qui peut passer sur plusieurs fichiers
- ensuite, intégrez la gestion d'un fichier de conf, pour que ce soit plus simple de lui passer la liste des fichiers à surveillers
- etc. etc. petit à petit !

➜ **Ne perdez pas de temps, et codez !**

- c'est en faisant qu'on apprend
- je vous recommande de désactiver Copilot (aidez-vous un peu de ChatGPT si vous voulez :| )

## 2. Rendu attendu

> ***A rendre dans un dépôt git dédié.***

🌞 **L'IDS**

- le code Python
- éclaté en plusieurs fichiers
- concentrez-vous sur ça d'abord, vous verrez tout le reste plus tard pour le rendu : **d'abord, le code !**

---

🌞 **Packaging systemd**

- un fichier `.service` qui permet de lancer votre IDS
- un fichier `.timer` qui permet de lancer à intervalles réguliers le *service*

🌞 **README.md**

- un README.md clean à la racine du dépôt git
- il explique comment installer et utiliser votre outil

🌞 **Example de fichier de configuration**

- un exemple de fichier de configuration devra être fourni

# III. Bonus

⭐ **Bonus API**

- il existe un deuxième programme `ids-api.py` qui expose une API HTTP sur un port donné
- si on interroge cette API, on peut :
  - effectuer un check
    - `POST /check`
  - récupérer le rapport au format JSON
    - `GET /reports` : tous les rapports
    - `GET /reports/<ID>` : un seul rapport
- j'vous ai écrit [un bout de cours sur les API avec Python Flask](../../../cours/dev/api/README.md)

⭐ **Bonus alertes**

- si un `check` ne passe pas, vous envoyez une alerte
- par exemple, dans un salon Discord qu'on configure dans le fichier de conf

⭐ **Bonus surveillance**

- surveillez d'autres éléments du systèmes :
  - les ports utilisés (en écoute et en connexion établie)
  - les processus en cours d'utilisation
  - d'autres choses ? Soyez créatifs

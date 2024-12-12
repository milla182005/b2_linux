# TP2 Commun : Stack Web

![PHP](./img/php.jpg)

**Droit au but : vous allez conteneuriser votre une mini app Web qui a besoin d'une base de données.**

> *Apprendre à setup un env Docker pour avoir besoin ni de VM, ni d'installer quoique ce soit sur votre PC.*

Le but donc :

➜ **avoir un seul `docker-compose.yml` qui lance tout**

- un conteneur avec l'app web installée
  - je vous file [une app simpliste d'exemple ](./app/)
- un conteneur base de données MySQL
- un conteneur PHPMyAdmin pour gérer la base

➜ **on utilise des images officielles dans l'idéal**

- on évite de rédiger des `Dockerfile` si on peut
- surtout si c'est des images officielles

➜ **donc pour bosser sur un projet comme celui-ci :**

- `docker compose up`
- tu dév sur ta machine, ça s'exécute sur des conteneurs
- `docker compose down` quand t'as fini de dév, plus rien qui tourne

➜ **et surtout : juste un fichier `docker-compose.yml` à se partager au sein du groupe si plusieurs personnes à bosser dessus**

- quelques lignes
- pour avoir exactement le même environnement
- à la racine du projet dans le dépôt git c'est carré

## Sommaire

- [TP2 Commun : Stack Web](#tp2-commun--stack-web)
  - [Sommaire](#sommaire)
- [0. Setup](#0-setup)
- [I. Packaging de l'app Web](#i-packaging-de-lapp-web)

# 0. Setup

➜ **Dans le TP, l'emoji 📜 figurera à la fin de chaque partie pour t'indiquer quels fichiers tu dois rendre**

T'es un dév. Tu dév avec ta machine, ton OS. Donc ça veut dire...

➜ **Installer Docker sur votre PC**

- pas dans une VM quoi, sur ton PC
- doc officielle
- je préviens tout de suite pour les Windowsiens :
  - Docker nécessite soit WSL soit Hyper-V
  - je vous recommande WSL parce que Hyper-V il va péter votre VirtualBox
  - et même avec WSL, magic happens
  - on l'a vu en cours et premier TP, Docker, c'est une techno Linux...

> Même si j'étais dév sous Windows, je préférerai lancer moi-même une VM Linux et faire deux trois bails d'intégration pour que je puisse lancer des commandes `docker run` sur mon PC qui lance des conteneurs dans la VM. Je peux vous apprendre c'est pas compliqué, faut juste lancer la VM quand tu veux use Docker (au lieu de lancer Docker, ça revient au même quoi finalement, t'façon il lance un noyau Linux pour toi le bougre si tu le fais pas toi-même).

![Docker on Windows](./img/docker_on_windows.jpg)

# I. Packaging de l'app Web

J'vous oriente dans la démarche :

➜ **on a dit qu'on voulait 3 conteneurs**

- 1 web, 1 db, 1 PhpMyAdmin
- parce qu'on est pas des animaux à tout mettre dans le même
- un conteneur = un process please

➜ **d'abord on prend des infos sur les images dispos**

- [Python y'a une image officielle](https://hub.docker.com/_/python), lisez le README pour voir comment s'en servir
  - on dirait que le plus simple c'est de faire votre propre Dockerfile
  - surtout si vous avez besoin d'ajouter des libs
  - à vous de voir, lisez attentivement le README
- [idem pour MySQL](https://hub.docker.com/_/mysql)
  - là pas besoin de Dockerfile, on utilise direct l'image
  - on peut config :
    - un user et son password, ainsi qu'une database à créer au lancement du conteneur
    - direct via des variables d'environnement
    - c'est de ouf pratique
  - [on peut aussi jeter un fichier `.sql`](./app/seed.sql)
  - cat se) dans le bon dossier (lire le README) avec un volume, et il sera exécuté au lancement
    - parfait pour créer un schéma de base
- par contre pour PHPMyAdmin
  - pas d'image officielle
  - cherchez sur le Docker Hub y'a plusieurs gars qui l'ont packagé
  - c'est très répandu, donc y'a forcément une image qui fonctionne bien

➜ **ensuite on run les bails**

- vous pouvez jouer avec des `docker run` un peu pour utiliser les images et voir comment elles fonctionnent
- il va falloir lire [le fichier de seed SQL](./app/seed.sql) et le [code de l'application Web](./app/main.py) pour proposer un conteneur MySQL dispo
- rapidement passer à la rédaction d'un `docker-compose.yml` qui lance l'app Web + sa base de données
- lisez bien les README de vos images, y'a tout ce qu'il faut
- vous pouvez ajouter le conteneur PhpMyAdmin une fois que tout fonctionne
  - il doit aussi pouvoir se co à la base de données

Pour ce qui est du contenu du `docker-compose.yml`, à priori :

- **il déclare 3 conteneurs**
  - **Web**
    - un volume qui place le code Python dans le conteneur
    - une variable d'environnement pour choisir le port d'écoute
    - une variable d'environnement pour choisir à quelle nom/IP l'app doit utiliser pour se connecter à la base de données
    - partage de port pour accéder au "site", une vieille API nulle
  - **MySQL**
    - définition du mot de passe `root` avec une variable d'environnement
    - injection [d'un fichier `.sql`](./app/seed.sql)
      - pour créer votre schéma de base au lancement du conteneur
      - et injecter des données simulées
  - **PHPMyAdmin**
    - dépend de l'image que vous utilisez
    - partage de port pour accéder à l'interface de PHPMyAdmin

> *Pour rappel : dès qu'un conteneur est déclaré dans un `docker-compose.yml` il peut joindre tous les autres via leurs noms sur le réseau. Et c'est bien pratique. **Nik les adresses IPs.***

Bon j'arrête de blabla, voilà le soleil.

🌞 **`docker-compose.yml`**

- dans votre dépôt git de rendu
- bah juste voilà ça doit fonctionner : je git clone ton truc, je `docker compose up` et ça doit fonctionner :)

➜ **Un environnement de dév local propre avec Docker**

- 3 conteneurs, donc environnement éphémère/destructible
- juste un **`docker-compose.yml`** donc facilement transportable
- TRES facile de mettre à jour chacun des composants si besoin
  - oh tiens il faut ajouter une lib !
  - oh tiens il faut une autre version de Python !
  - tout ça c'est np

![save urself](img/save_urself.png)

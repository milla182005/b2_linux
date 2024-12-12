
## 3. sudo c pa bo

🌞 **Ajouter votre utilisateur au groupe `docker`**

```
[ingrid@vbox ~]$ usermod -aG docker ingrid
usermod: Permission denied.
usermod: cannot lock /etc/passwd; try again later.
[ingrid@vbox ~]$ sudo !!
sudo usermod -aG docker ingrid
[sudo] password for ingrid:
[ingrid@vbox ~]$

```

## 4. Un premier conteneur en vif

> *Je rappelle qu'un "conteneur" c'est juste un mot fashion pour dire qu'on lance un processus un peu isolé sur la machine.*

Bon trève de blabla, on va lancer un truc qui juste marche.

On va lancer un conteneur NGINX qui juste fonctionne, puis custom un peu sa conf. Ce serait par exemple pour tester une conf NGINX, ou faire tourner un serveur NGINX de production.

> *Hé les dévs, **jouez le jeu bordel**. NGINX c'est pas votre pote OK, mais on s'en fout, c'est une app comme toutes les autres, comme ta chatroom ou ta calculette. Ou Netflix ou LoL ou Spotify ou un malware. NGINX il est réputé et standard, c'est juste un outil d'étude pour nous là. Faut bien que je vous fasse lancer un truc. C'est du HTTP, c'est full standard, vous le connaissez, et c'est facile à tester/consommer : avec un navigateur.*

🌞 **Lancer un conteneur NGINX**

- avec la commande suivante :

```bash
[ingrid@vbox ~]$ docker run -d -p 9999:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
bc0965b23a04: Pull complete
650ee30bbe5e: Pull complete
8cc1569e58f5: Pull complete
362f35df001b: Pull complete
13e320bf29cd: Pull complete
7b50399908e1: Pull complete
57b64962dd94: Pull complete
Digest: sha256:fb197595ebe76b9c0c14ab68159fd3c08bd067ec62300583543f0ebda353b5be
Status: Downloaded newer image for nginx:latest
ea540bd4420d127902f4dceaed158de56a8752ac95f362c90808e900e8752347
```

🌞 **Visitons**

- vérifier que le conteneur est actif avec une commande qui liste les conteneurs en cours de fonctionnement

````
1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645
[ingrid@vbox nginx]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                                 NAMES
1c34bd36e2bb   nginx     "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes   80/tcp, 0.0.0.0:9999->8080/tcp, [::]:9999->8080/tcp   condescending_shamir

````

- afficher les logs du conteneur

````
[ingrid@vbox nginx]$ docker logs 1c34bd36e2bb
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/12/11 20:18:55 [notice] 1#1: using the "epoll" event method
2024/12/11 20:18:55 [notice] 1#1: nginx/1.27.3
2024/12/11 20:18:55 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2024/12/11 20:18:55 [notice] 1#1: OS: Linux 5.14.0-427.26.1.el9_4.x86_64
2024/12/11 20:18:55 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1073741816:1073741816
2024/12/11 20:18:55 [notice] 1#1: start worker processes
2024/12/11 20:18:55 [notice] 1#1: start worker process 29

````
- afficher toutes les informations relatives au conteneur avec une commande `docker inspect`

````
[ingrid@vbox nginx]$ docker inspect 1c34bd36e2bb
[
    {
        "Id": "1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645",
        "Created": "2024-12-11T20:18:47.49491183Z",
        "Path": "/docker-entrypoint.sh",
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 15915,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2024-12-11T20:18:49.502720314Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:66f8bdd3810c96dc5c28aec39583af731b34a2cd99471530f53c8794ed5b423e",
        "ResolvConfPath": "/var/lib/docker/containers/1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645/hostname",
        "HostsPath": "/var/lib/docker/containers/1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645/hosts",
        "LogPath": "/var/lib/docker/containers/1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645/1c34bd36e2bb5deb1cbd102060b1f228c80c47059cb07f5d0e1c13f2c3f84645-json.log",
        "Name": "/condescending_shamir",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/home/ingrid/nginx/index.html:/var/www/html/index.html",
                "/home/ingrid/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge",
            "PortBindings": {
                "8080/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "9999"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                41,
                156
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/0fd9de32804fb7d793fa9d854d23ddc59f9b724ed6f6445386c8a1dc42ebdcf8-init/diff:/var/lib/docker/overlay2/b4637377267beaca1aae3623432c6da43dded25408dc0046f96f0fb5b5393551/diff:/var/lib/docker/overlay2/51155cf7a2705d362f505b8f9cdd9711a572f2eb16caa1a88e56d052a7639744/diff:/var/lib/docker/overlay2/93c034bfd3c1c0343eb9cc2aee814ae8bfce9503c0b182b958705812459196bf/diff:/var/lib/docker/overlay2/8d6c28d1f80e3049c7e41a59cdf91c63c0719c1e7e6c30ae493f3a8439559721/diff:/var/lib/docker/overlay2/413badc719dbd59c589a5e7e5f15316111fb566bf4823f2c964f0687597d6eb6/diff:/var/lib/docker/overlay2/90cfb0bc6d8160e60da1b82a3b37871e662314f7e9496db6a356c40ac78c5e11/diff:/var/lib/docker/overlay2/61f93a5dc07036d177d93131e9dd1b4c4619af79bfe6da35b2c35f21f2d7bfde/diff",
                "MergedDir": "/var/lib/docker/overlay2/0fd9de32804fb7d793fa9d854d23ddc59f9b724ed6f6445386c8a1dc42ebdcf8/merged",
                "UpperDir": "/var/lib/docker/overlay2/0fd9de32804fb7d793fa9d854d23ddc59f9b724ed6f6445386c8a1dc42ebdcf8/diff",
                "WorkDir": "/var/lib/docker/overlay2/0fd9de32804fb7d793fa9d854d23ddc59f9b724ed6f6445386c8a1dc42ebdcf8/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/ingrid/nginx/index.html",
                "Destination": "/var/www/html/index.html",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/home/ingrid/nginx/site_nul.conf",
                "Destination": "/etc/nginx/conf.d/site_nul.conf",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "1c34bd36e2bb",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {},
                "8080/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.27.3",
                "NJS_VERSION=0.8.7",
                "NJS_RELEASE=1~bookworm",
                "PKG_RELEASE=1~bookworm",
                "DYNPKG_RELEASE=1~bookworm"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "nginx",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "3bbc65f74646f392e41b6ca4b0adb0fc7842a10c8ea8f0a011d5743f7ae03ab8",
            "SandboxKey": "/var/run/docker/netns/3bbc65f74646",
            "Ports": {
                "80/tcp": null,
                "8080/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "9999"
                    },
                    {
                        "HostIp": "::",
                        "HostPort": "9999"
                    }
                ]
            },
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "445bcb4466081c55ffe188885f313082da1287b35f7d637143364d6e08039d74",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null,
                    "NetworkID": "a8eb0c9a4f58998d779247ebbb6e7bb2b3ec3e65b498ff886edf62547b376276",
                    "EndpointID": "445bcb4466081c55ffe188885f313082da1287b35f7d637143364d6e08039d74",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": null
                }
            }
        }
    }
]
````
- ouvrir le port `9999/tcp` (vu dans le `ss` au dessus normalement) dans le firewall de la VM

````
[ingrid@vbox ~]$ sudo firewall-cmd --permanent --add-port=9999/tcp
success
[ingrid@vbox ~]$ sudo firewall-cmd --reload
success
[ingrid@vbox ~]$ sudo firewall-cmd --list-ports
9999/tcp

````
- depuis le navigateur de votre PC, visiter le site web sur `http://IP_VM:9999`

````
[ingrid@vbox nginx]$  curl http://10.1.1.10:9999/
<h1>MEOOOW</h1>

````


🌞 **On va ajouter un site Web au conteneur NGINX**

- créez un dossier `nginx`

````
[ingrid@vbox nginx]$ mkdir -p /home/ingrid/nginx/

````
  - pas n'importe où, c'est ta conf caca, c'est dans ton homedir donc `/home/<TON_USER>/nginx/`
- dedans, deux fichiers : `index.html` (un site nul) `site_nul.conf` (la conf NGINX de notre site nul)
- exemple de `index.html` :

```html
[ingrid@vbox nginx]$ echo "<h1>MEOOOW</h1>" > /home/ingrid/nginx/index.html

```

- config NGINX minimale pour servir un nouveau site web dans `site_nul.conf` :

```nginx
[ingrid@vbox nginx]$ echo "server {
    listen 8080;

    location / {
        root /var/www/html;
        index index.html;
    }
}" > /home/ingrid/nginx/site_nul.conf

```

- lancez le conteneur avec la commande en dessous, notez que :
  - on partage désormais le port 8080 du conteneur (puisqu'on l'indique dans la conf qu'il doit écouter sur le port 8080)
  - on précise les chemins des fichiers en entier
  - note la syntaxe du `-v` : à gauche le fichier à partager depuis ta machine, à droite l'endroit où le déposer dans le conteneur, séparés par le caractère `:`
  - c'est long putain comme commande

```bash
[ingrid@vbox nginx]$ docker run -d -p 9999:8080   -v /home/ingrid/nginx/index.html:/var/www/html/index.html   -v /home/ingrid/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf   nginx
8a54aa43bdddbb8f5c2484850898629180901d385bcd32518fcc720373001823

```

🌞 **Visitons**

- vérifier que le conteneur est actif

````
[ingrid@vbox nginx]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                                 NAMES
8a54aa43bddd   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 3 seconds   80/tcp, 0.0.0.0:9999->8080/tcp, [::]:9999->8080/tcp   practical_mccarthy

````
- aucun port firewall à ouvrir : on écoute toujours port 9999 sur la machine hôte (la VM)
- visiter le site web depuis votre PC

````
[ingrid@vbox nginx]$  curl http://10.1.1.10:9999/
<h1>MEOOOW</h1>

````

## 5. Un deuxième conteneur en vif

Cette fois on va lancer un conteneur Python, comme si on voulait tester une nouvelle lib Python par exemple. Mais sans installer ni Python ni la lib sur notre machine.

On va donc le lancer de façon interactive : on lance le conteneur, et on pop tout de suite un shell dedans pour faire joujou.

🌞 **Lance un conteneur Python, avec un shell**

- il faut indiquer au conteneur qu'on veut lancer un shell
- un shell c'est "interactif" : on saisit des trucs (input) et ça nous affiche des trucs (output)
  - il faut le préciser dans la commande `docker run` avec `-it`
- ça donne donc :

```bash
# on lance un conteneur "python" de manière interactive
# et on demande à ce conteneur d'exécuter la commande "bash" au démarrage
docker run -it python bash
```

> *Ce conteneur ne vit (comme tu l'as demandé) que pour exécuter ton `bash`. Autrement dit, si ce `bash` se termine, alors le conteneur s'éteindra. Autrement diiiit, si tu quittes le `bash`, le processus `bash` va se terminer, et le conteneur s'éteindra. C'est vraiment un conteneur one-shot quoi quand on utilise `docker run` comme ça.*

🌞 **Installe des libs Python**

- une fois que vous avez lancé le conteneur, et que vous êtes dedans avec `bash`
- installez deux libs, elles ont été choisies complètement au hasard (avec la commande `pip install`):
  - `aiohttp`
  - `aioconsole`
- tapez la commande `python` pour ouvrir un interpréteur Python
- taper la ligne `import aiohttp` pour vérifier que vous avez bien téléchargé la lib

> *Notez que la commande `pip` est déjà présente. En effet, c'est un conteneur `python`, donc les mecs qui l'ont construit ont fourni la commande `pip` avec !*

➜ **Tant que t'as un shell dans un conteneur**, tu peux en profiter pour te balader. Tu peux notamment remarquer :

- si tu fais des `ls` un peu partout, que le conteneur a sa propre arborescence de fichiers
- si t'essaies d'utiliser des commandes usuelles un poil évoluées, elles sont pas là
  - genre t'as pas `ip a` ou ce genre de trucs
  - un conteneur on essaie de le rendre le plus léger possible
  - donc on enlève tout ce qui n'est pas nécessaire par rapport à un vrai OS
  - juste une application et ses dépendances

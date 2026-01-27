# B1 — Linux

## TP 1

***Déterminer l'adresse de réseau à partir d'une adresse IP et du masque associé***

    Adresse IP : 10.33.69.157
    Masque : 255.255.240.0 (/20)

→ Adresse de réseau : `10.33.64.0`

## TP 2

*Les commandes indiquées dans ce compte rendu, ainsi que leurs résultats, ont été exécutées depuis un Mac.*

### I. Exploration locale en solo

#### A. Affichage d'informations sur la pile TCP/IP locale ####

***En CLI***

    ifconfig
> Cette commande permet d'afficher les détails et l'état de toutes les cartes réseau, notamment l'adresse MAC de chacune d'entre elles devant la ligne `ether`.

![Screen1](/TP2/Screen1.png)

On observe ici les détails de connexion de `en0` (notre carte Wi-Fi) : l'IP devant `inet`, la MAC devant `ether`, et la broadcast devant `broadcast`.

    route get default
> Cette commande permet d'afficher les détails de la route par défaut.

![Screen2](/TP2/Screen2.png)

On retrouve la passerelle devant la ligne `gateway`.

***En GUI***

![Screen3](/TP2/Screen3.png)
On retrouve ici l'IP (`Adresse IP`), la MAC (`Adresse Wi-Fi`) et la gateway (`Routeur`).

***À quoi sert la gateway dans le réseau d'Ingésup ?***

La gateway dans le réseau d’Ingésup sert de passerelle entre le réseau local et le reste de l’infra (par exemple Internet). Sans cette passerelle, les machines ne pourraient pas sortir de leur sous-réseau donc ne pourraient pas joindre ce qu'il y a en dehors (encore une fois, Internet par exemple)...

#### B. Modifications des informations

On a :
* Adresse IP : 10.33.75.87
* Masque : 255.255.240.0 (/20)
* Adresse de broadcast : 10.33.79.255

Comme on a le masque /20, le bloc couvre de 10.33.64.0 à 10.33.79.255.
Donc l’adresse de réseau est : 10.33.64.0.
La plage est donc : 10.33.64.1 - 10.33.79.254.

![Screen4](/TP2/Screen4.png)

Après scan on peut voir qu'il y a certains trous parmis les IP des hôtes, par exemple on a `10.33.64.54` et `10.33.64.68`, mais rien entre les deux. On peut donc prendre l'adresse `10.33.64.66` par exmeple......

![Screen5](/TP2/Screen5.png)

On modifie donc les paramètres IPv4 de la machine : on passe la config en manuel (ou manuel/DHCP) et on entre une nouvelle IP.

![Screen6](/TP2/Screen6.png)

Après validation et retour dans les infos réseau, on voit que l'Adresse IP a changé pour celle que l'on a mis.

### II. Exploration locale en duo

#### A. Création du réseau



#### B. Modification d'adresse IP



#### C. Utilisation d'un des deux comme gateway



#### D. Petit chat privé ?



#### E. Wireshark



#### F. Firewall



### III. Manipulations d'autres outils/protocoles côté client

#### A. DHCP

***1. Afficher l'adresse IP du serveur DHCP du réseau WiFi***

    ipconfig getpacket en0 | grep server_identifier
> Cette commande nous permet de récupérer l'adresse du serveur DHCP.

![Screen7](/TP2/Screen7.png)

Ici l'adresse est `10.33.79.254`.

***2. Trouver la date d'expiration de votre bail DHCP***



***3. Demandez une nouvelle adresse IP (en ligne de commande)***

    ipconfig getifaddr en0
    sudo ipconfig set en0 DHCP
    ipconfig getifaddr en0
> On commence par récupérer l'IP actuelle, puis on force le renouvellement du bail DHCP. Enfin on vérifie que l'IP a changé.

![Screen9](/TP2/Screen9.png)

#### B. DNS

***1. Trouver l'adresse IP du serveur DNS que connaît votre ordinateur***

    scutil --dns | grep nameserver
> On affiche tous les paramètres DNS et on ne garde que les noms (IPs) du/des serveur(s) DNS connu(s) par l'ordinateur.

![Screen10](/TP2/Screen10.png)

On voit ici que l'ordinateur connaît deux serveurs DNS : `1.1.1.1` qui correspond à Cloudflare, et `8.8.8.8` qui correspond évidemment à Google.

***2. À l'aide de `dig`(sur macOS), faites un "lookup" pour `google.com`, `ynov.com`, et interpréter les résultats de ces commandes***

    dig google.com
    dig ynov.com
> On utilise `dig` pour effectuer un *lookup* sur `google.com` et `ynov.com`.

![Screen11](/TP2/Screen11.png)

Sous `;; ANSWER SECTION:` on trouve une ou plusieurs IPs selon le site traité.

Les IPs indiquées sont celles qui répondent pour `google.com` ou `ynov.com` ; car pour rappel derrière l'adresse d'un site se cache en réalité une ou plusieurs IPs qui répondent pour le site en question.

***3. Toujours à l'aide du même outil, faites un "reverse lookup" pour `78.78.21.21`, `92.16.54.88` et interpréter***

    dig -x 78.78.21.21
    dig -x 92.16.54.88
> On utilise `dig` avec l'argument `-x` pour effectuer un *reverse lookup* sur `78.78.21.21` et `92.16.54.88`.

![Screen12](/TP2/Screen12.png)

Sous `;; ANSWER SECTION:` on trouve une ou plusieurs adresses selon l'IP traitée : à l'inverse du *lookup*, cette fois ci on a fourni des IPs pour retrouver à quelles adresses ils correspondent. 

### IV. Bonus : pour aller plus loin

***1. Ouvrir Wireshark pendant les requêtes DHCP et DNS***

> On réexécute `sudo ipconfig set en0 DHCP` pendant la capture.

![Screen13](/TP2/Screen13.png)

L'ordinateur a libéré le bail DHCP (*release*) puis en a demandé un nouveau (processus DORA).

> On réexécute `dig google.com`pendant la capture.

![Screen14](/TP2/Screen14.png)

On observe dans ce cas également une activité sur WireShark : la requête sortante et la réponse entrante...

***2. Se renseigner sur les différences entre WiFi et câble***



***3. Explorer l'interface d'administration de votre box (chez vous) avec tout ça en tête***



***4. Sinon, elle sert à quoi la MAC si on a des IP ? → Se renseigner sur ARP***



***5. Utiliser un switch (physique) et se connecter non pas à 2, mais à 3 ou 4 ou 5 et faire mumuse avec le réseau ainsi créé***


# B1 — Linux

## TP 1

***Déterminer l'adresse de réseau à partir d'une adresse IP et du masque associé***

    Adresse IP : 10.33.69.157
    Masque : 255.255.240.0 (/20)

→ Adresse de réseau : `10.33.64.0`

## TP 2

*Les commandes indiquées dans ce compte rendu, ainsi que leurs résultats, ont été exécutées depuis un Mac.*

### I. Exploration locale en solo

#### 1. Affichage d'informations sur la pile TCP/IP locale ####

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

#### 2. Modifications des informations

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




### III. Manipulations d'autres outils/protocoles côté client




### IV. Bonus


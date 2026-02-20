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

#### A. Modification d'adresse IP

![Screen17](/TP2/Screen17.png)
![Screen18](/TP2/Screen18.png)

> Les paramètres IP à la connexion des deux machines, avant modification.

***Modifiez l'IP des deux machines pour qu'elles soient dans le même réseau.***

![Screen19](/TP2/Screen19.png)
![Screen20](/TP2/Screen20.png)

> Les paramètres IP des deux ordinateurs après modification.

***Vérifiez à l'aide de commandes que vos changements ont pris effet.***

    ifconfig

![Screen21](/TP2/Screen21.png)

On voit que le changement d'IP de la première machine a bien été opéré : l'IP de la carte `en7` est maintenant `193.168.1.1` comme défini dans les Réglages Système.

![Screen22](/TP2/Screen22.png)

Ici aussi l'IP a bien changé : celle de `en0` (ici la carte Ethernet) est bien `193.168.1.2` comme paramétré ici aussi.

***Utilisez `ping` pour tester la connectivité entre les deux machines.***

![Screen23](/TP2/Screen23.png)
![Screen24](/TP2/Screen24.png)

On voit grâce à `ping` que la connectivité entre les deux machine est bien opérationnelle !

***Testez avec un `/20`, puis un `/24`, puis le plus petit réseau que vous trouvez.***

*Dans les étapes ci dessus on a déjà un `/24`, il ne nous reste plus qu'à faire le `/20`.*

![Screen25](/TP2/Screen25.png)
![Screen26](/TP2/Screen26.png)

...et les `ping` d'une machine à l'autre et vice-versa fonctionnent...

***Inventez un nouveau réseau ! Comme `172.16.18.0/24` par exemple !***

![Screen27](/TP2/Screen27.png)
![Screen28](/TP2/Screen28.png)

...et les `ping` fonctionnent encore et toujours...

#### B. Utilisation d'un des deux comme gateway

**Sur la machine "gateway"**

Sur macOS Ventura 13 et versions ultérieures, pour "router" la connexion Internet d'une interface vers une autre, on se rend dans les `Réglages Système > Général > Partage > Partage Internet`.

Comme illustré ci-dessous, le paramètre `Partager votre connexion depuis` correspond à la carte réseau ayant accès à Internet, depuis laquelle on route les paquets Internet : ici, on choisit donc `Wi-Fi`.

Ensuite, juste en dessous, on a la deuxième section qui nous intéresse : `Avec les appareils utilisant`, avec la liste des interfaces réseau vers lesquelles on peut router des paquets depuis notre carte `Wi-Fi`. Ici, la carte utilisée pour relier les deux machines est `USB 10/100/1000 LAN`, on coche donc celle-ci, et on active le Partage Internet en haut.

![Screen29](/TP2/Screen29.png)

**Sur la machine qui recevra les paquets depuis la carte Wi-Fi de l'autre ordinataur**

Dans les paramètres de la carte Ethernet (utilisée pour la connexion avec l'autre ordinataur), on veille à repasser en DHCP : étant donné que l'autre Mac se prend pour un serveur DHCP (Partage Internet), il peut y avoir des erreurs si on reste en IP manuelle...

**Plus qu'à tester la connexion depuis le Mac qui reçoit les paquets !**

    ping 8.8.8.8

On ping Google pour être sûr que tout est en ordre.

![Screen30](/TP2/Screen30.png)

Et ça fonctionne !

#### C. Petit chat privé ?

**Rappel des IPs :**

Serveur : `172.16.18.1` | Client : `172.16.18.2`

***Connecter les deux machines entre elles avec `nc`, et échanger quelques messages...***

**Sur le serveur :**

    nc -l 8888

On met `nc` en mode listen (`-l`) sur le port 8888 (inutile de mettre `-p` ici)...

**Sur le client :**

    nc 172.16.18.1 8888

On se connecte au serveur avec son IP et le port choisi...

![Screen31](/TP2/Screen31.png)
![Screen32](/TP2/Screen32.png)

Et ça fonctionne !

***Pour aller un peu plus loin, essayez de préciser sur quelle IP le serveur doit écouter, et ne pas répondre sur les autres interfaces réseau (ex. uniquement Ethernet...)***

**Sur le serveur :**

    nc -l 172.16.18.1 8888

*La commande ne change pas sur le client, et tout fonctionne comme prévu.*

*Et, après reconnexion des deux machines au même Wi-Fi, quand essaie de se connecter à l'IP de la carte Wi-Fi du serveur depuis le client, l'opération prend fin directement (on n'est même pas invité à taper un message...).*

***On peut aussi accepter uniquement les connexions internes à la machine en écoutant sur `127.0.0.1`.***

    nc -l 127.0.0.1 8888

![Screen33](/TP2/Screen33.png)
![Screen34](/TP2/Screen34.png)

#### D. Wireshark

***Utilisez le pour observer les trames qui circulent entre vos deux cartes Ethernet...***

***...pendant un `ping`***

![Screen35](/TP2/Screen35.png)

*On filtre le protocole ICMP, celui utilisé par `ping`...*

Ici, il y a eu 6 `ping` (3 dans un sens, 3 dans l'autre)... pour chacun on a une requête et une réponse : 1 paquet par requête et 1 par réponse...

***...pendant un `netcat`***

![Screen36](/TP2/Screen36.png)

Ici, la connexion a été établie (4 premiers paquets), puis 4 messages ont été échangés (2 dans un sens, 2 dans l'autre — là aussi 2 paquets par message), et enfin il y a eu fermeture de session par le client (4 derniers paquets)...

*On filtre le port 8888, que l'on utilise ici...*

***...pendant que le PC 1 sert du PC 2 comme gateway***

![Screen37](/TP2/Screen37.png)

Ici aussi on a effectué 3 `ping` vers `8.8.8.8` (toujours Google) depuis la machine qui reçoit la connexion depuis la gateway.

#### E. Firewall

***Activez votre firewall et configurez-le pour accepter le `ping`.***

Toujours sur macOS Ventura 13 et ultérieur, on peut trouver les paramètres du firewall dans `Réglages Système > Réseau > Coupe-feu`. On active le Coupe-feu, et on clique sur `Options...`.

Pour autoriser `ping`, on désactive simplement le "Mode furtif".

![Screen38](/TP2/Screen38.png)

***Autorisez `nc` sur un port spécifique (entre 1024 et 20000), et testez.***

Sur macOS, sans solution tierce, il n'est pas possible d'autoriser une application spécifique sur un port spécifique : le Coupe-feu intégré permet de gérer des applications mais pas les ports, et `pf` ne gère que les ports. Ainsi, dans un cas soit l'appli est autorisée/bloquée pour tous les ports, soit vice-versa, soit les deux.

Pour cet exemple nous allons bloquer tous les ports sauf 8888, et toutes les applications sauf `nc`.

On commence par bloquer les applications sauf `nc` :

![Screen39](/TP2/Screen39.png)

Et on n'autorise que le port 8888 en éditant la config de `pf` :

    sudo nano /etc/pf.conf

...on ajoute le texte suivant au fichier :

    block in all
    pass in proto tcp to any port 8888 keep state

![Screen40](/TP2/Screen40.png)

...et on recharge et active la config :

    sudo pfctl -f /etc/pf.conf
    sudo pfctl -e

![Screen41](/TP2/Screen41.png)
![Screen42](/TP2/Screen42.png)

Comme on peut le voir, Netcat ne fonctionne plus qu'avec le port 8888 que nous avons autorisé, mais plus avec les autres...

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

> On réexécute `dig google.com` pendant la capture.

![Screen14](/TP2/Screen14.png)

On observe dans ce cas également une activité sur WireShark : la requête sortante et la réponse entrante...

***2. Se renseigner sur les différences entre WiFi et câble***

|               |            Câble            |            Wi-Fi           |
| ------------- | --------------------------- | -------------------------- |
| Débit         | Élevé, constant (→ 10 Gbps) | Bon, plus variable         | 
| Latence       | Faible (quelques ms)        | Plus élevée, très variable |
| Fiabilité     | Très fiable                 | ⚠ Obstacles, interférences |
| Sécurité      | Physiquement limité (câble) | Signal captable, variable  |

***3. Explorer l'interface d'administration de votre box (chez vous) avec tout ça en tête***

![Screen15](/TP2/Screen15.png)

On observe ici les paramètres DHCP de la "box" : on a la possibilité de modifier l'IP de celle-ci, le netmask, la plage d'IPs à attribuer aux appareils connectés au réseau... et même d'attribuer des baux DHCP statiques (pour garder toujours la même IP pour une même adresse MAC...) !

![Screen16](/TP2/Screen16.png)

Un autre exemple encore plus courant de paramètres que l'on peut explorer/modifier dans cette interface est la carte des équipement : voir tous les équipements connectés, leur IP, leur MAC... ; on peut également les identifier avec un nom et une icône pour les reconnaître plus facilement !

***4. Sinon, elle sert à quoi la MAC si on a des IP ? → Se renseigner sur ARP***

> La MAC ou Media Access Control (couche 2 "Liaison de données" du modèle OSI), est l'identifiant physique propre à la carte réseau.

> L'IP ou Internet Protocol (couche 3 "Réseau" du modèle OSI), permet d'identifier la carte réseau, sur le réseau, afin de router le trafic / les paquets.

> ARP (Address Resolution Protocol) permet de faire le lien entre une adresse IP et une MAC : il associe dynamiquement une IP à sa MAC correspondante sur un réseau local.

***5. Utiliser un switch (physique) et se connecter non pas à 2, mais à 3 ou 4 ou 5 et faire mumuse avec le réseau ainsi créé***

Ainsi, on peut :

* configurer des IP statiques dans le même sous-réseau sur chaque machine pour tester les pings, observer les tables ARP, capturer le trafic Wireshark... ;

* installer un serveur DHCP sur une machine pour attribuer dynamiquement les IP aux autres ;

* configurer un partage réseau ou un serveur HTTP/SSH accessible depuis les autres machines connectées

* etc... les possibilités sont infinies...

## TP 3

### I. Exploration en solo

*(Pour cette partie, tous les fichiers nécessitant à la création un mot de passe ont été chiffrés avec le mdp "a"...)*

#### A. Base64

> On va d'abord créer un fichier file_bin contenant 50 kilooctets de données binaires aléatoires.

> Puis encoder le fichier en base64 et vérifier que le fichier généré est bien un texte « pur » formaté selon le modèle particulier de base64.

***Comparer la taille des fichiers, que constatez-vous ?***

    frederik@macbookair-1 TP3 % ls -l file_bin*
    -rw-r--r--  1 frederik  staff  51200 15 févr. 11:10 file_bin
    -rw-r--r--  1 frederik  staff  69335 15 févr. 11:11 file_bin_b64

On constate que le fichier est plus gros. `file_bin` fait 51200 B alors que `file_bin_b64` fait 69335 B.

> On décode le fichier base64 pour produire un nouveau fichier file_bin2.

***Les fichiers binaires sont-ils identiques ?***

    frederik@macbookair-1 TP3 % diff -s file_bin file_bin2
    Files file_bin and file_bin2 are identical

Oui, ils sont identiques. L'encodage-décodage n'altère donc pas le contenu du fichier.

#### B. AES (Chiffrement symétrique)

> On va générer un autre fichier texte message aléatoire qui va créer un fichier texte contenant tous les mots du dictionnaire ayant ker comme sous-chaîne (vous pouvez bien entendu utiliser un autre filtre que ker).

> Ensuite on chiffre le message en aes256, et on procède au déchiffrement.

> Le fichier clair (message) est un fichier texte, le cryptogramme obtenu (message_c) est un fichier binaire, ce qui peut poser des problèmes dans certains contextes. On peut essayer de voir ce que ca donne...

#### C. RSA (Chiffrement asymétrique)

> Générez une paire de clés RSA de 2048 bits (en personnalisant le nom selon votre identifiant)...

> Regardons le contenu du fichier de la clé (qui est au format pem : privacy enhanced mail, donc encodée en base64).

> On va ensuite regarder un peu plus en détail les paramètres de la clé, puis protéger notre paire de clés RSA avec un chiffrement AES.

    openssl enc -e -salt -in cle_fredo.pem -out cle_fredo_protected.pem -aes256 -pbkdf2 -md sha256

> On va maintenant exporter la clé publique et visualiser ses paramètres.

***Que pouvons nous voir dans ces paramètres ?***

    frederik@macbookair-1 TP3 % openssl rsa -in clepublique_fredo.pem -pubin -text -noout 
    RSA Public-Key: (2048 bit)
    Modulus:
        00:d8:27:3d:b2:59:65:f8:af:ef:42:54:bd:b8:86:
        [...]
        f6:47:6e:f9:9b:d9:09:0c:23:0a:06:c7:2a:25:13:
        fb:15
    Exponent: 65537 (0x10001)

On retrouve le modulo et l'exposant public : on remarque que ce sont les mêmes que notre clé privée.

> On va maintenant créer une passphrase de votre choix avec vim ou nano, et chiffrer le fichier avec la clé publique.

*Sur macOS (LibreSSL), la commande  `pkeyutl`  plante avec l’erreur "Expecting: ANY PRIVATE KEY", il a donc fallu remplacer `pkeyutl` par `rsautl` (qui utilise le même chiffrement asymétrique) pour que ça fonctionne.*

> Enfin on procède au déchiffrement du fichier (avec la clé privée si vous ne l'aviez pas compris...)

### II. Sans que je vous file les réponses à chaques étapes

*(Là aussi, lorsque nécessaire on utilisera le mot de passe "a"...)*

#### A. Base64

***Créer un fichier `data.bin` contenant 100 Ko de données binaires aléatoires, et vérifier sa taille.***

    dd if=/dev/urandom of=data.bin bs=1k count=100
    ls -l data.bin

![Screen1](/TP3/Screen1.png)

***Encoder le fichier en Base64 dans un fichier `data.b64`, et afficher son contenu.***

    openssl base64 -e -in data.bin -out data.b64
    cata data.b64

![Screen2](/TP3/Screen2.png)

***Comparer la taille de `data.bin` et `data.b64`.***

    ls -l data*

![Screen3](/TP3/Screen3.png)

***Décoder le fichier `data.b64` afin dʼobtenir un fichier `data_restored.bin`, et vérifier que `data.bin` et `data_restored.bin` sont strictement identiques.***

    openssl base64 -d -in data.b64 -out data_restored.bin
    diff -s data.bin data_restored.binbin

![Screen4](/TP3/Screen4.png)

***Question 1 : Base64 est-il un chiffrement ? Pourquoi ?***

Non, c'est un encodage. Il est réversible sans clé, n'apporte aucune confidentialité et n'est donc pas un chiffrement.

***Question 2 : Pourquoi la taille du fichier change-t-elle après encodage ?***

L’encodage Base64 prend des blocs de 3 octets (24 bits) et les représente par 4 caractères ASCII de 6 bits chacun : on passe de 3 octets à 4 caractères, la taille du fichier augmente donc mécaniquement.

***Question 3 : Quel est approximativement le pourcentage dʼaugmentation ?***

Chaque 3 octets deviennent 4 octets de texte (x1,33), on obtient donc une augmentation d’environ 33% de la taille.

***Question 4 : Quelle méthode permet de vérifier rigoureusement que deux fichiers sont identiques ?***

On calcule le hash de chaque fichier avec une fonction comme SHA‑256, puis on compare les valeurs obtenues. Si les deux hash sont exactement identiques, on considère rigoureusement que les fichiers le sont aussi.

#### B. Chiffrement symétrique – AES

***Créer un fichier `confidentiel.txt` contenant votre nom, la date, et 5 lignes minimum.***

    nano confidentiel.txt

***Chiffrer le fichier avec AES 256, un sel, une dérivation de clé robuste, et un algorithme de hachage sécurisé. Le fichier chiffré devra sʼappeler `confidentiel.enc`***

    openssl enc -e -salt -in confidentiel.txt -out confidentiel.enc -aes256 -pbkdf2 -md sha256

`-aes256` → chiffrement en AES 256

`-salt` → sel aléatoire

`-pbkdf2` → dérivation de clé robuste PBKDF2

`-md sha256` → algorithme de hachage sécurisé (SHA‑256) pour la dérivation

***Vérifier que le fichier obtenu est bien binaire.***

    cat confidentiel.enc

![Screen5](/TP3/Screen5.png)

***Déchiffrer le fichier vers `confidentiel_dechiffre.txt` et vérifier que le contenu correspond exactement à lʼoriginal.***

    openssl enc -d -in confidentiel.enc -out confidentiel_dechiffre.txt -aes256 -pbkdf2 -md sha256
    diff -s confidentiel.txt confidentiel_dechiffre.txt

![Screen6](/TP3/Screen6.png)

***Chiffrer une seconde fois le même fichier avec le même mot de passe, et comparer les deux fichiers chiffrés.***

    openssl enc -e -salt -in confidentiel.txt -out confidentiel_bis.enc -aes256 -pbkdf2 -md sha256
    diff -s confidentiel.enc confidentiel_bis.enc

![Screen7](/TP3/Screen7.png)

***Question 1 : Pourquoi les deux fichiers chiffrés sont-ils différents ?***

On a ajouté un sel aléatoire au chiffrement, ce qui a dérivé la clé de chiffrement, le fichier a donc en quelque sorte été chiffré différemment, raison pour laquelle il est différent.

***Question 2 : Quel est le rôle du sel ?***

Aléatoire et ajouté au mot de passe, il empêche les attaques par tables rainbow et garantit que deux chiffrages identiques avec le même mot de passe donnent des résultats différents. Si dans notre cas on avait retiré l'option sel, les deux fichiers chiffrés à la fin auraient été identiques.

***Question 3 : Que se passe-t-il si une option change lors du déchiffrement ?***

Une manquante ou différente empêcherait la dérivation correcte de la clé AES, produisant une erreur "bad decrypt" ou un fichier corrompu, illisible.

***Question 4 : Pourquoi utilise-t-on PBKDF2 ?***

Car c'est une fonction lente et itérative qui résiste aux attaques brute force.

***Question 5 : Quelle est la différence entre encodage et chiffrement ?***

L'**encodage** (ex. Base64) transforme des données selon une règle publique et réversible, sans clé ni secret (pas de sécurité) ; tandis que le **chiffrement** (ex. AES/RSA) utilise une clé secrète pour rendre les données illisibles à quiconque sans la clé (sécurisé).

#### C. Cryptographie asymétrique – RSA

***Générer une paire de clés RSA 2048 bits (`rsa_private.pem`, `rsa_public.pem`), et protéger la clé privée par un chiffrement.***

    openssl genrsa -out private_key.pem 2048
    openssl rsa -in private_key.pem -pubout -out public_key.pem
    openssl enc -e -salt -in private_key.pem -out private_key_protected.pem -aes256 -pbkdf2 -md sha256

![Screen8](/TP3/Screen8.png)

***Afficher les paramètres des deux clés et comparer.***

    openssl rsa -in private_key.pem -text -noout
    openssl rsa -in public_key.pem -pubin -text -noout

![Screen9](/TP3/Screen9.png)
![Screen10](/TP3/Screen10.png)

On observe, une fois de plus, que les données communes à la clé privée et la clé publique (modulo et exposant) correspondent entre elles.

***Créer un fichier `secret.txt` et chiffrer ce fichier avec la clé publique (→ `secret.enc`).***

    nano secret.txt
    openssl rsautl -encrypt -in secret.txt -inkey public_key.pem -pubin -out secret.enc

***Déchiffrer avec le clé privée.***

    openssl rsautl -decrypt -in secret.enc -inkey private_key.pem

![Screen11](/TP3/Screen11.png)

***Question 1 : Pourquoi la clé privée ne doit-elle jamais être partagée ?***

Car cette clé privée est l'élément de déchiffrement des messages/fichiers chiffrés par la clé publique correspondant. Si la clé privée est compromise ou connue de quelqu'un d'autre, ce quelqu'un peut déchiffrer les communications adressées au destinataire original ; à l'inverse de la clé publique qui ne sert qu'à chiffrer les données et qui peut donc être diffusée librement.

***Question 2 : Pourquoi RSA nʼest-il pas adapté au chiffrement de gros fichiers ?***

RSA ne convient pas au chiffrement de gros fichiers car le temps de chiffrement/déchiffrement augmente de manière exponentielle au fur et à mesure que la taille du fichier augmente. Le chiffrement de gros fichiers serait également très gourmand en ressources CPU.

***Question 3 : Quelles différences observe-t-on entre les paramètres dʼune clé publique et dʼune clé privée ?***

La principale différence est que là où la clé publique est courte et partagée librement, la clé privée est longue est ultra-secrète : cette dernière inclut un grand nombre secret calculé pour annuler l'exposant de la clé publique, et derrière le chiffrement en lui-même.

***Question 4 : Quel est le rôle du modulo dans RSA ?***

Le modulo agit comme une "boîte fermée" qui limite tous les calculs à un espace fixe défini par le produit de deux grands nombres premiers. Cela permet le chiffrement/déchiffrement avec nos clés, sans que les nombres deviennent ingérables ou fuient hors de cet espace sécurisé. C'est ce qui rend l'opération rapide et sûre en pratique.

***Question 5 : Pourquoi utilise-t-on souvent RSA pour chiffrer une clé AES plutôt quʼun document entier ?***

On utilise cette méthode de chiffrement hybride car là où RSA est très lent, bien qu'utilisant deux clés séparées (chiffrement asymétrique), AES est plus performant (surtout pour les gros fichiers) mais n'utilise qu'une seule clé (chiffrement symétrique). Il s'agit donc de chiffrer le document en AES dans un premier temps pour la rapidité, puis la clé AES en RSA pour la sécurité (puisque le chiffrement asymétrique est moins facilement compromissible) ; comme des poupées russes...

#### D. Signature numérique

***Créer un fichier `contrat.txt` et générer son empreinte (hash).***

    nano contrat.txt
    openssl dgst -sha256 fichier.txt

![Screen12](/TP3/Screen12.png)

***Signer le fichier avec votre clé privée (→ `contrat.sig`), et vérifier la signature avec la clé publique.***

    openssl dgst -sha256 -sign private_key.pem -out contrat.sig contrat.txt
    openssl dgst -sha256 -verify public_key.pem -signature contrat.sig contrat.txt

***Modifier légèrement le fichier `contrat.txt` et refaire la vérification.***

    nano contrat.txt
    openssl dgst -sha256 -verify public_key.pem -signature contrat.sig contrat.txt

![Screen13](/TP3/Screen13.png)

***Question 1 : Que se passe-t-il après modification du fichier ? Pourquoi ?***

Jusqu'à la modification du fichier depuis la signature, on a bien "Verified OK", ce qui signifie que la signature est valide. Le hash du fichier correspond à celui de la clé privée utilisée pour la signature. En d'autres termes, c'est la confirmation que personne n’a modifié le fichier depuis la signature.

Or, après la modification du fichier, quand on vérifie à nouveau la validité de la signature, celle-ci échoue : cela signifie que le hash du fichier a changé depuis la signature et qu'il ne correspond plus à la clé privée. Quelqu'un a modifié le fichier après la signature !

***Question 2 : Quel est le rôle du hachage dans le mécanisme de signature ?***

Le hachage permet de prouver l'auteur d'un fichier, et que celui-ci n'a pas été modifié depuis sa signature : le hash du fichier est chiffré par la clé privée. Ainsi, seule la clé publique correspondant reconnaîtra ce hash (certifiant de ce fait l'auteur), et ce uniquement si le hash n'a pas été réécrit lors d'une modification...

***Question 3 : Quelle différence entre signature numérique et chiffrement ?***

La signature numérique assure l'authenticité et l'intégrité d'un fichier en chiffrant une empreinte avec la clé privée du signataire pour vérification publique via sa clé publique. Cela certifie que le détenteur de la clé est l'auteur du fichier (sauf si clé compromise), et que le fichier n'a pas été modifié depuis la signature.

À l'inverse, le chiffrement protège la confidentialité en rendant le contenu lisible par le destinataire uniquement, via une clé publique pour chiffrer et une clé privée pour déchiffrer, sans impliquer d'empreinte ni de preuve d'origine.

### Bonus : Mettre en place un chiffrement hybride complet, en expliquant chaque étape

***1. Générer une clé AES aléatoire***

On commence par créer un fichier `aes_key.bin` contenant 32 octets aléatoires en binaire. Cette clé nous servira à chiffrer un fichier volumineux en AES.

    openssl rand -out aes_key.bin 32
N.B. : L'option `rand` ici est basée sur `/dev/urandom`...

`-out aes_key.bin` → on spécifie l'emplacement d'enregistrement de la clé ;

`32` → et on indique la longueur de la clé (32 bytes pour AES-256)...

> On a maintenant notre clé AES aléatoire.

***2. Chiffrer un fichier volumineux avec AES***

*Le fichier en question sera une archive zip de 17,7 Mo. Ni celui-ci ni sa version chiffrée ne seront inclus dans le repository...*

    openssl enc -e -salt -in b1_linux-main.zip -out b1_linux-main.enc -aes-256-cbc -pass file:aes_key.bin -pbkdf2 -md sha256

`enc` → on spécifie le mode chiffrement/déchiffrement ;

`-e` → on re-spécifie le mode chiffrement ;

`-salt` → on ajoute un sel aléatoire ;

`-in b1_linux-main.zip` → on spécifie le fichier source ;

`-out b1_linux-main.enc` → ... et le fichier destination (chiffré) ;

`-aes-25-cbc` → on spécifie qu'on chiffre en AES-256 en mode CBC (chaînage par blocs, adapté au gros fichiers) ;

`-pass file:aes_key.bin` → on lui donne la clé directement depuis le fichier binaire qu'on a créé ;

`-pbkdf2` → on spécifie la dérivation PBKDF2 ;

`-md sha256` → et le hachage SHA-256...

> On a maintenant un fichier `b1_linux-main.enc`, *instantanément* chiffré en AES-256 depuis `l1_linux-main.zip` (chiffrement symétrique, soit une seule clé), avec notre clé AES `aes_key.bin`.

***3. Chiffrer la clé AES avec RSA***

On commence par générer une paire de clés RSA 2048 bits :

    openssl genrsa -out private_key.pem 2048

`genrsa` → on génère une clé privée (et sa clé publique intégrée) ;

`-out private_key.pem` → on spécifie le nom de la clé privée ;

`2048` → et on spécifie la taille de la clé (en bits)...

> On a maintenant notre clé privée.

    openssl rsa -in private_key.pem -pubout -out public_key.pem

`rsa` → on utilise l'outil de manipulation de clés RSA ; 

`-in private_key.pem` → on spécifie à partir de quelle clé privée on exporte la clé publique ;

`-pubout` → on indique qu'on extrait uniquement la clé publique ;

`-out public_key.pem` → et enfin on indique l'emplacement de sortie de la clé publique...

> On a maintenant notre clé publique.

On va à présent chiffrer notre unique clé AES avec notre clé publique RSA.

    openssl rsautl -encrypt -in aes_key.bin -inkey public_key.pem -pubin -out aes_key.enc

`rsautl` → on utilise l'outil de chiffrement/déchiffrement (entre autres) RSA ;

`-encrypt` → on indique que l'on va chiffrer des données ;

`-in aes_key.bin` → on spécifie le fichier source (à chiffrer) ; 

`-inkey public_key.pem` → on indique la clé publique avec laquelle on réalise le chiffrement ;

`-pubin` → on indique aussi que l'on vient de spécifier une clé publique ;

`-out aes_key.enc` → et on spécifie la destination du fichier chiffré...

> On a enfin chiffré notre clé AES avec RSA.

![Screen14](/TP3/Screen14.png)

**Notre fichier `b1_linux-main.zip` est maintenant protégé par chiffrement dit "hybride" : on a à la fois l'efficacité du chiffrement AES et le chiffrement asymétrique du RSA, moins compromissible du fait de l'utilisation de deux clés séparées.**

**Pour accéder à notre fichier original, il nous faudra à présent utiliser dans un premier temps la clé privée RSA pour accéder à notre clé AES, qui nous permettra elle-même de déchiffrer le fichier `b1_linux-main.enc` par la suite. Pour cela on utilisera les deux commandes suivantes :**

    openssl rsautl -decrypt -in aes_key.enc -inkey private_key.pem -out aes_key_decrypted.bin
    openssl enc -d -in b1_linux-main.enc -out b1_linux-main_decrypted.zip -aes-256-cbc -pass file:aes_key.bin -pbkdf2 -md sha256

**On peut aussi procéder à la vérification pour être sûr que les données des originaux n'ont pas été altérées :**

    diff -s aes_key.bin aes_key_decrypted.bin
    diff -s b1_linux-main.zip b1_linux-main_decrypted.zip

![Screen15](/TP3/Screen15.png)

## TP 4

### I. Mise en place de l’environnement virtualisé

***Installez VirtualBox et créez une VM Ubuntu. Vérifiez que la VM a une IP accessible depuis la machine hôte.***

*On a installé Ubuntu dans une machine virtuelle.*

    ip a

Sur la VM, on affiche les informations des différentes cartes réseau de la machine virtuelle.

![Screen1](/TP4/Screen1.png)

La carte `enp0s6` correpsond à un réseau privé hôte, donc accessible de puis l'hôte : son IP est `10.37.129.10`.

### II. Serveur SSH

***Installez le serveur SSH sur la VM.***

    sudo apt install openssh-server

Sur la VM aussi utilise le gestionnaire de paquets `apt` (pour Ubuntu) pour installer le serveur OpenSSH...

***Vérifiez que le service SSH fonctionne et écoute sur un port.***

    systemctl status ssh

![Screen2](/TP4/Screen2.png)

On observe dans les premières lignes que le service SSH est actif...
Plus bas dans les logs on remarque aussi qu'il écoute sur le port 22.

***Connectez-vous depuis la machine hôte.***

    ssh parallels@10.37.129.10

![Screen3](/TP4/Screen3.png)

***Générez une clé SSH sur la machine cliente et copiez-la sur le serveur pour tester la connexion sans mot de passe.***

    ssh-keygen
    ssh-copy-id parallels@10.37.129.10

Sur le client (ici l'hôte de la VM), on commence par générer une clé avec `ssh-keygen` et on la copie via SSH avec `ssh-copy-id`.

![Screen4](/TP4/Screen4.png)

### III. Sécurisation SSH

***Interdisez l’accès root.***

    sudo nano /etc/ssh/sshd_config

Dans le fichier `/etc/ssh/sshd_config` de la VM, on ajoute/décommente la ligne `PermitRootLogin no`.

***Désactivez l’authentification par mot de passe.***

Toujours dans le fichier `sshd_config`, on fait pareil avec les lignes `PasswordAuthentication no` et `PubkeyAuthentication yes`.

***Changez le port par défaut (22) pour réduire les tentatives de brute-force.***

Enfin, toujours dans le même fichier, on fait pareil avec la ligne `Port 2222`. (On se mettra sur le port 2222.)

![Screen5](/TP4/Screen5.png)

On ferme le fichier de config SSH, et on met à jour les règles du firewall pour ouvrir le port 2222 :

    sudo ufw allow 2222/tcp

On reboot la VM.

***Testez la connexion avec le nouveau port depuis la machine cliente.***

    ssh -p 2222 parallels@10.37.129.10

On rajoute l'argument `-p 2222` à notre commande de connexion SSH pour spécifier un port (différent du port 22 par défaut) à utiliser pour la connexion.

![Screen6](/TP4/Screen6.png)

***Créez un alias SSH dans ~/.ssh/config pour simplifier les connexions.***

    nano ~/.ssh/config

On édite le fichier de config SSH client sur la machine client.

![Screen7](/TP4/Screen7.png)

`Host` → Nom de l'alias

`HostName` → IP du serveur (VM)

`Port` → Le port SSH utilisé par le serveur

`IdentityFile` → Clé privée du client

`IdentitiesOnly` → Si oui ou non on n'utilise que des clés

![Screen8](/TP4/Screen8.png)

Et après test la connexion avec alias fonctionne maintenant !

### IV. Transfert de fichiers

***Transférez un fichier et un dossier depuis la machine cliente vers le serveur, en utilisant SCP.***

On va copier l'image `Screen1.png` de ce repo vers le bureau de la VM.

    scp -P 2222 ~/repogit/b1_linux/TP4/Screen1.png parallels@10.37.129.10:/home/parallels/Desktop

*(On fait bien attention à préciser le port !!)*

![Screen9](/TP4/Screen9.png)

Après vérification on a la confirmation que la copie a été effectuée...

Copions maintenant le dossier `TP4` en entier au même emplacement...

    scp -r -P 2222 ~/repogit/b1_linux/TP4/ parallels@10.37.129.10:/home/parallels/Desktop

*(On rajoute `-r` pour le mode récursif, comme on a un dossier...)*

![Screen10](/TP4/Screen10.png)

***Explorez les commandes `put`, `get`, `ls` pour transférer et naviguer sur le serveur avec SFTP.***

On va l'utiliser pour se connecter à la VM et y transférer `Screen2.png` pareil que tout à l'heure.

    sftp TP4

Commandes utilisées :

`cd` → se déplacer dans la VM (utiliser `lcd` pour faire la même chose dans le client)

`ls` → afficher les fichiers du dossier actuel dans la VM (équivalent client : `lls`)

`put [nom_fichier]` → mettre le fichier spécifié du dossier actuel du client vers le dossier actuel du serveur (la VM)

![Screen11](/TP4/Screen11.png)

*On utiliserait `get` de la même manière pour transférer dans l'autre sens : télécharger un fichier depuis la VM vers notre client...*

***Avec Rsync, synchronisez un dossier entre client et serveur.***

    rsync -avz --port=2222 ~/repogit/b1_linux/TP4 TP4:/home/parallels/Documents

`-avz` → `a`rchive (copie conforme), `v`erbose (retour CLI), `z` (compression, pour aller plus vite)

![Screen12](/TP4/Screen12.png)

On a bien le retour verbose : tous les fichiers se sont bien transférer, on peut même vérifier...

On réexécute la commande car entre-temps on a rajouté `Screen12.png` !

![Screen13](/TP4/Screen13.png)

On voit que cette fois-ci il n'a été copié que le nouveau fichier, `Screen12.png`, comme prévu !

### V. Analyse des logs et sécurité

***Suivez les logs dʼauthentification pour observer les connexions SSH.***

    sudo tail -f /var/log/auth.log

![Screen14](/TP4/Screen14.png)

On observe ici, par exemple, que la connexion SSH a réussi à 9:22:35 (`Accepted publickey`), et qu'il y a eu connexion en tant que `sudo` (au moment d'afficher le log)...

***Installez Fail2Ban et testez un bannissement après plusieurs tentatives échouées.***

    sudo apt install fail2ban

On utilise `apt` pour installer Fail2Ban sur le système.

    sudo fail2ban-client status sshd

On affiche le statut des bannissements avant d'essayer de forcer la connexion...

Pour le test on a ré-activé l'authentification SSH par mot de passe.
On essaie alors des mots de passe incorrects pour déclencher Fail2ban.

Au bout d'un moment cela ne fonctionne plus, on se rend compte dans Ubuntu que l'IP du client est bannie...

![Screen15](/TP4/Screen15.png)
![Screen16](/TP4/Screen16.png)

### VI. Tunnel SSH

***Créez un tunnel local pour accéder à un service web distant depuis la machine cliente.***

    ssh -L 8080:localhost:80 TP4

*(On rajoute `-L 8080:localhost:80` pour relier les ports 8080 du client et localhost:80 de notre VM (que le client puissa eccéder au port 80 de la VM).)*

![Screen17](/TP4/Screen17.png)

La connexion SSH a réussi : le tunnel est ouvert en arrière-plan...

***Créez un tunnel distant pour permettre lʼaccès SSH au client via le serveur.***

    ssh -R 2223:localhost:22 TP4

*(Ici, à l'inverse, grâce à `-R`, on relie le port 2223 de la VM au localhost:22 de notre client, pour que la VM puisse accéder au port 22 du client.)*

![Screen18](/TP4/Screen18.png)

Une fois de plus la connexion SSH a réussi, le tunnel est ouvert.

### VII. Nginx et HTTPS

***Installez Nginx sur la VM.***

    sudo apt install nginx

On utilise `apt` pour installer Nginx...

***Créez un site test dans `/var/www/site-tp` et un fichier `index.html` avec un message de bienvenue. Configurez Nginx pour servir ce site sur HTTP.***

On commence par créer le dossier du site test puis la page HTML...

    sudo mkdir -p /var/www/site-tp
    sudo nano /var/www/site-tp/index.html

On crée ensuite un fichier de configuration, en l'éditant comme illustré :

    sudo nano /etc/nginx/sites-available/site-tp

![Screen19](/TP4/Screen19.png)

`listen` → on spécifie le port sur lequel écouter ;

`server_name` → définit à quoi répond ce bloc (la wildcard `_` correspond à *tout*) ;

`root` → on indique l'emplacement du site ;

`index` → on indique aussi quel fichier est l'index (la page principale) ;

`location` → définit comment Nginx doit réagir quand on accède à la racine du site (vérifie si le site existe)...

On n'oublie pas d'activer le site `site-tp` en créant un lien symbolique de notre config dans le dossier `/etc/nginx/sites-enabled/` (on supprime aussi la config par défaut) ; et de redémarrer Nginx.

    sudo ln -s /etc/nginx/sites-available/site-tp /etc/nginx/sites-enabled/
    sudo rm /etc/nginx/sites-enabled/default
    sudo systemctl restart nginx

Et on vérifie si ça fonctionne depuis le client :

    curl -k http://10.37.129.10

*(L'option `-k` de `curl` permet d'ignorer la vérification du certificat SSL : ce n'est pas utile ici, mais ça le sera juste après car le certificat SSL que l'on va créer est auto-signé...)*

![Screen20](/TP4/Screen20.png)

***Générez un certificat auto-signé pour HTTPS et configurez la redirection HTTP → HTTPS.***

    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt

On utilise ici `openssl` pour générer une clé privée et un certificat pour HTTPS :

`req` → on utilise le module de gestion des demandes de signature de certificat ;

`-x509` → transforme la demande en un certificat auto-signé ;

`-nodes` → ne pas chiffrer la clé privée avec un mot de passe ;

`-days 365` → on spécifie une durée de validité d'1 an pour le certificat ;

`-newkey rsa:2048` → on fait créer en même temps la clé privée RSA 2048 bits correspondante ;

`-keyout` → on indique l'emplacement de création de la clé privée ;

`-out` → enfin on spécifie l'emplacement de création du certificat...

Maintenant, on va mettre à jour la configuration de notre site sur Nginx pour automatiquement rediriger les requêtes HTTP vers HTTPS...

    sudo nano /etc/nginx/sites-available/site-tp

... on la met alors à jour comme illustré :

![Screen21](/TP4/Screen21.png)

- Sur le bloc HTTP, on a remplacé les chemins de site (maintenant traités par HTTPS) par justement la redirection vers HTTPS (`return`) ;

- On a également rajouté le bloc HTTPS sous le bloc HTTP, avec le port 443 (HTTPS), les chemins du site auparavant sur le bloc HTTP, et en rajoutant les chemins vers les certificats générés (`ssl_certificate` et `ssl_certificate_key`)...

Enfin on redémarre Nginx pour appliquer les changements.

    sudo systemctl restart nginx

***Testez le site depuis le client.***

    curl -kL http://10.37.129.10
    curl -k https://10.37.129.10

![Screen22](/TP4/Screen22.png)

On voit que les requêtes HTTP et HTTPS fonctionnent bien, ce qui veut dire que :
- les requêtes HTTP sont bien redirigées vers HTTPS (avec argument `L` qui permet la redirection) ;
- les requêtes HTTPS de base fonctionnent bien !

### VIII. Firewall et permissions

***Autorisez Nginx dans le firewall (ports HTTP/HTTPS).***

    sudo ufw allow 'Nginx Full'
    sudo ufw enable
    sudo ufw status

Ici, on a ajouté la règle "Nginx Full" à notre pare-feu (pour ouvrir les deux ports, HTTP et HTTPS, d'un coup) ; on l'a ensuite activé et on a checké son statut pour être sûr que tout est en ordre...

![Screen23](/TP4/Screen23.png)

*(Par précaution on a re-testé `curl` depuis le client et ça fonctionne toujours...)*

***Vérifiez les permissions sur `/var/www/site-tp` pour que Nginx puisse lire les fichiers.***

    sudo chown -R www-data:www-data /var/www/site-tp

On commence par utiliser `chown` pour définir le propriétaire de notre dossier site (+ son contenu → mode récursif `-R`) sur `www-data` (user qui exécute le serveur Nginx), ainsi que le groupe du même nom (→ `:www-data`)...

    sudo chmod -R 775 /var/www/site-tp

Et pour finir avec les permissions, on utilise `chmod` (toujours en mode récursif sur le dossier entier) pour définir celle du propriétaire (Xxx) et du groupe (xXx) sur 7 (lecture/écriture/exécution), et celle des autres (xxX) sur 5 (lecture/exécution).

    ls -lR /var/www

Enfin on peut utiliser `ls -lR` pour lister le contenu de `www` ainsi que ses permissions. Le but étant de s'assurer que les permissions de `site-tp` et `index.html` sont correctes (c'est le cas)...

![Screen24](/TP4/Screen24.png)

(On n'a laissé que `site-tp` dans le dossier pour un output propre.)

### IX. Validation finale

***SSH fonctionnel sur port personnalisé et authentification par clé uniquement.***

![Screen5](/TP4/Screen5.png)

- Port : `Port 2222`
- Authentification par clé : `PubkeyAuthentication yes` `PasswordAuthentication no`

✔ SSH est bien configuré de manière à n'accepter que les clés, sur le port 2222.

***Fail2Ban actif et opérationnel.***

![Screen15](/TP4/Screen15.png)
![Screen16](/TP4/Screen16.png)

✔ Fail2Ban est bien opérationnel : *quelques* tentatives infructueuses de connexion par mot de passe et l'IP du client se retrouve bannie.

***Transferts de fichiers fonctionnels (SCP, SFTP, RSYNC).***

**SCP**

![Screen9](/TP4/Screen9.png)

**SFTP**

![Screen11](/TP4/Screen11.png)

**RSYNC**

![Screen12](/TP4/Screen12.png)

✔ Les transferts de fichiers fonctionnent pour les 3 protocoles.

***Nginx accessible en HTTP et HTTPS avec redirection automatique HTTP → HTTPS.***

![Screen22](/TP4/Screen22.png)

✔ `curl` fonctionne en HTTP (redirection auto) et en HTTPS depuis le client.

***Certificat SSL auto-signé valide.***

    openssl x509 -checkend 0 -noout -in /etc/ssl/certs/nginx-selfsigned.crt

On utilise cette commande pour vérifier si le certificat a expiré ou non : 

`Certificate will not expire` : le certificat est encore valide

`Certificate will expire` : le certificat est expiré

![Screen25](/TP4/Screen25.png)

✔ Le certificat SSL auto-signé est encore valide.

***Firewall configuré et permissions correctes sur `/var/www/site-tp`.***

![Screen23](/TP4/Screen23.png)

✔ Firewall activé avec règles relatives à Nginx.

![Screen24](/TP4/Screen24.png)

✔ Permissions correctes pour `site-tp` et `index.html` (`rwxrwxr-x` = `775`).

## TP 5

### I. Prise en main et sécurisation

#### A. Accès à l'interface

***Question 1 : Quelle sont les adresses IP du LAN et du WAN ?***

L'adresse IP du LAN est `192.168.128.2`. C'est cette IP qu'on utilise pour accéder à l'interface web de pfSense.

On peut directement retrouver l'IP du WAN, (avec celle du LAN) sur le tableau de bord, sous la carte "Interfaces" : on a `192.168.64.2`.

![Screen1](/TP5/Screen1.png)

***Question 2 : Pourquoi utilise-t-on HTTPS ?***

Étant donné que pfSense est un pare-feu, il est essentiel de garantir que sa gestion ne devienne pas une faille de sécurité de l'infrastructure. Ainsi, les données sont chiffrées avant de quitter l'ordinateur : même interceptées (ex. attaque "Man in the Middle"), elles sont illisibles.

***Question 3 : Pourquoi faut-il changer les identifiants par défaut sur un pare-feu ?***

Là aussi, il s'agit de le sécuriser : on cherche à limiter au maximum tout risque d'intrusion dans l'infrastructure.

#### B. Sécurisation de lʼaccès administrateur

***Modifiez les paramètres du compte administrateur.***

On va modifier le mot de passe du compte `admin` : on se rend dans le menu System → User Manager et on clique sur le crayon pour modifier les paramètres du compte. On saisit alors un nouveau mot de passe dans les champs "Password" :

![Screen2](/TP5/Screen2.png)

... et on enregistre en cliquant sur le bouton "Save" en bas de page.

***Question 1 : Où se gèrent les utilisateurs ?***

Comme on a pu le voir ici, les utilisateurs se gèrent dans le menu System → User Manager. On a alors une liste des utilisateurs, on peut alors ajouter des utilisateurs, en supprimer, et modifier leurs paramètres...

***Question 2 : Quʼest-ce quʼun mot de passe robuste ?***

En principe, un mot de passe robuste est, d'un point de vue syntaxe, composé de majuscules, minuscules, chiffres, caractères spéciaux, et globalement d'au moins 12 caractères ; et d'un point de vue humain, non commun et impossible à deviner.

***Question 3 : Pourquoi sécuriser en priorité lʼaccès admin sur un équipement réseau ?***

On sécurise en priorité l'accès admin car c'est celui-ci qui ne doit être compromis sous aucun prétexte : si un attaquant prend le contrôle du pfSense, il prend le contrôle de tout ce qui entre et sort de l'infrastructure, du réseau...

### II. Comprendre les interfaces réseau

***Vérifiez lʼaffectation des interfaces WAN / LAN.***

On doit vérifier que l'interface "WAN" de pfSense correpsond à la carte "Réseau partagé" d'UTM ; et que l'interface LAN correspond à la carte "Hôte uniquement"...

![Screen3](/TP5/Screen3.png)

- MAC Réseau partagé : `CA:32:32:42:75:18` → on doit avoir la même pour WAN
- MAC Hôte uniquement : `8E:92:6E:43:B9:4D` → on doit avoir la même pour LAN

![Screen4](/TP5/Screen4.png)

- MAC WAN : `CA:32:32:42:75:18`
- MAC LAN : `8E:92:6E:43:B9:4D`

Les affectations sont correctes.

***Question 1 : Quelle interface permet lʼaccès Internet ?***

L'interface WAN (Wide Area Network) permet l'accès à Internet : c'est par cette carte que passeront les paquets échangés avec le réseau externe.

***Question 2 : Quelle interface correspond au réseau interne ?***

Pour le réseau interne c'est l'interface LAN (Local Area Network) qui gère les paquets. C'est le réseau local, qui permet notamment la gestion web de pfSense.

***Question 3 : Que se passerait-il si les interfaces étaient inversées ?***

**Côté WAN ← LAN**

Le pare-feu attend la connexion Internet sur le WAN et ainsi bloque les connexions entrantes non sollicitées sur cette interface. Si les PC du LAN sont sur le WAN, toutes leurs requêtes seront bloquées par ces règles de sécurité...

**Côté LAN ← WAN**

Pire encore de ce côté on aurait une faille de sécurité critique : l'interface d'administration (WebGUI) serait ouverte par défaut sur le LAN. En mettant Internet sur le port LAN, la gestion du pare-feu est exposée au monde entier, permettant à n'importe qui de tenter de s'y connecter.
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

### III. Configuration des services réseau

*(On créera au préalable une autre machine virtuelle sous Ubuntu, qui servira de client pour les services et fonctionnalités que l'on activera par la suite, à commencer par DHCP...)*

#### A. DHCP

***Configurez le serveur DHCP pour le réseau LAN.***

Pour configurer le serveur DHCP de pfSense, on se rend dans le menu Services → DHCP Server. Ici, il nous suffit de cocher l'option "Enable DHCP server on LAN interface" et de cliquer sur "Save" en bas de page, étant donné que les paramètres par défaut sont cohérents avec ce que l'on veut...

![Screen5](/TP5/Screen5.png)

***Question 1 : Pourquoi utiliser DHCP plutôt quʼune IP fixe ?***

On utilise DHCP pour attribuer automatiquement une IP à chaque nouvel appareil connecté au réseau, un peu comme dans un réseau domestique classique — en fait, si on rajoute pfSense entre le routeur et les appareils connectés dans un réseau domestique, cela permet de continuer à attribuer les IPs automatiquement sans risque de causer une erreur humaine...

En effet, sans serveur DHCP, on devrait attibuer manuellement une IP à chaque appareil, en veillant à chaque fois à ne pas attribuer une IP déjà prise...

En somme, DCP permet donc une gestion centralisée et automatisée des IPs, mais aussi du masque du sous-réseau, de la passerelle, des serveurs DNS...

***Question 2 : Quelle plage dʼadresses choisir ? Quelles adresses faut-il éviter dʼinclure dans la plage ?***

On doit choisir une plage d'adresses qui font toutes partie de notre sous-réseau (ici, `192.168.128.0`). La plage d'adresses ne doit pas non plus inclure :
- le serveur DHCP lui-même `192.168.128.2`,
- l'hôte de la VM `192.168.128.1`,
- ou l'adresse broadcast `192.168.128.255`.

Ici, on laisse donc `192.168.128.11 - 192.168.128.245`, assez vaste et qui évite également d'inclure des IPs que l'on réserverait à d'autres choses (ex. imprimantes, serveurs, switchs... en IP statique)...

***Vérifier si la VM Ubuntu obtient automatiquement une IP.***

> Dans cette configuration précise, UTM utilise une interface réseau spéciale de macOS (`bridge101`). Cette interface utilise un serveur DHCP, alimenté par le processus `bootpd`. Il n'est pas désactivable, on est donc obligé de l'empêcher de s'exécuter, en exécutant un `killall` en boucle dans le Terminal...

>     while true; do sudo killall bootpd 2>/dev/null; done

> Pendant ce temps on efface la configuration du serveur DHCP macOS de Ubuntu :

>     sudo systemctl stop NetworkManager
>     sudo rm -f /var/lib/NetworkManager/*.lease
>     sudo rm -f /var/lib/dhcp/dhclient.leases
>     sudo ip addr flush dev enp0s2; sudo dhclient -v enp0s2
>     sudo systemctl start NetworkManager

Maintenant que le serveur DHCP de macOS est "neutralisé", Ubuntu prend celui de pfSense...

![Screen6](/TP5/Screen6.png)

L'IPv4 de l'interface LAN de Ubuntu est à présent la première de la plage définie dans pfSense, on a donc bien la confirmation que c'est le DHCP de pfSense qui a attribué l'IP...

Enfin côté des baux DHCP sur l'interface web de pfSense (menu Status → DHCP Leases), on a bien une machine "Ubuntu" qui a pour IP la première de la plage définie...

![Screen7](/TP5/Screen7.png)

#### B. DNS

***Activez et configurez le résolveur DNS.***

On se rend dans les paramètres DNS de pfSense (menu Services → DNS Resolver). Celui-ci est activé par défaut, il ne reste plus qu'à le configurer.

- On commence par changer "Network Interfaces" en LAN. Cela évite que le résolveur ne réponde à des requêtes venant de l'extérieur (WAN).

- À l'inverse on change "Outgoing Network Interfaces" en WAN. C'est par cette interface que pfSense interrogera les serveurs DNS racines sur Internet, donc aucun intérêt d'inclure le réseau interne (LAN).

![Screen8](/TP5/Screen8.png)

C'est pour l'instant tout ce que l'on aura à configurer pour ce qui est du résolveur DNS. On peut enregistrer les changements de configuration en cliquant sur Save en bas de page...

Enfin on clique sur "Apply Changes" quand ce message s'affiche... :

![Screen9](/TP5/Screen9.png)

On peut alors tester sur Ubuntu si tout est en ordre :

    resolvectl status

On regarde quel serveur DNS est utilisé (ici, `192.168.128.2` soit pfSense)...

    nslookup google.com

...et on teste pour voir si le DNS résoud bien les noms de domaine en IP : là aussi ça fonctionne !

![Screen10](/TP5/Screen10.png)

***Question 1 : Pourquoi un pare-feu peut-il jouer le rôle de serveur DNS ?***

Une fois de plus cela a l'avantage de simplifier la centralisation et l'automatisation des paramètres pour toutes les machines du réseau, mais le principal avantage reste qu'en contrôlant le DNS, le pare-feu peut bloquer l'accès à des sites malveillants ou interdits dès la requête du nom, avant même que la connexion ne soit établie. Il y a aussi des logs qui permettent à l'administrateur de voir quels domaines sont consultés par les utilisateurs du réseau.

***Question 2 : Que se passe-t-il si le DNS ne fonctionne pas mais que le ping vers 8.8.8.8 fonctionne ?***

Si le Ping fonctionne mais pas le DNS, cela veut dire que la connexion Internet fonctionne, mais la machine est incapable de traduire un nom de domaine (ex. google.com) en IP : impossible d'accéder aux sites...

### IV. Autoriser lʼaccès Internet

#### A. Règles de pare-feu

***Configurez les règles nécessaires pour permettre aux machines du LAN dʼaccéder à Internet.***

On se rend dans le menu Firewall → Rules → LAN de pfSense.

![Screen11](/TP5/Screen11.png)

Dans un premier temps on va supprimer les règles par défaut : elles autorisent tout le trafic sans restriction. On coche alors les deux règles, et on clique sur le bouton "Delete".

On va maintenant configurer 4 règles : une pour le DNS, une pour HTTP, une pour HTTPS, et une pour Ping ; en cliquant sur le bouton "Add" avec une flèche vers le haut (pour les ajouter en début de liste). On paramètre alors nos règles :

**DNS**

![Screen12](/TP5/Screen12.png)

**HTTP**

![Screen13](/TP5/Screen13.png)

**HTTPS**

![Screen14](/TP5/Screen14.png)

**Ping**

![Screen15](/TP5/Screen15.png)

La liste de règles après application des changements devrait maintenant ressembler à cela :

![Screen16](/TP5/Screen16.png)

***Question 1 : Quelle doit être la source ? la destination ?***

Ici, la source est toujours la même : "LAN net". Cette option correspond à tout le réseau local (LAN), la règle s'applique donc à tous les appareils connectés.

La destination, elle, varie selon les règles : pour le DNS on a fait exprès de mettre "LAN address" uniquement (cette option correspond à pfSense uniquement, pertinent ici car c'est lui le serveur DNS) ; et pour les autres règles on met "any" pour pouvoir accéder à tout l'Internet.

***Question 2 : Faut-il autoriser tous les protocoles ?***

Évidemment, non, on n'autorise pas tous les protocoles. C'est le principe du "moindre privilège" : on n'autorise que ce dont on a besoin. Ici, en n'autorisant que DNS, le Web, et Ping, on s'assure qu'un logiciel malveillant ne pourra pas utiliser d'autres canaux pour communiquer avec l'extérieur.

***Testez un Ping vers pfSense, puis vers `8.8.8.8`.***

![Screen17](/TP5/Screen17.png)

✔ Du côté Ping tout semble en ordre

***Faites aussi un test DNS.***

    nslookup google.com

> Cette commande sert à traduire un nom de domaine en IP.

![Screen18](/TP5/Screen18.png)

✔ DNS a réussi à trouver l'IP de `google.com` : là aussi ça fonctionne

***Enfin essayez d'accéder au web.***

![Screen19](/TP5/Screen19.png)

✔ On peut librement accéder à Internet (ex. YouTube).

***Si ça ne fonctionne pas, où regarder ?***

Le principal endroit où regarder si quelque chose ne fonctionne pas, c'est les logs de pfSense (menu Status → System Logs). C'est notamment la raison pour laquelle on a activé les logs sur chacune des règles : pour être sûr que tout fonctionne bien. On les aurait désactivé après vérification pour ne pas qu'ils se remplissent trop vite.

**Exemple si on passe la règle des Ping en "block"**

Ping ne fonctionne plus, et des logs indiquent que le pare-feu a bloqué des requêtes Ping :

![Screen20](/TP5/Screen20.png)

#### B. NAT

***Vérifiez la configuration du NAT sortant.***

On se rend dans le menu Firewall → NAT → Outbound.

Ici on voit bien que le NAT est en mode "Automatic outbound NAT rule generation" : pfSense crée lui-même les règles de traduction nécessaires (d'IP privée en IP publique) pour que le réseau fonctionne immédiatement.

On voit aussi que la règle "Auto created rule" envoie les paquets du LAN vers le WAN après translation d'IP.

![Screen21](/TP5/Screen21.png)

***Question 1 : Pourquoi le NAT est-il nécessaire avec une interface WAN en NAT ?***

Ici, le NAT est nécessaire car Ubuntu utilise une adresse IP privée (192.168.128.11) qui est invalide sur Internet (il faut une IP publique, et le rôle du NAT est justement de faire la translation).

***Question 2 : Quelle est la différence entre NAT automatique et manuel ?***

Dans le cas d'un NAT automatique, c'est pfSense qui gère les règles de translation des IPs privées en IPs publiques. Cela garantit que tout nouveau réseau aura accès à Internet sans intervention de l'admin. Cependant, si le NAT est manuel, il faudra manuellement créer une règle par réseau (ce qui peut permettre un contrôle plus précis, exemple par machine, par port...).

***Question 3 : Comment vérifier quʼune traduction dʼadresse a lieu ?***

On peut se rendre dans le menu Diagnostics → States de pfSense.

![Screen22](/TP5/Screen22.png)

Ici on peut observer les translations d'IP en cours :

- `192.168.128.11` → `192.168.128.2` : Ubuntu → pfSense (LAN)
- `192.168.64.2` (`192.168.128.11`) → `34.107.221.82` : pfSense (WAN) → Internet

- > En somme : `192.168.128.11` → (pfSense) → `34.107.221.82`

✔ La traduction d'adresse a bien eu lieu !

### V. Filtrage

#### A. Blocage dʼun site spécifique

***Question 1 : Faut-il bloquer par IP ou par nom de domaine ?***

On privilégie le blocage par nom de domaine, car un seul domaine peut utiliser plusieurs IPs, et qui changent constamment. Bloquer le nom de domaine permet de bloquer toutes les IPs d'un site, qu'elles changent ou non.

***Question 2 : Que se passe-t-il si le site utilise HTTPS ?***

Lors de l'établissement de la connexion, le pare-feu peut identifier le nom du serveur via le DNS (bien que le flux soit chiffré). Il peut donc bloquer la connexion d'entrée. Le navigateur affichera alors une erreur "Délai d'attente dépassé" ou "Échec de la connexion sécurisée".

***Question 3 : Pourquoi le blocage par IP peut-il être contourné ?***

Un blocage par IP pourrait facilament être contourné premièrement car un site peut posséder plusieurs IPs différentes (il suffirait d'en prendre une qui n'est pas bloquée) ; ou en utilisant un VPN...

Bloquer un site par son IP peut aussi s'avérer problématique, par exemple si le site utilise un hébergeur tel que Cloudflare, car cela pourrait aussi bloquer des centaines d'autres sites légitimes qui partagent la même adresse

***Bloquez lʼaccès à un site web de votre choix.***

On va donc chercher à bloquer un site par son nom de domaine et non par son IP. Cependant, on ne peut pas directement créer une règle de pare-feu pour un nom de domaine : seulement pour une IP ou un alias.

Nous allons donc commencer créer un alias pour bloquer Instagram : menu Firewall → Aliases, et on cliquer sur le bouton "Add". On entre alors un nom d'alias et le nom de domaine à bloquer, on sauvegarde et on applique les changements.

![Screen23](/TP5/Screen23.png)

On retourne dans le menu Firewall → Rules → LAN. Là aussi on ajoute une règle en haut de la liste (pour qu'elle passe en priorité sur les autres). Cette fois-ci on sélectionne "Block" comme action, et en destination on sélectionne notre alias "Instagram". On enregistre et on applique la nouvelle règle.

![Screen24](/TP5/Screen24.png)

***Testez et observez les logs.***

![Screen25](/TP5/Screen25.png)

On essaie de Ping Instagram, mais on dirait qu'il n'y a pas de réponse.

![Screen26](/TP5/Screen26.png)

Du côté pare-feu, le diagnostic est confirmé : le pare-feu bloque nos requêtes vers Instagram.

#### B. Blocage dʼune catégorie de sites (jeux dʼargent)

***Créez une solution propre et maintenable pour bloquer plusieurs sites.***

Là aussi, on va utiliser un alias, qui cette fois ne servira plus à bloquer un seul site spécifique, mais bien plusieurs sites d'un seul coup. Cela nous permet d'arriver à un résultat propre, optimisé, et fonctionnel, avec un seul alias et une seule règle.

On répète la marche à suivre (menu Firewall → Aliases) pour créer un alias, sauf que cette fois-ci on ajoutera plusieurs noms de domaine (en cliquant sur "Add Host"), comme illustré :

![Screen27](/TP5/Screen27.png)

On ajoute ensuite la règle correspondante dans le pare-feu du LAN, et on enregistre les changements de configuration.

![Screen28](/TP5/Screen28.png)

On essaie là aussi de Ping les 3 sites bloqués : pas de réponse, les logs de pfSense confirment que le pare-feu bloque ces sites.

![Screen29](/TP5/Screen29.png)

**Rappel :** Pour que les refus de paquets soient loggés par pfSense, il faut cocher l'option "Log packets that are handled by this rule" sur la règle correspondante...

***Question : Pourquoi ne pas créer une règle par site ?***

Comme expliqué précédemment, on crée une règle, pour un alias, pour plusieurs sites à la fois premièrement car c'est plus simple et rapide d'ajouter plusieurs sites dans une même liste, plutôt de créer règle + alias à chaque fois (il en va de même pour ajouter ou supprimer un site de la liste, il suffit simplement de modifier l'alias). Cela évite aussi d'avoir une liste de règles et d'alias immense et illisible : à la place un a une seule blacklist.

Enfin, moins il y a de règles à parcourir pour chaque paquet qui traverse le réseau, plus le traitement est rapide et efficace pour le processeur du pare-feu : en somme c'est plus optimisé.

### VI. Aller plus loin

#### A. Blocage par catégorie (réseaux sociaux)

***Créez un alias pour une nouvelle catégorie et implémentez une règle.***

*(Pour un résultat propre, pour cette règle "réseaux sociaux", on supprimera au préalable la règle "Instagram" déjà définie, ainsi que l'alias correspondant...)*

On crée donc l'alias "Social_Media" (Firewall → Aliases) :

![Screen30](/TP5/Screen30.png)

... ainsi que la règle correspondante (là aussi on bloque donc les réseaux sociaux dans Firewall → Rules → LAN) :

![Screen31](/TP5/Screen31.png) 

***Analysez les logs.***

Toujours dans le menu Status → System Logs → Firewall :

![Screen32](/TP5/Screen32.png)

On voit ici que le pare-feu bloque des tentatives de Ping depuis notre VM Ubuntu vers différentes IPs (qui correspondent aux réseaux sociaux que nous avons interdits) : c'est bien la règle que nous venons de définir qui bloque ces Ping.

***Question : Que se passe-t-il si la règle est placée sous une règle "Pass Any" ?***

Toute règle placée sous une règle "Pass Any" ou "Deny Any" est ignorée : étant que plus la règle est haute, plus sa priorité est élevée, c'est elle qui s'appliquera par dessus les autres qui la suivent.

#### B. Règles horaires

***Créez un horaire et appliquez-le à une règle existante.***

Pour créer un horaire on va dans le menu Firewall → Schedules. On clique sur le bouton "Add".

On va maintenant donner un nom à notre nouvel horaire et sélectionner les plages de déclenchement. On clique alors sur les jours concernés sur le calendrier, puis sur la plage horaire de déclenchements pour les jours sélectionnés. On clique sur "Add Time" pour ajouter la plage sélectionnée à l'horaire, et on finit sa création...

On prend le scénario d'horaires d'étudiants qui n'ont cours que le matin, de 9h à 13h, les jours de la semaine. On effectue donc les sélections adéquates, et on enregistre...

![Screen33](/TP5/Screen33.png)

On va maintenant appliquer notre horaire sur notre règle qui bloque les réseaux sociaux : ainsi (en suivant notre scénario) ceux-ci seront bloqués pendant les horaires de cours et autorisés à la sortie.

On retourne dans notre menu de règles de pare-feu : on édite notre règle en cliquant sur l'icône de crayon. On ouvre les paramètres avancés en cliquant sur le bouton "Display Advanced" et on cherche le paramètre "Schedule". On sélectionne alors notre horaire (comme illustré ci-dessous), et on enregistre notre règle...

![Screen34](/TP5/Screen34.png)

On peut maintenant voir que notre horaire est appliqué à la règle et est en marche (on est le 23 février, 11h06) :

![Screen35](/TP5/Screen35.png)

...et on peut même regarder dans les logs pendant des Ping vers nos IP interdites par la règle, pendant l'horaire, et après l'horaire...

![Screen36](/TP5/Screen36.png)

***Question : Pourquoi les règles horaires sont-elles utiles en entreprise ?***

En entreprise, les règles horaires peuvent être utiles pour plusieurs raisons : elles permettent d'automatiser la coupure de sites non-essentiels (ex. réseaux sociaux) pendant les horaires de travail, afin notamment de préserver  la bande passante, réduire les distractions, ...

#### C. Serveur web local

***Installez un serveur web sur Ubuntu, pour autoriser un accès spécifique et bloquer les autres.***

Pour autoriser un accès spécifique et bloquer les autres, on va encore configurer de nouvelles règles dans notre pare-feu pfSense.

- Une règle pour autoriser uniquement les connexions sur le port 80 (HTTP) ;
- Une règle (juste en dessous) pour bloquer tout le reste...

On n'oublie pas de les mettre en haut de liste pour ne pas qu'elles soient ignorées par une règle plus (ou moins) restrictive.

![Screen37](/TP5/Screen37.png)

*(L'environnement de virtualisation ne permet pas de tester ces règles : le LAN virtuel est ici comme un switch, donc les connexions entre appareils de ce switch sont directes, en ignorant la route. Les connexions ne passent pas par pfSense, ce qui rend le blocage impossible......)*

***Question 1 : Filtrer par IP source ? par port ?***

On filtre par IP *et* par port : cela permet de limiter le filtrage à une machine précise (ici notre serveur Nginx), et en complément à un port en particulier à autoriser exclusivement sur cette machine...

***Question 2 : Pourquoi le pare-feu protège-t-il le LAN même en réseau interne ?***

Dans un réseau de travail par exemple, cela permet de loguer et contrôler l'activité en réseau, on limite aussi les risques de propagation d'un virus d'un appareil du LAN vers d'autres appareils du réseau, et on cloisonne les services (ex. la compta qui n'a pas à accéder aux caméras...).

#### D. Logs et analyse

***Activez la journalisation sur certaines règles.***

Comme on a pu le voir dans les précédentes étapes, pour journaliser certaines règles, on active l'option "Log packets that are handled by this rule".

Ainsi, dans le menu Status → System Logs → Firewall, on a maintenant une liste des paquets autorisés et des paquets bloqués, et quelle règle s'est occupé de quel paquet.

Par exemple, en essayant de Ping `8.8.8.8` (autorisé par notre règle "Allow Ping"), il s'affichera dans nos logs une ligne avec :
- une coche verte,
- l'heure exacte du Ping,
- l'interface utilisée,
- la règle qui l'a laissé passé ("Allow Ping"),
- l'IP locale de la machine qui a fait la requête de Ping (ex. notre VM `192.168.128.11`),
- l'IP de la destination (ici `8.8.8.8`)
- le protocole utilisé par le paquet (ici ICMP).

Si toutefois on essaie de Ping un réseau social pendant que notre horaire est actif, le paquet sera bloqué, et le log contiendra une ligne avec notamment une croix rouge.

Exemple de tout à l'heure :

> *Cf. `Screen36` [B. Règles horaires](#b-règles-horaires) ![Screen36](/TP5/Screen36.png)*

***Question 1 : Quelle est la différence entre paquet bloqué et autorisé ?***

Comme expliqué précédemment, un paquet bloqué (noté d'une croix rouge dans les logs) est un paquet qui s'arrêtera au pare-feu, et qui n'arrivera jamais à destination.

Un paquet autorisé (coche verte) en passant par le pare-feu, est un paquets qu'il va laisser passer, pour continuer son chemin vers la destination.

***Question 2 : Quelle règle a déclenché le blocage ?***

Ici (toujours dans notre cas de [règles horaires](#b-règles-horaires)), c'est à la fois la règle "Allow Ping" qui a déclenché le blocage avant 13h, et qui l'a stoppé après.

#### E. Filtrage MAC

***Testez le filtrage par adresse MAC.***

Pour filtrer des paquets, par exemple un ping vers `8.8.8.8.` par adresse MAC, on va créer une nouvelle règle dans le firewall. On choisira ici d'autoriser le Ping pour une seule de nos deux VM Ubuntu.

Comme pour nos noms de domaine, on ne peut pas non plus mettre une adresse MAC dans une règle pour la filtrer. On ne peut pas non plus utiliser d'alias cette fois-ci.
On va devoir commencer par créer un bail DHCP statique dans le menu Services → DHCP Server. On descend tout en bas, et on clique sur le bouton vert "Add", sous la section "DHCP Static Mappings for this Interface".
- On met la MAC de la machine dans le champ "MAC Address" ;
- On met l'IP fixe à attribuer devant "IP Address" ;
- On met un petit nom dans le champ "Hostname" ; 
- Et on coche l'option "ARP Table Static Entry" (verrouille physiquement l'IP sur la MAC de la carte réseau)...

![Screen38](/TP5/Screen38.png)

On enregistre en cliquant sur "Save" tout en bas et on répète pour la 2e machine.

On fera également attention à activer l'option "Enable Static ARP entries" dans les paramètres du serveur DHCP.

LAN MAC VM 1 (autorisé) : `C2:81:86:B8:35:2C` → `192.168.128.8`
LAN MAC VM 2 (bloqué) : `CE:AA:0E:EC:BF:24` → `192.168.128.9`

Ainsi, notre table de baux statiques ressemble à cela :

![Screen39](/TP5/Screen39.png)

On crée maintenant notre règle de filtrage :

![Screen40](/TP5/Screen40.png)

On a appliqué nos changements : plus qu'à tester :

![Screen41](/TP5/Screen41.png)
![Screen42](/TP5/Screen42.png)

On essaie de Ping `8.8.8.8` avant et après création de notre règle de "filtrage MAC", et enfin un site qui n'a rien à voir avec aucune de nos règle : on voit que notre nouvelle règle fonctionne normalement, comme peut le confirmer le log.

On a donc réussi à créer une règle qui, à première vue, ne fait que bloquer l'IP d'une machine, mais qui en réalité, bloque aussi et techniquement sa MAC puisqu'on a "physiquement lié" l'IP et la MAC...

***Question : Le filtrage MAC est-il réellement sécurisé ? Pourquoi est-il facilement contournable ?***

Non, le filtrage MAC n'est pas réellement sécurisé : il peut facilement être contourné, par exemple avec un logiciel de modification (logicielle) d'adresses MAC (pour en prendre une autre ou en prendre une toujours autorisée). C'est donc une barrière assez légère, et non une mesure robuste contre quelqu'un de déterminé ou expérimenté.

#### F. Portail captif

***Question 1 : Dans quels contextes utilise-t-on cela ?***

On utilise généralement les portails captifs :
- dans les lieux publics (aéroports, gares, restaurants (Wi-Fi gratuit)... ;
- dans une entreprise pour le réseau "Invités" afin qu'ils n'accèdent pas aux serveurs internes — ou pour que chaque employé ait son accès personnalisé au réseau ;
- ou encore dans les hôtels, pour identifier chaque client individuellement sur une connexion partagée.

***Question 2 : Quels avantages par rapport à une simple règle de pare-feu ?***

On a ici plusieurs avantages par rapport à une règle :
- ici, les limitations s'appliquent à un utilisateur (qui s'identifie donc d'où il veut sur le réseau) et non sur un simple appareil ;
- chaque utilisateur est responsable de ce qu'il fait, en sachant que c'est logué avec son nom comme il s'est identifié ;
- enfin on peut par exemple limiter la durée de connexion (exemple, 2h de Wi-Fi gratuit) ou le volume de données consommé, ce qu'une règle classique ne permet pas de faire facilement.

***Implémentez un portail captif.***

Cette fois-ci on va dans le menu Services → Captive Portal, et on clique sur le bouton "Add" : on lui donne un nom, éventuellement une description et on clique sur "Save & continue" pour commencer sa configuration.

![Screen43](/TP5/Screen43.png)

On commence par cocher l'option "Enable Captive Portal" pour l'activer. On a alors un panel d'options qui s'affichent (dont des limites de connexions simultanées, ou de trafic, mais que nous n'utiliseront pas).

- On choisit "LAN" comme interface ;
- et "Authentication Backend" comme Authentication Method (cela utilisera les comptes utilisateurs créés directement dans pfSense.) ;
- et on laisse "Authentication Server" sur Local Database.

![Screen44](/TP5/Screen44.png)
![Screen45](/TP5/Screen45.png)

On enregistre les modifications. On va maintenant créer deux utilisateurs pour le portail : 

On va dans le menu System → User Manager, et on clique sur "Add".

On va entrer un nom d'utilisateur (Username) et un mot de passe (Password), et enregistrer :

![Screen46](/TP5/Screen46.png)

...ensuite on retourne sur l'édition de l'utilisateur. Sous la section "Effective Privileges", on clique sur "Add" et on sélectionne "User - Services: Captive Portal login" dans la liste. On clique sur le bouton "Save" deux fois pour enregistrer...

![Screen47](/TP5/Screen47.png)

...et on recommence toute la procédure avec le 2e utilisateur.

![Screen48](/TP5/Screen48.png)

Sur la VM Ubuntu, on peut maintenant ouvrir un navigateur et tenter d'accéder à un site web : on devrait atterrir sur le portail captif. Dans le cas contraire l'OS, dès la connexion, se rend compte qu'un portail captif est en place et demande la connexion avant de pouvoir accéder au réseau...

![Screen49](/TP5/Screen49.png)

Après entrée du nom d'utilisateur et du mot de passe, on peut accéder à Internet comme si de rien n'était.

Et, comme d'habitude, nos logs (cette fois-ci dans le menu Status → System Logs → Authentication → Captive Portal Auth) nous confirment que nos connexions ont abouti, et depuis quelles machines (IPs).
On peut également aller dans le menu Status → Captive Portal, où l'on retrouvera également des options pour déconnecter à distance les machines du portail.

![Screen50](/TP5/Screen50.png)

Grâce à ces deux utilisateurs que nous avons pu créer, on aurait également pu autoriser / interdire certains sites pour l'un et pas pour l'autre, notamment grâce à la fonction de **groupes**...

#### G. Sauvegarde / restauration

***Sauvegardez la configuration, modifiez-la, et restaurez-la.***

**Sauvegarde**

On va dans le menu Diagnostics → Backup & Restore. Dans la section "Backup Configuration", on sélectionne quoi sauvegarder (ici, on a sélectionné de quoi sauvegarder un maximum d'informations et paramètres). On enregistre sur notre ordinateur la sauvegarde en cliquant sur le bouton "Download configuration as XML".

![Screen51](/TP5/Screen51.png)

**Modification**

On a supprimé toutes les règles de pare-feu.

![Screen52](/TP5/Screen52.png)

**Restauration**

On revient dans le menu Diagnostics → Backup & Restore. Sous la section "Restore Backup", on sélectionne les paramètres correspondants à notre sauvegarde (Restore Area, Encryption), on téléverse le fichier (Configuration file), et on clique sur le bouton "Restore Configuration" (et on confirme) : cela écrasera toute modification effectuée depuis notre sauvegarde. (Ici, notre suppression de règles.)

Ce message nous confirme que la restauration a bien été effectuée :

![Screen53](/TP5/Screen53.png)

...et on peut vérifier par nous même :

![Screen54](/TP5/Screen54.png)

***Question : Pourquoi la sauvegarde régulière est-elle essentielle en production ?***

En production, il est absolument essentiel de sauvegarder régulièrement la configuration du pare-feu, en particulier après que des changements aient été effectués dessus : dans l'éventualité où le pare-feu planterait, ou en cas de corruption de disque, ou en cas d'erreur de configuration fatale (par exemple), ou même d'attaque sur le réseau, on peut restaurer l'intégralité du réseau en quelques secondes au lieu de tout recommencer.

Il est aussi utile de remarquer que dans le cas de pfSense, dans le menu Diagnostics → Backup & Restore → Config History, on peut retrouver un historique des configurations après chaque modification de celle-ci : cela permet rapidement de corriger une erreur de configuration (par exemple la suppression de toutes les règles), sans avoir à penser à sauvegarder à chaque fois, même si le geste ne serait pas de trop......

![Screen55](/TP5/Screen55.png)

## TP 6

### Préparation du système

***Mettre à jour le système***

    sudo apt update
    sudo apt upgrade

On utilise `apt` pour rechercher les mises à jour (`update`) et les installer (`upgrade`).

***Installer les paquets nécessaires : `openvpn` et `easy-rsa`***

    sudo apt install openvpn
    sudo apt install easy-rsa

Là aussi on utilise `apt` pour installer les paquets.

### I. Comprendre la PKI

#### A. Questions

***1. À quoi sert une autorité de certification (CA) ?***

Une autorité de certification gère l'infrastructure de clés publiques (PKI) et garantit l'identité des entités (serveur et clients) au sein du réseau VPN.

Elle valide l'identité du serveur et des clients avant la connexion en utilisant sa propre clé privée pour signer les demandes de certificats des clients, et en fournissant un certificat racine que le serveur et le client utilisent pour vérifier que les certificats présentés sont authentiques et signés par la même autorité.

***2. Quelle différence entre clé privée et certificat ?***

Le certificat permet de prouver qu'un client est "connu" de l'infrastructure et qu'il peut se connecter.

La clé privée, elle, sert à prouver que le certificat appartient bien à son propriétaire légitime.

***3. Pourquoi un serveur VPN a-t-il besoin de certificats ?***

Dans un environnement tel qu'un VPN, où la sécurité et l'authenticité sont essentiels, l'utilisation de certificats s'avère utile pour :
- s'assurer que le client est autorisé par la CA avant d'ouvrir le tunnel (de même, le client vérifie le certificat du serveur pour éviter de se connecter à une machine malveillante) ;
- renforcer la sécurité des échanges en utilisant deux trousseaux de clés RSA pour chiffrer les communications ;
- simplifier la connexion avec ce même système de clés qui remplace un mot de passe plus facilement compromissible...

#### B. Création de l'infrastructure Easy-RSA

***Créer un environnement PKI***

Avant tout on se rend dans le dossier `/usr/share/easy-rsa/` : c'est là que `apt` a installé les fichiers qui nous serviront pour créer l'environnement PKI...

On initialise maintenant l'environnement :

    sudo ./easyrsa init-pki

![Screen1](/TP6/Screen1.png)

Maintenant que notre environnement PKI est généré et prêt à l'emploi (dans le dossier `pki`), on va mainteant pouvoir manipuler les CA et les certificats...

***Générer une CA, un certificat serveur, un certificat client, les paramètres Diffie-Hellman, et une clé TLS supplémentaire.***

**Génération CA**

    sudo ./easyrsa build-ca nopass

On vient de générer notre autorité de certification, en utilisant l'argument `nopass` pour ne pas avoir à utiliser de mot de passe de chiffrement pour la clé.

![Screen2](/TP6/Screen2.png)

Ici, c'est le certificat racine qui viant d'être créé : `ca.crt`.

**Génération certificat serveur**

On commence par générer la demande de certificat :

    sudo ./easyrsa gen-req server nopass

![Screen3](/TP6/Screen3.png)

*(en même temps on a également généré la clé privée du serveur)*

...et on la signe :

    sudo ./easyrsa sign-req server server

![Screen4](/TP6/Screen4.png)

Ici, le premier "server" correspond au type de certificat (indispensable pour les fonctions de serveur VPN) ; on spécifie ensuite le nom du  fichier que que l'on a créé à l'étape précédente, "server", pour le signer.

**Génération certificat client**

Même procédure pour le client que pour le serveur... :

    sudo ./easyrsa gen-req client1 nopass
    sudo ./easyrsa sign-req client client1

**Génération paramètres Diffie-Hellman**

    sudo ./easyrsa gen-dh

![Screen5](/TP6/Screen5.png)

On vient là ge générer les paramètres Diffie-Hellman (dans le fichier `dh.pem`) : ce sont eux qui permettront de sécuriser l'échange des clés de session entre le client et le serveur.

**Génération clé TLS supplémentaire**

    sudo openvpn --genkey secret ta.key

On vient de créer le dernier élément de l'infrastructure Easy-RSA : la clé TLS. Cette clé permet une sécurité accrue des communications : le serveur ignorera tout paquet qui n'est pas signé avec cette clé, ce qui protège contre les attaques par déni de service (DoS) et le scan de ports. Elle servira également pour l'authentification TLS du VPN...

***Question 1 : Où Easy-RSA crée-t-il ses fichiers ? Que contient le dossier `pki/` ?***

Une fois installé dans `/usr/share/easy-rsa`, il crée ses fichiers dans ce même dossier. Comme on a pu le voir, lorsqu'on a généré notre environnement PKI, celui-ci s'est créé dans un nouveau dossier, `pki`, qui contient tout ce qui est en rapport avec notre infrastructure, afin de pouvoir s'y retrouver... cela inclut notamment les clés privées, les requêtes de certificats et les certificats signés.

***Question 2 : Quelle est la différence entre `gen-req` et `sign-req` ?***

Comme on a pu le voir là aussi, là où `gen-req` permet, comme son nom l'indique, de générer une requête de certificat (et la clé privée correspondante), `sign-req` permet de signer cette requête afin que le certificat soit validé et que l'identité du serveur/client soit vérifiée.

***Question 3 : Que se passe-t-il si vous oubliez de signer un certificat ?***

Si on oublie de signer un certificat, cela provoquera à la connexion une rupture de la "chaîne de confiance" : l'identité du détenteur du certificat non-signé ne peut pas être vérifiée via l'autorité de certification, et ainsi la connexion sera rejetée.

### II. Configuration du serveur OpenVPN

#### A. Configuration de base

***Créer un fichier de configuration serveur dans `/etc/openvpn/server/server.conf`. On attend dans cette config un port d'écoute, un protocole, une interface virtuelle, un réseau attribué aux clients, et des références aux certificats.***

*(Pour faire fonctionner nos références proprement, on copiera au préalable notre infrastructure PKI (dossier PKI et clé TLS) dans le dossier...)*

On se rend dans le dossier (`cd`), et on crée le nouveau fichier avec `nano`. On y ajoutera les lignes suivantes :

![Screen6](/TP6/Screen6.png)

***Question 1 : Que signifie `dev tun` ?***

`dev tun` correspond à une interface réseau de type "*tun*nel". Elle permet de transporter uniquement des paquets IP, ce qui est plus léger et suffisant pour la majorité des connexions VPN.

***Question 2 : Quelle est la différence entre UDP et TCP pour un VPN ?***

Là où TCP garantit que chaque paquet arrive à destination (bien qu'un cas de "TCP meltdown" puisse fortement ralentir le flux), UDP est plus rapide car il n'attend pas d'accusé de réception pour chaque paquet, raison pour laquelle ce dernier est le protocole recommandé par défaut pour un VPN...

***Question 3 : Quelle plage IP choisir pour le VPN ? Pourquoi ?***

Pour le VPN, on peut choisir une plage d'adresses privées (définies par la RFC 1918), comme `10.8.0.0/24` : cela évite les conflits avec les IP de sites web (car ces plages sont non routables sur l'Internet public), mais aussi les conflits sur le LAN (on évite que deux réseaux utilisent la même plage)...

#### B. Routage et NAT

***Activer le forwarding IP.***

On va éditer le fichier de configuration système `/etc/sysctl.conf` et décommenter la ligne `net.ipv4.ip_forward=1`, comme illustré :

![Screen7](/TP6/Screen7.png)

...et on rafraîchit les paramètres du noyau avec le fichier `sysctl.conf` :

    sudo sysctl -p

***Mettre en place une règle NAT pour avoir l'accès internet depuis le VPN.***

On commence par récupérer le nom de l'interface réseau depuis laquelle on transmet les paquets depuis Internet :

    ip route

... c'est celle où là ligne commence par "default", ici `enp0s5`.

On configure ensuite le routage depuis cette interface :

    sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o enp0s5 -j MASQUERADE

...et on vérifie que le changement a bien été pris en compte : 

    sudo iptables -t nat -L -n -v

![Screen8](/TP6/Screen8.png)

On voit bien la cible `MASQUERADE` sur l'interface `enp0s5` pour la source `10.8.0.0/24`.

***Question 1 : Où se configure le paramètre `ip_forward` ?***

Le paramètre `ip_forward` peut se configurer de deux manières : grâce à la commande `sudo sysctl -w net.ipv4.ip_forward=1` (seulement, cette méthode est temporaire, jusqu'au prochain redémarrage...) ou en modifiant le fichier de configuration système comme tout juste réalisé...

***Question 2 : Quelle commande permet d'afficher les règles NAT actuelles ?***

Pour cela on peut utiliser la commande `sudo iptables -t nat -L -n -v` : elle permet d'afficher la table NAT de notre système, avec notamment nos routes, y compris celle que l'on vient de définir...

***Question 3 : Pourquoi faut-il "masquerader" le réseau VPN ?***

Car il ne faut pas confondre réseau Internet public et réseau privé : les deux sont incompatibles entre eux, car des millions de réseaux privés utilisent cette même adresse. Le "Masquerade", une forme de NAT, fait en sorte que l'adresse IP privée du client soit remplacée par sa propre adresse IP publique avant d'envoyer le paquet sur Internet. Le serveur (VPN) va mémoriser quel client a fait la demande, réceptionner la réponse de Google, puis la renvoyer au bon client VPN.

#### C. Démarrage et analyse du service

***Démarrer le service OpenVPN et vérifier son état.***

On va utiliser `systemctl` pour démarrer le serveur OpenVPN, et plus précisément notre instance "server", on rajoute donc `@server` :

    sudo systemctl start openvpn-server@server

... et on peut dès à présent vérifier si le service est bien en route :

    sudo systemctl status openvpn-server@server

... et que l'interface "tunnel" a bien été créée :

    ip addr show tun0

![Screen9](/TP6/Screen9.png)

Ici, tout semble en ordre : le service est actif, et l'interface "tunnel" a bien été créée et affectée.

***Question 1 : Si le service échoue, quelle commande permet d'afficher ses logs système ?***

Pour afficher les logs système d'un service (s'il est en échec par exemple), en l'occurrence de notre instance OpenVPN, on peut utiliser la commande `sudo journalctl -u openvpn-server@server` (`-u` permet justement d'afficher les logs d'un service en particulier). Les logs du service s'affichent alors, comme illustré :

![Screen10](/TP6/Screen10.png)

***Question 2 : Quelle est la différence entre `status` et `journalctl` ?***

Là où `systemctl status` donne un résumé de l'état du service et affiche seulement les 10 dernières lignes de log (souvent insuffisant pour un diagnostic complet), `journalctl` permet de consulter les logs complets du service. `systemctl status` peut donc servir pour savoir si le service est lancé, et `journalctl` peut servir à remonter jusqu'à la source d'un problème le cas échéant...

***Question 3 : Les chemins vers les certificats sont-ils corrects ?***

Il semblerait que les chemins vers les certificats soient tous corrects : dans le cas contraire, le service ne pourrait pas s'exécuter (status failed) et les logs afficheraient une erreur explicite (ex. "Cannot load certificate file" ou "No such file or directory").
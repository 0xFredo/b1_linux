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
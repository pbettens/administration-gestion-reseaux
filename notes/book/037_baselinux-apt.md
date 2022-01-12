\clearpage

## Gestionnaire de paquet `apt` 

\btwoc
\index{apt}


`apt` (_advanced package tool_) est l'outil de gestion des paquets _debian_. 

Une distribution linux est plus qu'un simple noyau linux, c'est un ensemble
cohérent de logiciels qui sont « distribués » par une « entité ». Cette entité
peut-être une entreprise ou une communauté. Une distribution linux rassemble les
logiciels en un ensemble **cohérent** mais également **stable** et offre un
système de maintenance de ces logiciels. 

Chaque distribution a ses particularités : l'usage (bureautique, serveur…), le
matériel sur lequel l'installer, la facilité d'utilisation, les choix
prédéfinis, le support… Il faudra donc à un moment **choisir** sa distribution
linux. Pour le cours, le choix s'est porté sur **_debian_**. 

En fonction de la distribution choisie, le système de gestion des logiciels —
les « paquets » — diffère. Les plus connus sont les systèmes basés sur les
paquets `deb` et `rpm`. 

Un paquet `deb` est une archive au format `ar` contenant elle-même deux
archives ; `control.tar.gz` et `data.tar.gz` et un fichier de version
(`debian-binary`). Les paquets debian se manipulent à l'aide de la commande
`dpkg` pour une gestion bas niveau paquet par paquet. La gestion quotidienne de
son système se fait quant-à elle grâce à `apt`[^f_037_1]. 

\index{miroir}
\index{source.list}

Les paquets _debian_ sont disponibles sur plusieurs sites et il est également
possible d'installer et maintenir un **miroir** de l'un de ces sites. La
première chose à faire est de configurer le miroir utilisé. Cela se fait dans
le fichier `/etc/apt/sources.list` qui pourrait avoir l'allure
suivante[^f_037_2] :

```bash
deb http://ftp.be.debian.org/debian/ \
    buster main contrib non-free
deb-src http://ftp.be.debian.org/debian/ \
    buster main contrib non-free

deb http://security.debian.org/ \
    buster/updates main
deb-src http://security.debian.org/ \
    buster/updates main
```

- _buster_ est le nom de la _release_ stable à l'heure de la rédaction de ces
  notes[^f_037_3]. 

- _main contrib non-free_ seul _main_ est nécessaire à l'installation du
  système, les autres valeurs peuvent être ajoutées pour accéder aux paquets
  _contrib_ ou _non-free_. Les paquets _non-free_ peuvent être utile pour un
  _driver_ propriétaire particulier par exemple. 

### `apt`

\index{apt}

- `apt update` met à jour la liste des paquets disponibles ainsi que leur version en local ;

- `apt list --upgradable` liste les paquets qui peuvent être mis à jour ;

- `apt upgrade` met à jour le système en téléchargeant les paquets et en les installant sur le système ; 

- `apt search <pattern>` fait une recherche dans la liste des paquets disponibles à la recherche d'un paquet correspondant au _pattern_ ;

```bash
# apt search bind9
En train de trier... Fait   
Recherche en texte intégral... Fait
bind9/stable,now 1:9.11.5[cut] \
    amd64  [installé]
  Serveur de noms de domaines internet

bind9-doc/stable 1:9.11.5[cut] all
  documentation de BIND
[cut]
```

- `apt install <paquet>` installe le paquet _paquet_ et les paquets dont il dépend ;

    - `--reinstall` cette option demande de faire une réinstallation du paquet ;

- `apt remove` désinstalle un paquet. Ne désinstalle pas les dépendances ;

- `apt autoremove` désinstalle les paquets  que ne sont plus nécessaires ;

**Remarque** : Lors de l'installation d'un paquet, toujours faire un _update_
avant une installation. 

En effet, la procédure d'installation va tenter d'aller chercher le paquet dans
la version renseignée dans la liste des paquets disponibles **locale**. Si le
paquet a été mis à jour sur le miroir, `apt` tentera de télécharger un fichier
qui n'existe plus. 

[^f_037_1]: Historiquement, la gestion des paquets debian se faisait à l'aide des commandes `apt-get` et `apt-cache`. Ensuite la commande `aptitude` a été conseillée. Aujourd'hui, la commande `apt` suffit. 

[^f_037_2]: Pour une configuration en Belgique, choisir un miroir proche. Le miroir de son fournisseur d'accès est un bon choix. Si beaucoup de machines debian sont installées dans son entreprise maintenir un miroir local est sans doute une bonne idée.  

[^f_037_3]: Il est conseillé de renseigner le nom de la _release_ plutôt que `stable` pour éviter un saut de version inopiné lors d'une mise à jour du système.  Chez debian, elles se nomment : Hamm, Slink, Potato, Woody, Sarge, Etch, Lenny, Squeeze, Wheezy, Jessie, Stretch, Buster et Bullseye.


\etwoc
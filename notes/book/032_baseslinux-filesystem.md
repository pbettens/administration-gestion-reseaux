\yaline

## Le système de fichiers

\index{filesystem}
\btwoc

Une machine _\*nix_ n'a qu'un seul système de fichier (_filesystem_) dont la
racine se note `/`. Ce _filesystem_ représente comment on accède aux
« informations » stockées sur un « support ». 

- *l'information* peut bien sûr être un fichier ou un programme mais elle peut
  aussi être un _pseudo-fichier_ faisant le lien avec un composant matériel
  (_hardware_).
  
- *le support* sera une partition d'un disque dur bien sûr mais également un
  accès à un partage distant accessible par le réseau. Il pourra aussi être un
  pseudo-fichier accédant au matériel. 

![Exemple de filesystem](resources/images/filesystem.png)

La structure du _filesystem_ *nix est standardisée par le « Filesystem Hierarchy
Standard »@wikipedia-fhs^,^ @fhs-pdf — même si les différentes distributions ne
respectent pas le standard à la lettre, les grandes lignes le sont.

Le groupe en charge de l'harmonisation de _filesystem_ à choisi de distinguer :

- les fichiers **partageables** entre plusieurs machines de ceux qui ne le sont
  pas. Les pages de manuel peuvent par exemple être partagées;
- les fichiers **variables** et ceux qui le sont peu. Les fichiers variables ont
  un contenu et une taille qui varie fortement pendant la vie du programme qui
  les utilise et donc du système. Par exemple, les mails entrants et sortant. 

\etwoc

En voici un résumé : 

+--------------+-----------------------------------------------------------+
| *Répertoire* | *Description*                                             |
+==============+===========================================================+
| `/bin`       | Les commandes de base nécessaires au démarrage et à       |
|              | l'utilisation d'un système minimaliste (`ls`, `cat`…)     |
|              | exceptées les commandes _root_ qui se trouvent dans       |
|              | `/sbin`. _bin pour binaire_.                              |
+--------------+-----------------------------------------------------------+
| `/boot`      | Les fichiers nécessaires au démarrage (par ex. le noyau   |
|              | et _grub_). _boot pour… boot_                             |
+--------------+-----------------------------------------------------------+
| `/dev`       | Les _pseudo-fichiers_ correspondant à un périphérique     |
|              | _hardware_. _dev pour devices_                            |
+--------------+-----------------------------------------------------------+
| `/etc`       | Fichiers de configuration.                                |
|              | Contient souvent un répertoire pour le programme          |
|              | concerné (par ex. `/etc/apache2/`).                       |
|              | _etc pour editable text configuration_.                   |
+--------------+-----------------------------------------------------------+
| `/home`      | Répertoires des _users_ du système (par ex. `/home/alice`)|
|              | _home pour « qu'il fait bon chez moi »_.                  |
+--------------+-----------------------------------------------------------+
| `/lib`       | Bibliothèques logicielles (_libraries_) nécessaires aux   |
|              | binaires de `/bin` et `sbin`. _lib pour libraries_.       |
+--------------+-----------------------------------------------------------+
| `/mnt`       | Point de montage pour les systèmes de fichiers            |
|              | temporaires. _mnt pour mount_.                            |
+--------------+-----------------------------------------------------------+
| `/media`     | Point de montage pour les médias amovibles (anciennement  |
|              | les CD-ROM, aujourd'hui les clés USB et demain…).         |
|              | _media pour medias_                                       |
+--------------+-----------------------------------------------------------+
| `/opt`       | Logiciels optionnels, ce sont ceux qui ne sont pas        |
|              | proposés par la distribution et installés pour tous les   |
|              | _users_. Il est préférable d'utiliser `/opt` à            |
|              | `/usr/local`. _opt pour optionals_.                        |
+--------------+-----------------------------------------------------------+
| `/proc`      | Système de fichiers virtuel pour les processus.           |
|              | _proc pour processes_                                     |
+--------------+-----------------------------------------------------------+
| `/root`      | Répertoire _home_ de _root_.                              |
+--------------+-----------------------------------------------------------+
| `/sbin`      | Binaires pour _root_. _sbin pour system binaries_.        |
+--------------+-----------------------------------------------------------+
| `/srv`       | Données pour les services hébergés (contenu web statique, |
|              | base de donnée…). _srv pour services_.                    |
+--------------+-----------------------------------------------------------+
| `/tmp`       | Fichiers temporaires. Le répertoire est vide au           |
|              | démarrage. _tmp pour temporary_.                          |
+--------------+-----------------------------------------------------------+
| `/usr`       | Arborescence semblable à la racine pour les fichiers et   |
|              | répertoires qui ne sont pas nécessaires au fonctionnement |
|              | minimals du système (par ex. `/usr/bin`, `/usr/lib`,      |
|              | `/usr/sbin`, `/usr/share`, `/usr/include`…)               |
|              | _usr pour unix system resources_                          |
+--------------+-----------------------------------------------------------+
| `/var`       | Destiné à recevoir des fichiers variables divers.         |
|              | _var pour variables_.                                     |
+--------------+-----------------------------------------------------------+
| `/var/cache` | Pour différents cache (par ex. _bind_, _apt_).            |
+--------------+-----------------------------------------------------------+
| `/var/lock`  | Fichiers de verrouillage.                                 |
+--------------+-----------------------------------------------------------+
| `/var/mail`  | Boites mails des utilisateurs.                            |
+--------------+-----------------------------------------------------------+
| `/var/spool` | Données en attentes de traitement (par ex. pour           |
|              | l'impression, les mails, les tâches planifiées…).         |
+--------------+-----------------------------------------------------------+


Remarques : 

- Certains binaires se trouvant dans `/sbin` peuvent être exécutés par un
  _user_ sans privilège tant que cet _user_ ne demande pas une action à laquelle
  il ou elle n'a pas droit. Par contre `/sbin` ne se trouve pas dans son `PATH`.
- Historiquement un petit disque — rapide — était réservé aux fichiers
  essentiels — se trouvant dans `/bin`, `/sbin`… — tandis que les autres étaient
  placés dans `/usr` qui pouvait donc se trouver sur un autre disque. 
- Les répertoires `/usr/src` et `/usr/include` sont plutôt destinés à recevoir
  tout ce qui est nécessaires à la compilation C ou C++ des différents logiciels
  se trouvant sur la machine. 

\yaline

### Chemins relatifs, chemins absolus

\btwoc

Les noms de fichiers commençant par `/` sont des noms de fichiers **absolus**
c'est-à-dire faisant référence à la racine du _filesystem_. Le nom est
indépendant du répertoire courant.  

Il existe un raccourci représentant le répertoire _home_ de l'utilisateurice :
`~`. Pour l'utilisatrice _alice_, il s'agit de `/home/alice`. 

Par exemple : 

```bash
/etc/apache2/apache2.conf
~/bin/yascript.sh
```

Les noms de fichiers **relatifs** sont relatifs au répertoire courant, ils
s'expriment sans la référence à la racine `\`. Les raccourcis `.` et `..`
représentent respectivement le répertoire courant et le répertoire parent. 

Par exemple si le répertoire courant est `/home/alice` : 

```bash
../../etc/apache2/apache2.conf
bin/yascript.sh
```
\etwoc
\yaline
\newpage

### Les différents types de fichiers

\btwoc

Un fichier est désigné par un **nom** bien sûr et possède un **_inode_** unique.
L'_inode_ d'un fichier contient : le type de fichier, les droits d'accès, le
nombre de liens physiques, un _uid_ du propriétaire, un _gid_, la taille du
fichier, les dates d'accès, de modifications, les connexions et l'adresse du
fichier. 

Les différents types de fichiers sont : 

- fichier ordinaire. Quasi tout ce qui peut être enregistré est un fichier;

- répertoire (_directory_). C'est un fichier contenant une table associant un
  nom à un _inode_. Les noms et les _inodes_ des fichiers qu'il contient;

- fichier de périphérique. Les _peusdo-fichiers_ faisant le lien vers du
  matériel (disque, terminal, sortie parallèle, sortie série, sortie usb…). Ils
  se trouvent dans `/dev`;

- lien physique et lien symbolique :

    - un lien physique est un fichier contenant l'_inode_ du fichier qu'il référence. C'est un nom supplémentaire pour le même inode;
      
      À chaque ajout d'un lien physique le nombre de référence vers le fichier augmente. À chaque suppression, il diminue. Lorsque le nombre de référence passe de 1 à 0, le fichier est supprimé. 

    - un lien symbolique montre le chemin vers le fichier pointé. C'est un nouveau fichier — avec un nouvel _inode_ — contenant l'_inode_ du fichier vers lequel le lien pointe. 

\etwoc

```bash
$ echo "foo" > file
$ ln file fileln
$ ln -s file filens
$ ls -il 
total 8
262155 -rw-r--r-- 2 pbt pbt 5 fév 18 22:52 file
262155 -rw-r--r-- 2 pbt pbt 5 fév 18 22:52 fileln
262158 lrwxrwxrwx 1 pbt pbt 4 fév 18 22:53 filelns -> file
```

\btwoc

- tube nommé (_named pipe_). Un tube nommé est un tube… nommé. Comme la commande
  _pipe_ « `|` », un tube permet de relier la sortie d'un processus à l'entrée
  d'un autre. Un pipe nommé permet à un processus d'écrire dans un «_fichier
  sans fin ». Pendant ce temps, un autre processus peut lire dans ce « fichier
  sans fin ». 

\etwoc
\yaline
\clearpage

### Les permissions

\btwoc

`ugo` pour _user_, _group_ et _other_. Un fichier, quel que soit son type a des
permission pour son propriétaire, son groupe et «  les autres ». Les autres
étant tous les _users_ n'appartenant pas au groupe. 

Pour chacun d'entre eux, les droits peuvent être `rwx` pour _read_, _write_ et
_execute_. Les droits d'un fichier se présentent comme suit : 

\etwoc


```bash
-rwxr---wx 1 alice yagroup  46K fév 11 16:11 filename.pdf
```
\btwoc

- `r` permet de lire un fichier ou de voir le contenu d'un répertoire;
- `w` permet d'écrire dans un fichier ou d'écrire dans un répertoire. Écrire
  dans un répertoire, c'est ajouter, effacer, renommer un fichier;
- `x` indique que le fichier est exécutable (il peut s'agir d'un binaire ou d'un
  script) ou que le répertoire est « traversable ». 

\etwoc
\yaline

### Le coin des commandes 

\btwoc

#### `ls`

\index{ls}

`ls` donne la liste des fichiers d'un répertoire. Voici quelques options : 

- `-l`, format long donne les droits, le nombre de liens vers le fichier, le propriétaire, le groupe, la taille, la date de dernier accès et le nom;

- `-i`, affiche le numéro d'_inode_ du fichier;

#### `cd`

\index{cd}

`cd` (_change directory_) change le répertoire courant. 

```bash
cd /home/alice
cd ..
cd ~/bin
cd -
```

L'option `-` change le répertoire vers le répertoire précédent (pas le parent). 

#### `chmod`

\index{chmod}

`chmod` permet de changer le propriétaire et le groupe d'un fichier ou d'un répertoire, le _mode_. Le mode peut être représenté de manière symbolique ou octale. 

- représentation symbolique `[ugoa...][-+=][perms]` où _perms_ est 0 ou plus
  parmi les lettre `rwxXst`. Plusieurs modes symboliques peuvent être donnés,
  séparés par des virgules;

- représentation octale est composée de maximum 4 chiffres octaux. Le premier
  permet de placer le _user id_ ou le _group id_ ou le _sticky bit_, le second
  pour le _user_ `u`, le troisième pour le _group_ `g` et le quatrième pour
  _other_ `o`. 

  L'usage courant se compose des trois derniers. 

```bash
chmod u+x, go-w foo
chmod 510 bar
```

#### `chgrp`

\index{chgrp}

Change le groupe de chaque fichier. 

```bash
chgrp newgroup file
```

#### `du` et `df`

\index{du}
\index{df}

Ces deux commandes donnent des informations sur l'espace disque. `du` (_disk
usage_) donne l'espace utilisé pour un répertoire tandis que `df` (_disk free_)
donne l'espace disponible sur la partition concernée. 

Option `-h` donne les résultats de manière « _human readable_ ».

#### `u|mount`

\index{mount}
\index{umount}

`mount` et `umount` permettent d'ajouter / retirer un support au système. Pour qu'un _filesystem_ soit accessible il doit être monté sur un répertoire. 

Les montages habituels d'un système sont ceux réalisés au _boot_ du système et
ceux lorsque l'utilisateurice insère une clé USB. Le premier montage est fait
automatiquement grâce aux renseignements se trouvant dans le fichier
`/etc/fstab` tandis que l'autre est actuellement pris en charge par un
« auto-mount » dès que l'insertion de la clé est faite. En tant que _root_ il
sera parfois nécessaire de manipuler les différentes partitions. 

```bash
mount
mount /dev/sdb1 /mnt/backup
```

- la première commande (sans paramètre) montre l'état des différents montages;
- le seconde monte la première partition du second disque scsi dans le répertoire `/mnt/backup` (qui doit exister au préalable).

#### `touch`

\index{touche}

`touch` « touche » le fichier. L'effet est de changer la date d'accès au fichier ou de créer un fichier vide s'il n'existait pas. 

#### `mkfifo`

\index{mkfifo}

`mkfifo` crée un pipe nommé (_named pipe_). 

\etwoc
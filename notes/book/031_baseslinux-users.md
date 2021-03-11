
## Les utilisateurices _users_ et les groupes

\bintro

Un système correctement administré a plus de _groups_ que de _users_. 

\eintro

\btwoc 
\index{group}\index{user}
\index{passwd}\index{shadow}

Lors de l'installation, un compte administrateur ayant tous les privilèges est
créé. Le compte _root_. En plus de ce compte au minimum un compte sans privilège
particulier est ajouté. Nous l'appellerons _user_ même si le nom peut être
choisi.

La (première) bonne pratique est de se connecter au système avec un compte non
privilégié — le compte _user_ — et de n'utiliser les privilèges de _root_ que
lorsque c'est nécessaire. 

_root_ devra gérer les utilisateurs du système dans deux situation différentes :

- la première si le système doit être accessibles par plusieurs utilisateurices,
  il sera nécessaires de gérer les comptes. C'est évident ; 

- la seconde est pour l'installation de services sur le système. Une bonne
  pratique est d'associer un compte à chaque service afin que chaque service
  tourne avec les privilèges de ce compte… et pas ceux de _root_. 

  Par exemple, le service _dns_ tourne sous le compte _bind_, le serveur web
  _apache2_ avec le compte _www-data_…

Créer un compte consiste en l'enregistrer dans le système en lui donnant un
_login_, _password_, un répertoire _home_, un _shell_… tout cela se traduit par
une ligne dans le fichier `/etc/passwd` et une autre dans `/etc/shadow`. 


`$ cat /etc/passwd`
```bash
login:passwd:uid:gid:comment:home:shell
user:x:1000:1000:user,,,:
  /home/user:/bin/bash
```

- `login` le nom associé au compte. La bonne pratique est d'utiliser un _login_
  en minuscules, sans accents ni caractères spéciaux. La longueur est limitée à
  16 caractères;

- `passwd` contenait anciennement le mot de passe hashé associé au compte.
  Aujourd'hui, _linux_ ne laisse plus un hash en lecture et le mot de passe ne
  se trouve plus[^f031_1] dans `/etc/passwd`.

    Le champ du mot de passe chiffré peut être vide. Dans ce cas, aucun mot de
    passe n'est nécessaire pour s'authentifier avec le compte donné. Cependant,
    certaines applications qui lisent le fichier `/etc/passwd` peuvent décider
    de ne donner aucun accès si le mot de passe est vide. Si le mot de passe est
    un « `x` » minuscule, alors le mot de passe chiffré se trouve dans le
    fichier _shadow_ (`man 5 shadow`) ; il doit y avoir une ligne correspondante
    dans le fichier shadow, sinon le compte de l'utilisateur n'est pas valide.
    Si le mot de passe est constitué d'une autre chaîne, alors il est considéré
    comme un mot de passe chiffré, comme indiqué dans _crypt_ (`man 3 crypt`). 

    Ce champ peut aussi prendre la valeur « `*` » qui précise qu'il n'est pas
    possible de se connecter au compte avec `login` ou la valeur « `!` » qui
    empêche toute connexion au compte. Ces valeurs sont généralement utilisées
    pour les comptes de service. Les comptes qui sont utilisés par les services
    et qui ne sont pas destinés pour qu'une personne puisse s'y logger. 

- `uid` est l'_user id_, l'identifiant unique de l'utilisateur. La valeur 0 est
  celle de _root_, les autres sont libres. Les premières valeurs sont utilisées
  par le système. En fonction des distributions les _uid_ des comptes commencent
  à partir d'une certaine valeur. Pour _debian_, c'est 1000. 

    Attention certaines applications se basent sur l'_uid_ et pas le _login_ du
    compte. Il s'agit donc d'agir avec prudence lorsque des machines communiquent
    entre elles pour ces quelques services (par exemple `nfs`) ;

- `gid` est le _group id_, l'identifiant unique du groupe. Chaque compte est
  associé à un groupe principal et peut être ajouté à d'autres groupes. Le
  groupe principal est renseigné ici, les autres dans `/etc/group`[^f031_2] ;

- `comment` série de valeurs reprenant le nom complet du compte ainsi que
  diverses informations ; 

- `home` répertoire _home_ du compte. Chemin absolu ;

- `shell` shell associé au compte. Généralement `/bin/bash`. 

    Cette valeur peut être positionnée à `/bin/false` pour désactiver le compte
    ou pour empêche un login au compte. 

[^f031_1]: S'il s'y trouve il faut revoir la sécurité de cette machine. 
[^f031_2]: Les lignes sont de la forme `group:password:gid:users_list`. La
commande `groups` donne la liste des groupe d'un _user_. Un _grep_ pourrait
faire l'affaire `grep user /etc/group`. 

Le fichier `/etc/shadow` est un fichier qui contient les informations cachées
concernant les mots de passe des comptes et leurs dates de validité.

`# cat /etc/shadow`
```bash
user:$6$0--cut--Z3U/:17309:0:99999:7:::
```

Ce fichier ne doit pas être accessible en lecture par les utilisateurices
normaux afin de maintenir la sécurité des mots de passe, en particuliers pour
prévenir les attaques par dictionnaires.
 
Chaque ligne de ce fichier contient 9 champs, séparés par des deux-points
(« `:` »), dans l'ordre suivant :

- `login` le login — existant — du compte concerné ;

- `password` le mot de passe _hashé_. 

    Si le champ du mot de passe contient une chaine qui ne peut pas être un
  résultat valable de crypt(`man 3 crypt`), par exemple si elle contient les
  caractères `!` ou `*`, alors l'utilisateur ou l'utilisatrice ne pourra pas
  utiliser son mot de passe UNIX pour se connecter (mais il se peut que
  le compte puisse se connecter au système par d'autres moyens).
  
- `date du dernier changement de mot de passe` la date du dernier changement de
  mot de passe, exprimée en nombre de jours depuis le 1^er^ janvier 1970.
 
    Quand cette valeur vaut `0` un changement de mot de passe est requis à la
    prochaine connexion. 
    
    Quand la valeur est absente (champ vide), les fonctionnalités de
    vieillissement de mot de passe sont désactivées.

- `âge minimum du mot de passe` l'âge minimum du mot de passe est la durée (en
  jour) que l'utilisateur devra attendre avant de pouvoir le changer de nouveau.
 
    Un champ vide ou une valeur de 0 signifie qu'il n'y a pas d'âge minimum pour
    le mot de passe.
 
- `âge maximum du mot de passe` l'âge maximum du mot de passe est la durée (en
  jour) après laquelle l'utilisateur devra changer son mot de passe.
 
    Une fois cette durée écoulée, le mot de passe restera valable. Il sera
    demandé à l'utilisateur de le changer la prochaine fois qu'il se connectera.
    Un champ vide signifie qu'il n'y a pour le mot de passe aucune limite d'âge,
    aucune période d'avertissement d'expiration et aucune période d'inactivité
    (voir ci-dessous).

- `période d'avertissement d'expi-ration du mot de passe` la durée (en jour)
  pendant laquelle l'utilisateur sera averti avant que le mot de passe n'expire
  (voir l'âge maximum du mot de passe ci-dessus).
 
    Un champ vide ou une valeur de 0 signifie qu'il n'y aura pas de période
    d'avertissement d'expiration du mot de passe.
 
- `période d'inactivité du mot de passe` la durée (en jour) pendant laquelle le
  mot de passe sera quand même accepté après son expiration (voir l'âge maximum
  du mot de passe ci-dessus). L'utilisateur devra mettre à jour son mot de passe
  à la prochaine connexion.
 
    Après expiration du mot de passe suivie de la période d'expiration, plus
    aucune connexion n'est possible en utilisant le mot de passe de
    l'utilisateur. L'utilisateur doit contacter son administrateur. Un champ
    vide signifie qu'aucune période d'inactivité n'est imposée. date de fin de
    validité du compte La date d'expiration du compte, exprimé en nombre de
    jours depuis le 1er janvier 1970.
 
    Un champ vide signifie que le compte n'expirera jamais.
 
    La valeur 0 ne doit pas être utilisée puisqu'elle peut être interprétée soit
    comme un compte sans expiration, soit comme ayant expiré le 1^er^ janvier 1970.
 
- le dernier champ est réservé pour une utilisation future.

Outre ces deux entrées dans les fichiers `/etc/passwd` et `/etc/shadow` la
création d'un compte entraine la copie des fichiers contenu dans `/etc/skel`
dans le répertoire _home_ du compte. C'est là que _root_ peut paramétrer
certains fichiers de configuration avant la création d'un compte. Par défaut, ce
répertoire contient : 

```bash 
alice@harmony:~$ tree -a /etc/skel
/etc/skel
├── .bash_logout
├── .bashrc
└── .profile

0 directories, 3 files
```

Un manière simple de désactiver un compte est de changer son _shell_ dans le
fichier `/etc/passwd` et le remplacer par `/bin/false` par exemple. 


### Choisir un bon mot de passe

\index{passwd}

Le choix d'un bon mot de passe est primordial pour les mots de passe sensibles
comme les mots de passe des comptes administrateurs et donc, _root_ mais
également pour les mots de passe des comptes utilisateurs sans privilège
particulier. À ce sujet, il sera possible de mettre en place une _politique de
mot de passe_. 

En préambule, respecter les règles de bonne utilisation des mots de
passe est contraignant et difficile. C'est cependant un des éléments
principaux de la sécurité informatique.

Voici quelques règles habituelles :

- un mot de passe devrait être spécifique à un service informatique et ne
  devrait pas être réutilisé~: un service = un mot de passe spécifique et
  unique;

- un mot de passe est personnel et ne peut être donné à personne;

- un bon mot de passe est facile à retenir pour l'utilisateur qui le définit et
  difficile à trouver par une machine ou quelqu'un d'autre;

- un mot de passe ne devrait pas être basé sur des informations personnelle qui
  peuvent être facilement identifiées ou devinées;

- une bonne manière de faire est qu'il soit long, et ne puisse pas figurer dans
  une liste ou un dictionnaire;

- pour définir un bon mot de passe, par exemple accoler des mots en incorporant
  des majuscules, des minuscules, des chiffres et des caractères spéciaux  (car
  un mot de passe ne peut souvent pas contenir d'espace). Ou encore prendre les
  initiales des mots d'une phrase. C'est mieux également s'il mélange les
  langues;

    Par exemple si je pense aux trois mots « table », « chaise » et « manger »,
    un bon mot de passe pourrait être `tableCHAISEeat723`. 

    De part sa longueur ce mot de passe sera résistant — à l'heure où j'écris —
    à une force brute caractère par caractère mais aussi à une recherche mot par
    mot puisqu'il utilise deux langues et des chiffres. 

- pour un mot de passe de service, il est inutile qu'il soit retenu et il est tout à fait envisageable de le stocker dans un gestionnaire de mots de passe.

Nous verrons avec `PAM` qui est possible de définir des règles sur le format des mots de passe. 

\etwoc

### Le coin des commandes

\btwoc

#### `adduser` et `addgroup` 

\index{adduser}\index{useradd}

`adduser` et `addgroup` ajoutent des comptes utilisateurs ou des groupes au
système en fonction des options et du fichier de configuration
`/etc/adduser.conf`. Ces commandes choisissent un _uid_ et _gid_ conformes à la
charte _debian_, crée un répertoire personnel configuré en fonction du contenu
de `/etc/skel`, d'exécuter un script sur mesure. 

```bash
adduser <username>
```

Ajoute un compte utilisateur normal en lui associant le premier _uid_ disponible et un groupe — créé si nécessaire — du même nom que le compte utilisateur. 

- Attribue un masque de `002` au compte. 

- Crée un répertoire personnel. Par défaut tous les fichiers créés dans le répertoire personnel du compte utilisateur auront le bon groupe. 

- Copie les fichiers _skel_ dans le répertoire personnel du compte utilisateur. 

- Si le fichier   
    `/usr/local/sbin/adduser.local` existe, il sera exécuté. 

```bash
adduser --system <username>
```

Ajoute un compte utilisateur système.

- Par défaut les comptes utilisateurs systèmes sont placés dans le groupe `nogroup`.

- Un répertoire personnel est également créé. 

- Le shell par défaut sera `/usr/sbin/nologin`.

```bash 
adduser --group <groupname>
addgroup <groupname>
addgroup --system <groupname>
```

Ces trois commandes ajoutent un groupe au système sans utilisateur. La dernière
ajoute un groupe système qui diffère d'un groupe normal par son _gid_ qui n'est
pas dans le même intervalle. 

```bash
adduser <username> <groupname>
```

Ajoute le compte utilisateur _username_ au groupe _groupname_. 

#### `useradd` et `usergroup`

\index{usergroup}

Ces commandes sont les commandes bas niveau associées aux commandes précédentes.
Elles créent les compte en fonction des paramètre donnés en option sur la ligne
de commande. 

#### `userdel` et `deluser`

\index{userdel}\index{deluser}

Comme pour les autres commandes `userdel` est la commande de bas niveau pour
laquelle il est nécessaire de passer en arguments les options désirées tandis
que `deluser` est de plus haut niveau. 

`userdel` se contente de supprimer les entrées de `/etc/passwd` et `etc/shadow`,
elle ne supprime par exemple pas le groupe principal associé au compte
utilisateur. Par contre, elle ne supprimera pas le compte s'il a des processus
actifs. 

`deluser` et `delgroup` retirent des comptes utilisateur et des groupes du
système suivant les options et les informations contenues dans
`/etc/deluser.conf` et `/etc/adduser.conf`. 

```bash
deluser <username>
deluser --remove-home \
  --remove-all-files <username>
```

Retire un compte utilisateur normal.

- Par défaut le répertoire personnel n'est pas supprimé, c'est l'option
  `--remove-home` qui s'en charge. 

- L'option `--remove-all-files` recherche tous les fichiers que _username_
  possède et les supprime. 

- Il est possible de faire une sauvegarde des fichiers du compte utilisateurs
  _via_ l'option `--backup`. 

- Si le fichier `/usr/local/sbin/deluser.local` existe, il sera exécuté après la suppression du compte. Ceci permet un nettoyage supplémentaire. 

```bash
deluser --group <groupname>
delgroup <groupname>
```

Retire un groupe du système. 

```bash
deluser <username> <groupname>
```

Retire le compte utilisateur du groupe. 

#### `chage`

\index{chage}

La commande `chage` modifie le nombre de jour entre les changements de mot de
passe et la date du dernier changement. 

```bash
chage -M 30
```

Cette commande force le changement du mot de passe chaque mois.

#### `su`

\index{su}
\index{PAM}

La commande `su` — pour _substitute_ — permet d'exécuter une commande sous le compte d'un autre compte utilisateur. 

Sans argument, `su` exécute un _shell_ en _root_. 

L'option `-` (ou `-l` ou `--login`) lance le _shell_ comme un _shell_ de login: 

- efface les variables d'environnement exceptée `TERM`;
- initialises les variables d'environ-nement `HOME`, `SHELL`, `USER`, `LOGNAME` et `PATH`[^f_035_1];
- change de répertoire vers le répertoire cible;
- place le premier argument (`argv[0]`) du shell à `-` pour en faire un shell de login. 

`su` utilise PAM. 

```bash 
su -
su alice -c "ls -il"
```

[^f_035_1]: Ce point est source d'erreurs. Une exécution de `su` — sans le `-`
donc — ne charge pas le `PATH` de _root_ mais conserve celui de l'utilisateurice
sans privilège. Les répertoires `/sbin` et `/usr/sbin` n'en font par exemple pas
partie. 

\etwoc

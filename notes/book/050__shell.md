
\clearpage


# Le shell

\secttoc

\btwoc

\index{shell}\index{bash}

Le _shell_ est l'interprèteur de commandes. C'est le programme faisant
l'interface entre l'utilisateurice et la machine. C'est une interface en ligne
de commandes accessible via une console et permettant à l'utilisateurice de
lancer des commandes en entrant un texte. 

Le premier _shell_ `sh` est dû à Thompson Shell (1971) et a été remplacé par le
shell de Stephen Bourne (1977). Brian Fox réécrit ce shell et l'appelle _borne
again shell_ `bash` en 1988. C'est le shell le plus répandu bien qu'il en existe
d'autres: `csh`, `tcsh`, `ksh`, `zsh`… 

Dans la suite, nous nous intéressons à `bash`. 

`bash` peut être invoqué de différentes manières : 

- comme shell de login (_login shell_), si le premier caractère de son argument 0 est un tiret « `-` » ou s'il est invoqué avec l'option `--login`;
- comme shell interactif (_interactive shell_) si l'entrée et la sortie standards sont connectées à un shell ou s'il est invoqué avec l'option `-i`[^f_050_1]

Lorsque `bash` est lancé, il lit des fichiers d'initialisation :

- pour un shell de login (interactif ou non), le fichier `/etc/profile` — s'il existe — est exécuté ;
- pour un shell de login (interactif ou non), `bash` cherche dans l'ordre les fichiers `~/.bash_profile`, `~/.bash_login` et `~/.profile` et exécute le premier qu'il trouve (l'option `--noprofile` empêche ce comportement);
- pour un shell interactif (mais pas de login), `bash` exécute les fichiers `/etc/bash.bashrc` et `~/.bashrc` s'ils existent (l'option `--norc` empêche ce comportement);

Lors de l'_exit_, le fichier `~/.bash_logout` est exécuté s'il existe. 

[^f_050_1]: La variable `$-` peut être consultée pour voir les options passées au shell courant. 

\etwoc
\yaline

## Commandes `shell`

\btwoc

Une **commande simple** est une séquence optionnelle d'assignation de variables
suivie de mots et de redirections éventuelles. La commande se termine par un
opérateur de contrôle par exemple : `||`, `&&`, `&`, `;`, `|`, `|&`, `<new
line>`… Le premier mot est la commande a exécuter et les mots suivants sont
passés comme arguments à la commande. 

Un _pipe_ est une séquence de commandes séparées par le caractère _pipe_ « `|` ». 

```bash 
command1 | command1
```

la sortie standard de la première commande `command1` est connectée à l'entrée
standard de la deuxième commande `command2`. 

Si les caractères « `|&` » sont utilisés, la sortie standard **et** la sortie d'erreur standard entre dans le _pipe_. 

Un commande peut se terminer par « `&` ». Dans ce cas la commande est exécutée en tâche de fond (_background_) et le shell rend la main. 

Les commandes peuvent être séparées par un ET « `&&` » ou un OU « `||` ».

```bash
command1 && command2
```

- `command2` sera exécutée si et seulement si `command1` retourne _true_.

```bash
command1 || command2
```

- `command2` sera exécutée si `command1` retourne _false_.

Une **commande composée** est l'une des constructions suivantes (la liste n'est pas complète, cfr. `man bash`) : 

- `(list)` une liste de commandes entre parenthèses est exécutée dans un
  sous-shell ;

- `{ list; }` une liste de commandes exécutées dans le shell courant. Peut être
  vue comme un bloc ;

- `[[ expression ]]` évalue l'expression conditionnelle entre double crochets
  « `[[ ]]` » ;

  ```bash
  $ [[ -a /tmp/file ]] \
    && echo "File exist"
  ```
  - affiche "File exist"… si le fichier existe. 

- `for <name> in word… ; do <list>; done`

  ```bash
  $ for i in $(seq 10); do echo $i; done
  $ for i in one two three; do touch $i; done
  ```
  - affiche les valeurs de 1 à 10
  - crée les trois fichiers `one`, `two` et `three`

- `for (( expr1 ; expr2 ; expr3 )); do <list>; done`

  ```bash
  $ for (( i=0 ; i < 10 ; i++ )) ; do 
  >   echo $i; 
  > done
  ```
  - affiche les valeurs de 0 à 9

- `if <list>; then <list>; [else <list>;] fi`

  ```bash
  $ if [ $DISPLAY = ":0" ] ; then 
  >   echo "X is launch" ; 
  > fi
  ```
  - affiche "X is launch" si la variable `DISPLAY` vaut ":0"

`bash` permet de déclarer des **variables**.

```bash
name=value
```

Notons qu'il ne faut pas d'espace de part et d'autre du signe « `=` ».

Les **paramètres positionnels** sont les arguments du shell lors de son invocation : `$1`, `$2`… 

Les **paramètres spéciaux** sont repris dans le tableau suivant : 

\etwoc

-----------   -------------------------------------
 **Param**    **Description**
-----------   -------------------------------------
`$*`          Tous les paramètres positionnels. 
              Entre guillemets, représente un seul 
              mot : `$1_$2_$3`… (où `_` représente
              un espace) 

`$@`          Tous les paramètres positionnels.
              Entre guillemets, représente plusieurs
              mots : `$1 $2 $3`… 

`$#`          Nombre de paramètres (décimal)

`$?`          Code de retour de la dernière commande.

`$-`          Liste des options avec lesquelles le 
              shell a été invoqué. 

`$$`          _pid_ du shell.

`$!`          _pid_ de la dernière commande exécutée
              en arrière plan.

`$0`          Nom du script (ou du shell).
-----------   -------------------------------------

\yaline

## Les expansions

\btwoc

Toute une série d'**expansions** se font dans **cet ordre**: 

1. expansion des accolades (_brace expansion_), 
2. développement du tilde « `~` » (_tilde expansion_), 
3. remplacement des paramètres et variables (_parameter and variable expansion_), 
4. substitution de commandes (_command substitution_), 
5. évaluation arithmétique (_arithmetic expansion_), 
6. découpage des mots (_word splitting_) et 
7. développement des noms de fichiers (_pathname expansion_). 

**1** L'**expansion des accolades** permet la création de chaines quelconques
sous la forme d'un préambule facultatif, suivi de chaines entre accolades et
séparées par des virgules (sans espace blanc), le tout éventuellement suivi d'un
postambule. 

```bash
$ echo a{b,c,d}e
abe ace ade
```

Pratique par exemple pour créer plusieurs répertoires `mkdir /tmp/dir-{one,twe}`.

**2** Le **développement du tilde** de manière simplifiée se remplace par la
valeur de `HOME`. Tous les caractères précédent de premier slash « `/` » sont
considérés comme un _login_. 

```bash
$ echo ~
/home/alice
$ echo ~bob
/home/bob
```

**3** Le **remplacement des paramètres** s'associe au symbole dollar « `$` » et
aux accolades « `{}` ». Même si une variable peut s'écrire `$var` il est
conseillé de l'écrire `${var}`

`${parametre}` est remplacé par la valeur du paramètre. 

Le remplacement des paramètres est plus large que cette simple « valeur de variable » et les principaux remplacement sont repris dans le tableau suivant (cfr. _man pages_ pour la totalité) : 

\etwoc

---------------------------- ----------------------------------------------
**Remplacement                **Description**
de paramètres**
----------------------------  ----------------------------------------------
`${parametre:-mot}`           Donne la valeur du paramètre. Si le paramètre 
                              n'est pas défini, donne la valeur de _mot_

`${parametre:=mot}`           Donne la valeur du paramètre. Si le paramètre 
                              n'est pas défini, donne la valeur de _mot_ et
                              initialise le paramètre avec la valeur de _mot_

`${parametre:?mot}`           Donne la valeur du paramètre s'il existe sinon
                              affiche _mot_ comme message d'erreur.

`${parametre:+mot}`           Donne la valeur du _mot_ si le paramètre 
                              existe. Sinon ne retourne rien. 

`${#parametre}`               Donne la longueur de la valeur du paramètre. 

`${parametre:offset}`         Donne la valeur du paramètre jusqu'à _length_
`${parametre:offset:length}`  en commençant au caractère d'indice _offset_. 

`${!prefix*}`                 Donne toutes les variables commençant par 
`${!prefix@}`                 _prefix_.

`${parametre#mot}`            Supprime le schema correspondant au début. 
`${parametre##mot}`

`${parametre%mot}`            Supprime le schema correspondant à la fin. 
`${parametre%%mot}`

`${parametre/pattern/string}` Substitue _pattern_ par _string_.
----------------------------- ----------------------------------------------

\btwoc

**4** La **substitution de commandes** s'associe au symbole dollar « `$` » et
aux parenthèses « `()` »[^f_050_3]. 

`$(command)` est remplacé par le retour de la commande. 

La commande est exécutée dans un sous-shell. 

**5** L'**évaluation arithmétique** s'associe au symbole dollar « `$` » et à la
double paires de parenthèses « `(())` ». 

`$((expression))` est remplacé par la valeur de l'expression
arithmétique[^f_050_4]. 

**6** Le **découpage des mots** se fait à la suite des expansions précédentes et
tous les mots sont séparés en fonction de l'espace blanc (sauf si `IFS` a été
modifié). Pour que des valeurs ne soient pas séparées, il suffit de les entourer
de guillemets « `"` ».  

**7** Le **développement des noms de fichiers** consiste à rechercher dans les
mots les _jokers_ éventuels : `*`, `?` et `[]`. 

- `*` correspond à n'importe quelle chaine;
- `?` correspond à n'importe quel caractère. Un seul;
- `[…]` correspond à une suite de caractères, un intervalle ou à une classe de caractères. 

\etwoc
\yaline

## Les redirections

\btwoc

Par défaut l'entrée d'une commande, est l'entrée standard — le clavier — et les
sorties de la commande sont la sortie standard — le terminal. Ce comportement
peut être modifié en **redirigeant** ces canaux[^f_050_5]. 

`<mot` lit le fichier (ouvert en lecture) _mot_ comme entrée standard.

`>mot` redirige la sortie standard dans le fichier _mot_ (ouvert en écriture).

`2>mot` redirige la sortie d'erreur standard dans le fichier _mot_ (ouvert en écriture). 

L'utilisation de « `>>` » redirige une sortie en **ajout** dans le fichier. 

Il est possible de rediriger deux sorties dans un même fichier. Dans ce cas, la
première redirection redirige dans le fichier (`>mot`) et la seconde en faisant
référence à la redirection précédente avec l'esperluette (`2>&1`).

Pour rediriger la sortie standard et la sortie d'erreur standard dans le même fichier : 

```bash
$ ls > out 2>&1
```
\etwoc
\yaline

## Les tests

\btwoc

Les **expressions conditionnelles** sont utilisées pour tester les fichiers, les
chaines de caractères et l'arithmétique. Ces conditions sont testées avec la
commande « `[[ ]]` » ou « `[]` » ou encore la commande `test`. 

`bash` peut évaluer des **expressions arithmétiques** par exemple lors de
l'expansion arithmétique. Les opérateurs habituels — y compris ceux de
post|pré-inc|décrémentation ainsi que l'opérateur conditionnel — sont
disponibles. 

Par exemple : 

```bash
$ for (( i=0 ; i < 10 ; i++ )) ; do 
>   echo $i; 
> done
```

`bash` peut faire des tests sur les fichiers. Par exemple : 

- `-a file` teste l'existence du fichier;
- `-d file` teste l'existence et si c'est un répertoire;
- `-f file` teste l'existence et si c'est un fichier régulier;
- `-r file` teste l'existence et si le fichier est _readable_;
- `-w file` teste l'existence et si le fichier est _writable_;
- `-x file` teste l'existence et si le fichier est _excutable_;
- `file1 -nt file1` teste si _file1_ est plus récent (_newer than_) _file2_;

`bash` peut faire des tests sur les chaines de caractères. En voici quelques
uns : 

- `-n string` vrai si la longueur de la chaine est non nulle;
- `-z string` vrai si la longueur de la chaine est nulle;
- `string1 = string2` vrai si les deux chaines sont égales (`==` fonctionne
  aussi);
- `string1 != string2` vrai si les deux chaines sont différentes;
- `string1 > string2` vrai si les deux chaines sont triées lexicographiquement; 

Exemples :

```bash
$ if [ -d /tmp ] ; then 
>   echo "dir exist"; 
> fi
```

```bash
$ if test -d /tmp ; then 
>   echo "dir exist"; 
> fi
```

```bash
$ VAR=file.txt
$ if test -z ${VAR} ; then 
>   echo "foo";
> else
>   echo "bar";
> fi
```

\etwoc
\yaline

## L'historique

\btwoc

`bash` possède un historique des dernières commandes entrées. Par défaut cet historique mémorise 500 commandes. `echo ${HISTSIZE}` conserve cette valeur. 

Les flèches haut et bas permettent de naviguer dans cette historique. 

Au delà de cette utilisation basique, voici quelques usages de l'historique.

- `history` affiche l'historique. Associée à un `grep`, il est facile de retrouver une commande;
- `!n` réexécute la ligne `n`;
- `!-n` réexécute la ligne se référent à la commande courante - `n`;
- `!!` réexécute la dernière commande;
- `!string` réexécute la commande la plus récente commençant par la chaine `string`;
- `^string1^string2` répète la commande précédente en remplaçant `string1` par `string2`;

Il est également possible d'utiliser les évènements précédents autrement que de
simplement les exécuter. Là où `!!` execute la dernière commande, `!!:$`
représente le dernier mot de la dernière commande par exemple. 

Derrière ce `:` peuvent se trouver d'autres « désigneurs » de mots (_word designators_). En voici quelques uns : 

- `n` le `n`^e^ mot de la commande ;
- `^` le premier ;
- `$` le dernier ;
- `i-j` du `i`^e^ au `j`^e^ mot ;
- `*` tous les mots sauf la commande elle-même

Par exemple `!!:$`. Cette notation peut être raccourcie : `!!:$` par `!$` et
`!!:^` par `!^`, etc.

Encore derrière ces _word designators_ peuvent se trouver des modificateurs  (_modifiers_) également précédés de deux points « `:` ». En voici quelques uns : 

- `r` retire le suffixe et laisse le nom de base (_basename_) ;
- `p` affiche la commande mais ne l'exécute pas ;
- `s/old/new` remplace la première occurrence de `old` par `new` ;
- `gs/old/new` remplace toutes les occurrences de `old` par `new` ;


Voici quelques usages[^f_050_6] : 

```bash
$ ls /etc/apache2/apache2.conf
$ vim !!:$
```

```bash
$ xpdf /elsewhere/book.pdf
$ vim !!:s/pdf/md
```

```bash
$ cp pam.conf pam.bak
$ vi !^
```

```bash
$ cp ~/longname.conf \
  /really/a/very/long/path/other.conf
$ chmod go-rw !!:2
chmod go-rw \
  /really/a/very/long/path/other.conf
$ ls -l !cp:2
ls -l /really/a/very/long/path/other.conf
-rw------- 1 bob bob 0 fév 24 10:22 \
  /really/a/very/long/path/other.conf
```

\etwoc
\yaline

## Les commandes internes de `bash`

\btwoc

La plupart des commandes linux sont des binaires ou des scripts répartis dans
différents répertoires du _filesystem_. Il existe toutefois une série de
commandes qui sont des **commandes internes** à `bash`.  Ces commandes sont
directement interprètées pas le shell sans lancer un processus spécifique
exécutant la commande. 

En voici quelques unes, pour la liste complète, consulter le manuel `man bash` : 

- `source filename` lit et exécute les commandes du fichier _filename_ ;

  ```bash
  $ source ~/.bashrc
  ```

- `alias [name=value]` 
  - sans argument affiche la liste des _alias_ définis;
  - lorsqu'un argument est fourni, défini l'_alias_

  ```bash
  alias ll="ls -l"
  ```

- `bg jobid` place le _job_ _jobid_ (liste accessible par `jobs`) en tâche de
  fond (_background_) comme s'il avait été lancé avec `&` ;

  ```bash
  $ mycommand
  [Ctrl-z] 
  $ bg 1 
  ```

- `cd [<dir>]` change de répertoire (_change directory_) vers le répertoire
  _dir_ s'il existe, sinon, vers `HOME` ;

    `cd -` se rend dans le répertoire dans lequel l'_user_ se trouvait précédemment. Pratique pour faire un aller-retour. 

- `echo [-neE] [arg]` affiche _arg_. L'option `-n` supprime la passage à la
  ligne de fin, `-e` active l'interprètation des séquences d'échappement et `-E`
  la désactive. Cette option est l'option par défaut ;

- `exit [n]` quitte le shell avec la valeur de retour _n_ si elle est fournée, 0
  sinon ; 

- `ft jobid` place le _job_ en avant plan (_foreground_) ;

- `help [-dms] [pattern]` donne de l'aide sur une commande interne. L'option `-d` donne une description courte, `-m` donne l'aide dans un format _man page_ et `-s` donne un usage court ;  

- `history` affiche l'historique complet,  
  `history -c`  efface tout l'historique,  
  `history -d offset` efface l'entrée en position _offset_,  
  `history -d start-end` efface les entrées de _start_ à _end_,  

- `kill [-s sigspec | -n signum | -sigspec] [pid | jobspec]` envoie le signal _sigspec_ ou _signum_ au processus _pid_ ou _jobspec_,  
  `kill -l | -L` liste tous les signaux

- `pwd` affiche le réportoire courant ;

\etwoc
\yaline

## Script `shell`

\btwoc

Un script shell n'est qu'une suite de commandes shell placées dans un fichier. 

Un script peut porter l'extension `.sh` ou `.bash` mais elle n'est pas obligatoire. 

Un script shell commence par le nom du shell qui doit être utilisé. Cette première ligne, à l'allure suivante, s'appelle le **_shebang_** : 

```bash
#! /bin/bash
```

C'est une bonne pratique de faire immédiatement suivre ce _shebang_ d'un
commentaire précisant la fonction du script, l'auteur… et puis une fonction en
précisant l'usage. Par exemple :

```bash
usage() {
  echo -e "$(basename ${0}) options…"
  echo -e "…"
}
```

Une fonction se définit comme précédemment et s'appelle simplement en donnant son nom (sans les parenthèse).

```bash
usage
```

Un script shell se trouvant par exemple dans le fichier `yascript.sh` peut s'exécuter par : 

- `bash yascript.sh`[^f_050_2] ou;
- `./yascript.sh` si le fichier a été rendu exécutable au préalable. 

Quelques options sont pertinentes pour écrire un script plus sécurisé. 

`set -e` entraine que le script quitte après une commande en erreur, sans continuer. 

  ```bash
  badcommand
  echo "end"
  ```
  - comme la commande n'existe pas, _end_ ne sera pas affiché. 

`set -o pipefail` entraine que le code de retour d'un pipe ne soit pas celui de
la dernière commande mais celui de la dernière commande en erreur. 

Associée à l'option `-e`, permet au script de quitter dès qu'une erreur
survient… même dans un _pipe_. Par exemple : 

```bash
$ bash -c  "badcommand1 
> | echo foo; 
> badcommand2;  
> echo bar" 
foo
bash: badcommand1 : commande introu…
bash: badcommand2 : commande introu…
bar
```

```bash
$ bash -ceo pipefail  "badcommand1 
> | echo foo; 
> badcommand2;  
> echo bar" 
foo
bash: badcommand1 : commande introu…
```

`set -u` entraine que le script quitte s'il rencontre une variable qui n'existe pas. 

Le nom du script et ses paramètres sont accessibles par `$0` pour le nom du
script puis, `$1`, `$2`… pour les paramètres. 

L'accès à une variable devrait se faire entre accolades « `{}` » **et** entre
guillemets « `""` ».

Un script devrait | pourrait avoir l'allure suivante : 


```bash
#!/bin/bash
#
# Demo script. 
#
set -eo pipefail

readonly VAR=${1}

set -u 

usage() {
  echo -e "$(basename ${0}) options…"
  echo -e "…"
}

# param verification
if [ -z "${VAR}" ]; then
  echo "error message…"
  usage
  exit 1
fi

# now, do effective stuff
```

\etwoc
\yaline

[^f_050_2]: Lancé avec l'option `-x`, `bash` exécutera le script en mode _debug_. 

[^f_050_3]: La substitution de commande se fait également en utilisant les guillemets inverses (_back quotes_) « `` ` `` ». Il est conseillé de ne plus utiliser cette notation même si elle est encore fréquente dans la littérature. 

[^f_050_4]: Le format `$[expression]` est déprécié et va disparaitre dans les prochaines versions de `bash`. À oublier donc…

[^f_050_5]: Ces notes présentent la redirection des descripteurs de fichiers
(_file descriptor_) 0, 1 et 2, l'entrée standard, la sortie standard et la
sortie d'erreur standard. Il est possible de rediriger d'autres descripteurs de
fichier. Voir `man bash`.

[^f_050_6]: Certains exemples sont issus de [thegeekstuff](https://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history/).

## Astuces et raccourcis `bash`

\btwoc

- `[Esc] t` intervertit les deux derniers paramètres d'une commande. 

  Pratique lors de l'écriture de `systemctl apache2 start` au lieu de `systemctl
  start apache2`. 

- Le paquet `bash-completion` propose une autocomplétion intelligente pour
  toutes les commandes. À intalle. 

- `[Ctrl-r]<pattern>` affiche la première commande correspondant au schéma
  (_pattern_) disponible dans l'historique.

- `[Esc] .` écrit la dernière commande (sans le réexécuter immédiatement).

\etwoc
\yaline

## Le coin des commandes

\btwoc

### head

\index{head}

`head [-n]` affiche les _n_ premières lignes d'un fichier. Par défaut, _n_ vaut 10. 

### tail

\index{tail}

`tail [-n]` affiche les _n_ dernières lignes d'un fichier. Par défaut, _n_ vaut 10. D'autres options sont disponibles (cfr. `man tail`)

- l'option `-f` affiche les dernières lignes en continu, pratique pour des fichiers de logs ;

```bash
# tail -f /val/log/syslog
```

### grep

\index{grep}

`grep [options] patterns [file…]` recherche chaque _patterns_ dans chaque fichier _file_. `grep` peut lire sur l'entrée standard `-`. Voici quelques options (pour d'autres options, `man grep`) :

- `-i` _ignore case_ ;
- `-v` inverse la sélection, donne les lignes qui ne correspondent pas au _pattern_ ;
- `-c` n'affiche pas les lignes mais les comptes (_count_) ;
- `-m num` arrête de chercher après _num_ lignes correspondant au _pattern_ ; 




\etwoc
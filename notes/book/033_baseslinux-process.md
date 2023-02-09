\clearpage

## Les processus

\btwoc

Un processus est un programme en cours d'exécution. Un programme pouvant être
exécuté plusieurs fois en même temps, il est possible d'avoir plusieurs
instances du même programme au même moment. 

Un processus se caractérise par : 

- un _pid_, identifiant de processus (_process id_)  ;
- un _ppid_, identifiant du processus parent (_parent process id_) ;
- un _uid_, l'identifiant de _user_ qui a lancé le processus ;
- un _guid_, l'identifiant du groupe du _user_ qui a lancé le processus ;
- un _euid_, l'identifiant de _user_ « effectif » (_effective user id_) qui a lancé le processus_;
- un _egid_, l'identifiant du groupe du _user_ qui a lancé le processus ;
- un _état_, état (_state_) du processus (voir ci-dessous);
- une _priorité_

_gid_ est un alias de _egid_. _euid_ est un alias de _uid_. 

Un processus n'est pas toujours en cours d'éxécution (_running_) puisqu'il y a
plus de processus que de CPU. La plupart des processus sont soit en cours
d'exécution (**_run_**), soit prêt (**_ready_**), soit en attente (**_wait_**). 

À sa création, un processus est placé dans le statut prêt (_ready_) et attend
d'être choisi par le _scheduler_. 

Un processus _running_ devient « en attente » (_waiting_) lorsqu'il attend des
ressources qui ne sont pas encore disponibles ; entrées-sorties ou tout autre
évènement. Il se met en seul en attente ou le _kernel_ s'en charge. Lorsque les
ressources attendues seront disponibles, le _scheduler_ mettra le processus dans
l'état prêt (_ready_)._  


\etwoc
\index{state}

Voici les différents états d'un processus tels que présentés dans la page de
manuel :

----------  -------------------------------------------------
 **s**      **État du processus**
 (_state_)
----------  -------------------------------------------------
`R`         _running_ or _runnable_, le processus est en  
            cours d'exécution (_running_) ou est prêt à 
            l'être (_runnable_) (il ne lui manque que le CPU).

`S`         _interruptible sleep_, le processus est en 
            attente (_wait_) et peut être interrompu.

`D`         _uninterruptible sleep_, le processus est en 
            attente (_wait_) et ne peut pas être interrompu 
            (probablement en I/O).

`I`         _idle kernel thread_, le processus est _idle_ ce 
            qui signifie qu'il est à la fois 
            _uninterruptible sleep_ et _no load_. 
            Ce processus est en attente de travail et ne 
            participe pas au calcul de la charge du système
            [^f_033_1]. 

`T`         _stopped by job control signal_, le processus
            est stoppé (par un signal `SIGSTOP`). 
            Il redeviendra prêt lorsqu'il recevra un signal 
            `SIGCONT`.

`t`         _stopped by debugger_, le processus est stoppé par 
            un signal reçu d'un débogueur (_debugger_).

`X`         _dead_, le processus est terminé. Il a terminé son
            `run()`.   
            Cet état n'apparait normalement pas car un 
            processus terminé est détruit et n'apparait plus.

`Z`         _zombie_, le processus est terminé mais n'a pas 
            été récupéré par son parent ni par _init_.
----------  -------------------------------------------------

\btwoc

Le processus parent de tous les processus est **init** de _pid_ 1.

Un processus peut être lancé en tâche de fond (_background_) en entrant la
commande suivie d'une esperluette `&`.

\index{esperluette}

Un processus peut-être stoppé en lui envoyant le signal `SIGSTOP` au moyen, par exemple, d'un `Ctrl-Z`. Il peut alors être relancé — continué — en le plaçant en tâche de fond (_background_) _via_ `bg <job number>` par exemple.

[^f_033_1]: Voir ce [kernel commit](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=80ed87c8a9ca0cad7ca66cf3bbdfb17559a66dcf) (_consulté le 10 fév. 2021_) 

\etwoc
\yaline

### Le coin des commandes

\btwoc

#### `ps`

\index{ps}
\label{command:ps}

`ps` liste les processus du système. Sans option[^f_033_2], liste les processus associés à la console (`TTY`) courant.

[^f_033_2]: Cette commande est un peu particulière car elle accepte le « style UNIX » où les options peuvent être groupées et précédées d'un tiret (_dash_) « `-` », le « style BSD » où les options peuvent être groupées et ne doivent pas être précédées d'un tiret et le « style GNU » où les options sont au format long et sont précédées de deux tirets. 

- `u` liste les processus appartenant au compte utilisateur courant;
- `a` liste tous les processus associés à un terminal;
- `ax`, `-e`, `-A` liste tous les processus; 
- `faux` liste tout des processus sans restreindre aux processus de _user_
  (`a`), sans restreindre aux processus sans TTY (`x`), les sélectionne par
  _uid_ (`u`) et les affiche dans le format _full_ (`f`). Ce format
  liste les processus sous forme d'arbre. 

#### `kill`

\index{kill}
\index{signaux}

`kill <pid>` envoie un signal au processus _pid_. 

Même si les signaux les plus courants sont les signaux de mort ou de demandes de
mort, il en existe d'autres. La liste s'obtient par l'option `-L` ou `-l`. 

```bash
kill -L
```

Par défaut, _kill_ envoie le signal `TERM` qui demande au processus de se terminer ; une demande de mort. Pour insister et envoyer le signal de mort `SIGKILL`, il faut le préciser: 

```bash
kill -SIGKILL <pid>
kill -KILL <pid>
kill -9 <pid>
```
\index{Ctrl-Z}

Si vous avez stoppé un processus _via_ `Ctrl-Z` (ou en lui envoyant le signal `STOP` par `kill -STOP <ipd>`), vous pouvez lui envoyez le signal `CONT` par un

```bash
kill -CONT <pid>
```
\index{SIGHUP}

Le signal `SIGHUP` est aussi intéressant car il demande à certaines applications
de relire leur fichier de configuration. 

Attention, le _pid_ peut valoir **-1**, dans ce cas, il signifie : tous les processus exceptés _init_ et le processus _kill_ lancé. En ce sens, la commande suivante est déconseillée : 

```bash
kill -9 -1
```


#### `top` et `htop`

\index{top}\index{htop}

`top` et sa version _++_ `htop` montre la liste des processus.  
`q` pour quitter.  

`[h]top` supporte la navigation avec les touches `Up`, `Down`, `Left`, `Right`, `PgUp`, `PgDn` et les commandes suivantes (extrait, cfr. _man htop_) :

- `Space` _tag_ ou _untag_ un processus;
- `U` _untag_ tous les processus;
- `l` affiche les fichiers ouvert par ce processus (nécessite `lsof`);
- `t` _tree view_ vue en arbre;
- `k` envoie un signal sélectionnable dans un menu;
- `u` affiche uniquement les processus d'un _user_; 
- `M` trie par usage mémoire;
- `P` trie par usage processeur;
- `F` sélectionne une ligne et suit (_follow_) le processus si la liste est retriée par exemple;

Par défaut, `htop` montre

- `PID` le _process id_ ;
- `USER` le _user_;
- `PRI` la priorité du processus (habituellement, la valeur de _nice_ augmentée de 20);
- `NI` la valeur de _nice_,
- `VIRT` la taille de la mémoire virtuelle (_virtual_) du processus;
- `RES` la taille résident en mémoire du processus (text + data + stack);
- `SHR` la taille des pages partagées en mémoire (_shared_);
- `S` le statut de processus _state_;
- `CPU%` le pourcentage CPU utilisé;
- `MEM%` le pourcentage mémoire;
- `TIME+` le temp en click horloge utilisé par le processus;
- `Command` la commande complète du processus.

#### `pstree`

\index{pstree} 

`pstree` montre des processus sous forme d'arbre.

```bash
pstree
pstree <user>
pstree -ph 
pstree <user> -ph
pstree <pid> -ph
```

- `-p` montre les _pid_ et désactive la vue compacte;
- `-h` surligne le procesus courant et ses parents

#### `pidof`

\index{pidof}

`pidof` donne le _pid_ — ou les s'il y a plusieurs instance du même programme —
du processus dont le nom est donné.

```bash
pidof init
pidof bash
```

\etwoc
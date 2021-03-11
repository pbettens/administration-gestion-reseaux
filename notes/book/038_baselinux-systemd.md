\clearpage

## Démarrage du système, `systemd`

\btwoc
\index{grub2}

Au démarrage du système, les étapes suivantes sont exécutées :

- à la mise sous tension, le système charge le _moniteur_, anciennement `BIOS`
  et actuellement `uefi` qui fait la vérifications matérielles (CPU, mémoires,
  périphériques…) et initie le _bootstrapping_.

    Ce programme peut être interrompu par configurer le _boot_ du système et
    certaines configurations matérielles ;

    _uefi_ au contraire de _BIOS_ peut résider sur une partition du disque, le
    firmware _uefi_ peut donc lire et charger le code se trouvant sur une petite
    partition. C'est là que se trouve le code _uefi_ de _grub_. 

- le moniteur est configuré pour chercher du code sur certains périphériques
  dans un certains ordre (un disque dur, une clé USB…) ;

- chargement du code `grub2`[^f_038_1].

    Il est possible d'interrompre _grub_ et de passer des options au noyau sur
    lequel le système va booter. 

- si c'est le choix de linux qui est fait dans _grub_, chargement du noyau
  sélectionné — éventuellement en deux étapes — et montage de la partition
  système `/` ;

- chargement de _systemd_, le « super dæmon » dont la responsabilité est de
  lancer tous les services prévus dans l'ordre qui va bien. C'est-a-dire en
  gérant les dépendances et l'ordre suggéré par _root_. 

    Dans un système fonctionnant avec _systemd_, le processus de _pid_ `0`
    appelé `init` est `systemd`.

    Dans un système fonctionnant avec _Sys V_, le processus de _pid_ `0` appelé
    `init` est (était) le script exécutant le fichier `/etc/inittab` et lançant
    ensuite l'exécution des scripts se trouvant dans `/etc/rci.d` où `i` est le
    _runlevel_ choisi au _boot_ de la machine. _Sys V_ a été abandonné au profit
    de _systemd_ par debian avec _Jessie_.



_systemd_ est un gestionnaire de système et de services pour linux. _systemd_
est compatible avec les scripts d'initialisation _Sys V_ et remplace _sysvinit_.
Un des principaux avantages avancés pour _systemd_ est qu'il permet la
parallélisation.

Il s'organise par **unités** (_unit_) ; les services (_.services_), les points
de montage (_.mount_), les périphériques (_.device_)… Ces unités sont définies
dans un fichier. Ces fichiers se trouvent dans `/etc/systemd/`,
`/lib/systemd/system/`… (cfr. `man systemd.unit`).

Les unités sont rassemblées pour former des **cibles** (_target_) ayant un
sens ; par exemple la cible `graphical.target` ou `sound.target`. La
configuration de ces cibles comprend ce dont elles ont besoin pour pouvoir être
exécutées.

Au niveau de la sécurité des processus, _systemd_ place chaque service dans un
groupe de contrôle (_cgroup_) dédié au service. Ceci permet une bonne /
meilleure isolation du système. 

### Le coin de la commande

\index{systemctl}

#### `systemctl`

`systemctl` est la commande principale pour contrôler et gérer l'état du
système, lancer, stopper des services. 

```bash
# systemctl [options] command [unit]
```

- `status [pattern | pid]` sans option, affiche l'état du système sinon affiche l'état de la cible, de l'unité… ;

_Exemples sans les retours de commandes_  

```bash
$ systemctl status
$ systemctl status bind9.service
$ systemctl status 9823
$ systemctl /dev/sda
```

_Exemple de sortie du status de bind9_  

```bash
$ systemctl status bind9
● bind9.service - BIND Domain Name Server
Loaded: loaded (/lib/systemd/system/\ 
bind9.service;\
enabled; vendor preset: enabled)
Active: active (running) since Tue \
    2021-02-23 08:02:16 CET; 3 days ago
    Docs: man:named(8)
Main PID: 6792 (named)
Tasks: 7 (limit: 4915)
Memory: 16.4M
CGroup: /system.slice/bind9.service
        └─6792 /usr/sbin/named -u bind
```

- `--failed` affiche les services qui ont échoués ; 

- `list-units [pattern]` affiche les unités que _systemd_ a actuellement en
  mémoire[^f_038_2] ;

    ```bash
    $ systemctl list-units \
        --state=running
    ```

- `list-unit-files` affiche les fichiers correspondants aux différents
  services ; 

- `start [pattern]`  
    `stop [pattern]`  
    `reload [pattern]`  
    `restart [pattern]`  
    `reload-or-restart [pattern]` lance, stoppe, demande de relire le fichier de
    configuration (_reload_), stoppe et relance ou reloade ou restart le service
    correspondant au _pattern_.



- `enable <unit>`  
    `disable <unit>`  
    `is-enabled <unit>`  
    rend l'unité active ou inactive ou dit si elle l'est, au _boot_ du système ;  

- `halt`  
    `poweroff`  
    arrête le système.   
    
    La différence entre _halt_ et _poweroff_ est que la seconde mettra le système hors tension après l'arrêt du système. 
    
    _halt_ et _poweroff_ sont des appels aux cibles `halt.target` ou
    `poweroff.tarqet` ;

- `reboot` _reboote_ le système ;

La suite dans la page de manuel `man systemctl`.

[^f_038_1]: Les _bootloader_ linux sont, dans l'ordre d'apparition et de
disparition : _LILO_, _Grub_ et _Grub2_.

[^f_038_2]: De même, il existe des commandes pour lister les _sockets_
(`list-sockets`) et les _timers_ (`list-timers`) dont nous ne parlerons pas dans
ces notes. 

\etwoc
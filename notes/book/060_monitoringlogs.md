\clearpage

# Monitoring et fichiers de logs

\secttoc
\index{log}
\index{last}

\btwoc

Un élément de sécurité système est la surveillance (_monitoring_) du réseau. Il
est important de surveiller sa machine ou le réseau pour détecter les activités
non autorisées et les trous de sécurité. 

On peut chercher: 

- les permissions qui auraient été modifiées sur certains fichiers[^f060_13];
- les derniers _users_ connectés (Quand ? D'où ?) _via_ la commande `last`
- … consulter les fichiers de logs. 

[^f060_13]: Particulièrement des fichiers sensibles comme `/etc/passwd`, ceux lancés par `cron`, des exécutables… ce qui devient compliqué à « faire à la main ».

\etwoc
\yaline

## Les fichiers de logs

\btwoc

Le système et les services tournant fournissent des informations au système sur leur fonctionnement ; les activités et les évènements qu'ils produisent. Ces informations incluent, outre les activités normales, les avertissement, les erreurs, les performances et toutes informations utiles pour le dépannage et la surveillance du système. 

Ces fichiers de _logs_, en français, les *fichiers journaux*, se trouvent dans `/var/log`. 

Le fichier principal est `/var/log/syslog` et beaucoup de services offrent le ou les leurs. 

Par exemple `/var/log/apache2/access` `.log` et `/var/log/apache2/errors.log` pour le service _apache2_. 

Une manière simple de consulter ces fichiers sont les utilitaires `cat`, `grep` (avec l'option `-r`, voir \vref{command:grep}) et `tail` (voir \vref{command:tail}). 

Dans un environnement plus professionnel, il sera nécessaire de _faire remonter_ les logs et de les centraliser à un endroit où ils pourront être lus. En effet, une équipe d'administrateurs et administratrices système ne va pas les consulter individuellement sur chaque machine qu'elle gère. Les logs peuvent être centralisés. 

\index{telegraf} \index{InfluxDB} \index{Grafana}

Une manière de faire remonter ces logs est d'utiliser la pile `telegraf` - `InfluxDB` - `Grafana` : 

- `telegraf` est un utilitaire capable de reporter des métriques dans une base de données _InfluxDB_;
- `InfluxDB` est une base de données conçues pour enregistrer des métriques, des évènements systèmes… Elle peut recevoir les valeurs de plusieurs agent `telegraf`, éventuellement, répartis sur plusieurs machines; 
- `Grafana` est un service web capable d'afficher des données en provenance d'une base de données _InfluxDB_ par exemple.

### Pour aller plus loin

L'étape suivante est l'étude des IDS (_Intrusion Detection Système_), systèmes de détection d'intrusion… mais c'est une autre histoire… 

\etwoc
\yaline

## Le coin des commandes

\btwoc

### who

\index{who}

`who` affiche les _users_ actuellement connectés à la machine ; à partir de quel terminal, depuis quand et avec quelle IP. 

### ps

`ps` liste les processus en cours d'exécution (voir \vpageref{command:ps})

### last 

\index{last}

`last` donne les dernières connexions au système (la commande utilise _wtmp_ qui logue ces connexions dans `/var/log/wtmp` _binary file_). _wtmp_ logue les connexions au système avec l'utilisateurice, l'IP, la date et les moments — début, fin — de la connexion. 

```bash
last 
last root | grep -v console
```

\etwoc

Extrait d'une commande `last`

```
alice    pts/6        192.168.210.8    Tue Jan 17 20:18 - 21:45  (01:27)
```
\btwoc

### cat 

\index{cat}
\index{bat}

`cat` affiche le contenu d'un fichier sur la sortie standard. Cette commande intervient régulièrement dans un _pipe_. On peut lui préférer la commande `bat` qui est un _clone_ de la commande `cat` avec coloration syntaxique et intégration de _git_ (option `-d`). 

\etwoc



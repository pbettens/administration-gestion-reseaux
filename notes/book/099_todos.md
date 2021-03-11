
# Todos

_En vrac, les sujets à aborder_

- les pages de manuels `man`

- boot (bios / ueefi, grub lilo… systemd)

- su / sudo

- ssh 
   - échange de clé



- vérifier dns, db.root -> /usr/share/dns/root.hints

- ajouter commande pour retirer les commentaires dans un fichier de conf

```bash
grep -v "^[#|$]" /etc/apache2/apache2.conf | grep .
```

si dhcp, modifier /resolv.conf ne sert à rien puisqu'il est réécrit. mieux: 

- installer le paquet _resolvconf_

   ```bash
   # apt install resolvconf
   ```

- éditer le fichier `head` qui sera lu *avant* `/resolv.conf` et ajouter le nameserver local par exemple. 

   ```bash
   # cat /etc/resolvconf/resorv.conf.d/head
   nameserver 127.0.0.1
   ```


parler de resolv.conf dans dNS

le résolveur ou la configuration du client

nameserveur <address>
max 3 serveurs 
domain <name>
search <name>

le coup du head
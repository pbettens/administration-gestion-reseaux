# TDs

Les travaux dirigés, _aka_ les exercices. 

## Quelques remarques

_Deux, trois petites choses qui n'apparaissent pas dans les tds._

### Routage 

Les _petits trucs_ à ne pas oublier : 

- ne pas oublier de faire un _down_ de `dhcpclient` si l'on veut faire une configuration manuelle des routes; 

- `ifconfig` et `route` sont remplacées par `ip a` et `ip r` et les paramètres sont « comme d'habitude » ; 

- positionner `/proc/sys/net/ipv4/ip_forward` à `1` si l'on veut du _forwarding_ **et** configurer `iptables` pour faire du _nat_ (_network address translation_)

    ```bash
    iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
    ```

    Pour la signification des paramètres, consulter la page de manuel (rechercher nat et masquerading). 

    **En bref**  

    - `-t nat` signifie que l'on fait du nat ;
    - `-A POSTROUTING` signifie que l'on marque les paquets sortants initiant une nouvelle connexion

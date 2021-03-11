#TD1 
Pour le routage:

- ne pas oublier de faire un down de dhcpclient
- ifconfig et route comme d'hab
- /proc/sys/net/ipv4/ip_forward à 1
- ET le nat via iptables

    iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE

Pour la signification des paramètres, consulter la page de manuel (rechercher
nat et masquerading). 

**En bref**  

-t nat signifie que l'on fait du nat[network translation address] 
-A POSTROUTING signifie que l'on narque les paquets sortants initiants une
nouvelle connexion *À vérifier* 

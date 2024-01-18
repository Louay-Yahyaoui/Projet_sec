# Projet securité

Louay Yahyaoui

Hachem Saihi

Zoghlami Mohamed Ali

# Partie 1 : Authentification avec OpenLDAP, SSH, Apache, OpenVPN

## Section 1 : Configuration d'OpenLDAP:

Notre adresse IP est 192.168.56.105 vérifiée par:

```bash
ifconfig
```

On doit configurer le hostname et le fqdn de la machine

```bash
sudo nano /etc/hostname
```

On modifie ensuite le fichier hosts

```bash
sudo nano /etc/hosts
```

On ajoute la ligne

```bash
127.0.1.1 server.insat.tn
```

On effectue un reboot pour sauvegarder les changements

```bash
sudo reboot
```

On obtient :

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled.png)

On Installe le paquet OpenLDAP:

```bash
sudo apt install slapd ldap-utils
```

On lance le service OpenLDAP

```bash
sudo systemctl slapd start
```

![openldap.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/openldap.png)

On Installe apache web server

```bash
sudo apt install apache2 php php-cgi libapache2-mod-php php-mbstring php-common 
php-pear -y
```

On installe LDAP Account Manager(LAM)

```bash
sudo apt -y install ldap-account-manager
```

On active  PHP-CGI PHP extension

```bash
sudo a2enconf php*-cgi
```

On configure LDAP Account manager

On accéde à 192.168.56.105/lam

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%201.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%202.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%203.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%204.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%205.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%206.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%207.png)

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%208.png)

![users.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/users.png)

![users and groups.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/users_and_groups.png)

Pour tester LDAP Directory on éxecute la commande suivante :

```bash
ldapsearch -x -LLL -H ldap:/// -b dc=insat,dc=tn '(uid=it1)'

```

On ajoute les certificats:

![signed.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/signed.png)

![moving certif.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/moving_certif.png)

On crée le fichier ssl_ldap.ldif

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%209.png)

On execute cette commande pour ajouter les certificats

![activation ssl.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/activation_ssl.png)

On modifie ensuite le fichier /etc/default/slapd

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2010.png)

On modifie aussi /etc/ldap/ldap.conf

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2011.png)

Vérification ldaps

![ldaps.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/ldaps.png)

Machine Cliente:

On modifie /etc/hosts dans la machine cliente: 

On ajoute la ligne:

```bash
192.168.56.105 server.insat.tn
```

 

On installe les packages de LDAP

```bash
sudo apt install libnss-ldap libpam-ldap ldap-utils nscd -y
```

On configure /etc/nsswitch.conf

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2012.png)

On ajoute cette ligne dans /etc/pam.d/common-session

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2013.png)

On peut faire des requêtes de recherche à partir de la machine cliente

![testing client.gif](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/testing_client.gif)

login client:

![client login.gif](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/client_login.gif)

Pour tester LDAPS:

Maintenant, parlons des avantages de LDAPS :

1. **Chiffrement des Communications :**
LDAPS utilise SSL/TLS pour chiffrer les communications entre le client et le serveur LDAP, assurant ainsi la confidentialité des données pendant le transit.
2. **Authentification Sécurisée :**
LDAPS offre une authentification sécurisée, ce qui signifie que les informations d'identification sont transmises de manière sécurisée, réduisant le risque de vol d'identifiants.
3. **Intégrité des Données :**
La couche SSL/TLS garantit l'intégrité des données échangées entre le client et le serveur. Cela empêche toute altération non autorisée des informations pendant le transit.
4. **Protection contre l'Écoute Passive :**
LDAPS protège contre l'écoute passive, où un attaquant pourrait intercepter le trafic réseau pour récupérer des informations sensibles.
5. **Conformité aux Normes de Sécurité :**
L'utilisation de LDAPS est conforme aux meilleures pratiques de sécurité et aux normes de l'industrie pour sécuriser les communications LDAP.
    
    ![ldaps.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/ldaps%201.png)
    

## Section 3 : Intégration d'Apache

On installe les packages nécessaires

```bash
sudo apt-get install apache2-utils
sudo apt-get install libapache2-mod-ldap-userdir
```

On protège le chemin “/protected”

On configure le fichier `/etc/apache2/sites-available/000-default.conf`

![apacheldapconfig.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/apacheldapconfig.png)

test du chemin: On est amenée à saisir le login d’un utilisateur du group IT:

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2014.png)

## Section 4 : Mise en place d'OpenVPN

Pour l’installation et la configuration du serveur OpenVPN on a utilisé un script d’installation

Télécharger et exécuter le script:

```bash
wget https://git.io/vpn -O openvpn-install.sh && sudo bash openvpn-install.sh
```

![Screenshot from 2024-01-16 15-29-55.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-16_15-29-55.png)

![Screenshot from 2024-01-16 14-33-45.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-16_14-33-45.png)

![Screenshot from 2024-01-16 14-40-41.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-16_14-40-41.png)

on pointe server.conf sur les fichiers config client et on ne nécessite pas la certification client

et on supprime les lignes du groupe et username pour demander l’authentification:

![Screenshot from 2024-01-16 14-45-49.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-16_14-45-49.png)

fichier client.ovpn:

![Screenshot from 2024-01-16 14-52-58.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-16_14-52-58.png)

fichier config LDAP:

![Screenshot from 2024-01-17 10-06-15.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-17_10-06-15.png)

![Screenshot from 2024-01-17 10-06-24.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-17_10-06-24.png)

vpn coté serverur:

![port 1195.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/port_1195.png)

vpn coté client: (avec login incorrect):

![Screenshot from 2024-01-17 10-44-19.png](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Screenshot_from_2024-01-17_10-44-19.png)

On redemarre le service OpenVPN

```bash
systemctl restart openvpn
```

Si l’authentification est incorrecte, le serveur affiche:

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2015.png)

# Partie 2 : Gestion des Services Réseau avec DNS

## Section 1 : Configuration d'un serveur DNS

On nécessite le package bind9 pour la configuration dns:

```bash
sudo apt install bind9
```

We added the DNS server ip to /etc/resolv.conf

![etcresolvconf.PNG](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/etcresolvconf.png)

On vérifie le fichier named.conf.options:

```bash
sudo nano named.conf.options
```

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2016.png)

On ajoute la zone [db.insat.tn](http://db.insat.tn) à named.conf.local:

![Untitled](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/Untitled%2017.png)

On crée le fichier [db.insat.tn](http://db.insat.tn) où on définie les hosts openvpn, ldap et apache.

```bash
sudo nano /etc/bind/zones/db.insat.tn
```

![zone.PNG](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/zone.png)

## Section 2 : **Validation et Test**

Launch the DNS server 

```bash
/usr/sbin/named -g -c /etc/bind/named.conf -u bind
```

![dns.PNG](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/dns.png)

On s’assure que les noms de domaine associés aux services sont correctement
résolus.

![dnscheck.PNG](Projet%20securite%CC%81%20192ee759398a4dfe9aa192b056277caf/dnscheck.png)
# TP 3 Réseaux

# Préparation de l'environnement


## Vm client1.tp3

| Point                                                   | Méthode pour prouver                 |
| ------------------------------------------------------- | ------------------------------------ |
| Carte NAT désactivée                                    | ![](https://i.imgur.com/VDDjd3F.png) |
| Serveur SSH fonctionnel qui écoute sur le port 7777/tcp | ![](https://i.imgur.com/e1dsq2B.png) |
| Pare-feu activé et configuré                            | ![](https://i.imgur.com/27Rm0Gh.png) |
| Nom configuré                                           | ![](https://i.imgur.com/L5JFOXm.png) |
| Fichiers /etc/hosts de toutes les machines configurés   | ![](https://i.imgur.com/N96uTkW.png) |
| Réseaux et adressage des machines                       | ![](https://i.imgur.com/MvU4Kcy.png) |


## Vm server

| Point                                                   | Méthode pour prouver                 |
| ------------------------------------------------------- | ------------------------------------ |
| Carte NAT désactivée                                    | ![](https://i.imgur.com/qMI0x4s.png) |
| Serveur SSH fonctionnel qui écoute sur le port 7777/tcp | ![](https://i.imgur.com/quFIBrl.png) |
| Pare-feu activé et configuré                            | ![](https://i.imgur.com/gFptuBR.png) |
| Nom configuré                                           | ![](https://i.imgur.com/q4Fkckw.png) |
| Fichiers /etc/hosts de toutes les machines configurés   | ![](https://i.imgur.com/SI3a2OT.png) |
| Réseaux et adressage des machines                       | ![](https://i.imgur.com/5nVBD2z.png) |

## Vm router

| Point                                                   | Méthode pour prouver                 |
| ------------------------------------------------------- | ------------------------------------ |
| Carte NAT désactivée                                    | ![](https://i.imgur.com/wDBlFT9.png) |
| Serveur SSH fonctionnel qui écoute sur le port 7777/tcp | ![](https://i.imgur.com/IK3vfqS.png) |
| Pare-feu activé et configuré                            | ![](https://i.imgur.com/EJgMXR2.png) |
| Nom configuré                                           | ![](https://i.imgur.com/GZyeMB8.png) |
| Fichiers /etc/hosts de toutes les machines configurés   | ![](https://i.imgur.com/luzsJnO.png) |
| Réseaux et adressage des machines                       | ![](https://i.imgur.com/gsJZCZs.png) |


## Ping client1 <> router
    
   client1, ping router 
![](https://i.imgur.com/02sy0xZ.jpg)

router, ping client 

![](https://i.imgur.com/HSvkDb3.jpg)

## Server1 <> router

 server1, ping router
 
 ![](https://i.imgur.com/efO9RtY.png)

 router ping server1
![](https://i.imgur.com/aD1zRXS.png)


# I. Mise en place du routage

## 1. Configuration du routage sur router

![](https://i.imgur.com/QtLTorJ.png)

## 2. Ajouter les routes statiques

![](https://i.imgur.com/qSJN5pf.png)

![](https://i.imgur.com/Kdq4hji.png)


## 3. Comprendre le routage



|                                         | MAC src           | MAC dst           | IP src | IP dst  |
| --------------------------------------- | -------           | -------           | ------ | ------  |
| Dans net1 (trame qui entre dans router) |(0a:00:27:00:00:12)|(08:00:27:1a:a7:8b)|10.3.1.1|10.3.1.2 |
| Dans net2 (trame qui sort de router)    |(0a:00:27:00:00:13)|(08:00:27:5d:2f:e7)|10.3.2.1|10.3.2.11|


# II. ARP
   ## 1. Tables ARP
   
```
[dmathieu@client1 ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:e1:80:3d STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
[dmathieu@client1 ~]$   

```

On peut voir sur la première ligne que nous avons l'IP du router sur notre réseau, son interface et sa MAC.
Sur la deuxième ligne on voit la première IP disponible sur notre réseau, son interface et sa MAC.

```
[dmathieu@router ~]$ ip neigh show
10.3.2.11 dev enp0s9 lladdr 08:00:27:5d:2f:e7 STALE
10.3.1.2 dev enp0s8 lladdr 08:00:27:1a:a7:8b STALE
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.1.11 dev enp0s8  FAILED
```

On peut voir sur la première ligne que nous avons l'IP de client1, son interface et sa MAC.
Sur la deuxième ligne on voit la première IP disponible sur le réseau de client1, son interface et sa MAC.
Sur la troisième ligne on voit la première IP disponible sur le réseau de server1, son interface et sa MAC.
Sur la quatrième ligne on voit l'IP de server1, son interface et sa MAC.

## 2. Requêtes ARP

```
[dmathieu@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
[dmathieu@client1 ~]$
```

Seul le router est joignable, puis on fait un ping 10.3.2.11 -c 3 pour envoyer 3 paquets a server1, on regarde de nouveau la table ARP : 

```
[dmathieu@client1 ~]$ ping 10.3.2.11 -c 3
PING 10.3.2.11 (10.3.2.11) 56(84) bytes of data.
64 bytes from 10.3.2.11: icmp_seq=1 ttl=63 time=0.807 ms
64 bytes from 10.3.2.11: icmp_seq=2 ttl=63 time=0.604 ms
64 bytes from 10.3.2.11: icmp_seq=3 ttl=63 time=0.604 ms

--- 10.3.2.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2007ms
rtt min/avg/max/mdev = 0.604/0.671/0.807/0.100 ms
```
on regarde de nouveau la table ARP :
```
[dmathieu@client1 ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:e1:80:3d REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
[dmathieu@client1 ~]$
```

l'entrée vers le serveur1 est possible car nous avons l'indication "REACHABLE" qui la montre.


### B. Table ARP 2

```
[dmathieu@server ~]$ sudo ip neigh flush all
[dmathieu@server ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:13 REACHABLE
[dmathieu@server ~]$
 ```
On vide de nouveau les tables ARP comme précédemment, puis on ping l'addresse suivante: ```10.3.2.11 -c 3```. 

```
[dmathieu@server ~]$ ping 10.3.2.11 -c 3
PING 10.3.2.11 (10.3.2.11) 56(84) bytes of data.
64 bytes from 10.3.2.11: icmp_seq=1 ttl=64 time=0.021 ms
64 bytes from 10.3.2.11: icmp_seq=2 ttl=64 time=0.028 ms
64 bytes from 10.3.2.11: icmp_seq=3 ttl=64 time=0.060 ms

--- 10.3.2.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.021/0.036/0.060/0.017 ms
[dmathieu@server ~]$
```

Suite à ce ping nous obtenons une nouvelle table ARP du server:

```
[dmathieu@server ~]$ ip neigh show
10.3.2.254 dev enp0s8 lladdr 08:00:27:af:a6:61 STALE
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:13 REACHABLE
```

On voit que la première ligne indique que l'entrée du rooter est valide.
Puis une seconde ligne est apparue suite au ping réalisé et montre que l'entrée du client1 est également valable.

## E. u okay bro ?

Prenons l'exemple du client dont l'identité est 10.3.1.11. Il se rend dans son bureau de poste (ici représenté par 10.3.1.254) pour y déposer une lettre. Il veut savoir dans quelle boite aux lettres il doit mettre sa fameuse lettre représentée par la trame suivante:
```
10  19.027394   PcsCompu_61:52:2s   Broadcast   ARP 60  Who has 10.3.2.11? Tell 10.3.2.254
```

Le postier lui répond alors que c'est la boite aux lettres suivante : 

```
45   1.687148    PcsCompu_95:e0:7b   PcsCompu_92:14:91   ARP 60  10.3.1.254 is at 08:00:27:1a:a7:8b
```

Il te dit donc d'aller à cette boite aux lettres : ```08:00:27:1a:a7:8b```

Le colis est enfin acheminé vers la ville souhaitée (représenté ici par 10.3.2.254).

Cependant le postier doit savoir où est le destinataire de la lettre. 
Donc il te demande "qui habite au 10.3.2.11 ?" cela ce traduit par : 

```
3   19.027575   PcsCompu_1a:a7:8b   Broadcast   ARP 60  Who has 10.3.2.11? Tell 10.3.2.254
```

On lui réponds : 

```
4   19.027453   PcsCompu_37:ca:7e   PcsCompu_16:98:9d   ARP 42  10.3.2.11 is at 08:00:27:33:cd:4e
```

Le postier connait maintenant l'adresse à laquelle il doit livrer sa lettre.

# Entracte : Donner un accès internet aux VMs

Dans un premier temps nous allons réactiver la NAT en effectuant la commande ``` sudo ifup enp0s3 ```


On ping depuis client1 :
```
[dmathieu@lient1 ~]$ ping 8.8.8.8 -c 3
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=52 time=16.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=52 time=15.6 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=52 time=15.6 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2013ms
rtt min/avg/max/mdev = 15.637/16.016/16.716/0.516 ms
[dmathieu@lient1 ~]$
```

 
On rajoute la ligne ```nameserver 1.1.1.1```
dans le fichier de conf /etc/resolv.conf pour traduire les adresses IP en noms de domaines.

Puis on test pour voir la résolution de nom DNS

Pour cela on tape la commande : ```curl duckduckgo.com``` ou ```dig duckduckgo.com ```

```
[dmathieu@lient1 ~]$ curl duckduckgo.com
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
[dmathieu@lient1 ~]$
```
L'installation des packages epel-release fonctionne bien tout comme sl.

On tape la commande ``` sl ```



<div class="Gif " style="width: 518px; height: 448.933px;"><img src="https://media1.tenor.com/images/ed88efeb70f0602d731166117934145b/tenor.gif?itemid=7690460" width="518" height="448.93333333333334" alt="Trump Draws GIF - Trump Draws Train GIFs" style="max-width: 518px; background-color: rgb(63, 63, 63);"></div>


Et miracle !!!!

![](https://i.imgur.com/5exttzS.png)

# III. Plus de tcpdump
## 1. TCP et UDP
### A. Warm-up

Tout d'abord sur server1 ajoute le port 9999 dans le firewall :
```sudo firewall-cmd --add-port=9999/tcp --permanent```

et on oublie pas de reload 
```sudo firewall-cmd --reload ```


Ensuite un tape la commande 
```nc -l -t -p 9999 ```sur le server1

Et la commande ```nc -t 10.3.2.11 9999``` sur le client1.

Et on obtient des messages qui arrive bien de client1 :
```
[dmathieu@server ~]$ nc -t 10.3.2.11 9999
bonsoir paris yehhhhhh ! 
j'aime les pates
```

Puis on refait les memes test avec l'échange en udp et on obtient des messages qui arrivent bien de client1 : 

```
[dmathieu@server ~]$ nc -l -u -p 9999
bonsoir
j'aime le chocolat au lait
```

## B. Analyse de trames


TCP

Le 3-way handshake est représenté dans les 3 trames suivantes :

```
18   6.307956   10.3.1.11   10.3.2.11   TCP 74  33794 → 9999 [SYN] Seq=0 Win=29200 Len=0 MSS=1460 SACK_PERM=1 TSval=15407958 TSecr=0 WS=64

19   6.308462    10.3.2.11   10.3.1.11   TCP 74  9999 → 33794 [SYN, ACK] Seq=0 Ack=1 Win=28960 Len=0 MSS=1460 SACK_PERM=1 TSval=15403128 TSecr=15407937 WS=64

20   6.308956   10.3.1.11   10.3.2.11   TCP 66  33794 → 9999 [ACK] Seq=1 Ack=1 Win=29248 Len=0 TSval=15407946 TSecr=15403128
```
Dans la trame :


* [SYN] = salut
* [SYN, ACK] = OK, salut
* [ACK] = OK





```
6   7.991315    10.3.1.11   10.3.2.11   TCP 25  33794 → 9999 [PSH, ACK] Seq=1 Ack=1 Win=29248 Len=6 TSval=15405245 TSecr=15403128

7   7.992530    10.3.2.11   10.3.1.11   TCP 86  9999 → 33794 [ACK] Seq=1 Ack=7 Win=28992 Len=0 TSval=15404811 TSecr=15405284

```

* PSH=PUSH, cela signifie que l'on envoi de la donnée
* ACK= C'est un accusé de réception

```
65  15.891859   10.3.2.11   10.3.1.11   TCP 66  9999 → 33794 [FIN, ACK] Seq=1 Ack=22 Win=28992 Len=0 TSval=15410710 TSecr=15412504

66  15.893455   10.3.1.11   10.3.2.11   TCP 66  33794 → 9999 [ACK] Seq=22 Ack=2 Win=29248 Len=0 TSval=15415532 TSecr=15410710

67  16.487457   10.3.1.11   10.3.2.11   TCP 66  33794 → 9999 [FIN, ACK] Seq=22 Ack=2 Win=29248 Len=0 TSval=15416125 TSecr=15410710

68  16.487501   10.3.2.11   10.3.1.11   TCP 66  9999 → 33794 [ACK] Seq=2 Ack=23 Win=28992 Len=0 TSval=15411309 TSecr=15416125

```

* FIN=cela correspond à la fin de la connexion, soit le terme "Finish"
* ACK = Comme dis précédemment cela correspond à "l'accusé de réception"

Maintenant nous allons analysé UDP


```
3   4.052752   10.3.1.11   10.3.2.11   UDP 60  41261 → 9999 Len=5

4   4.532415    10.3.1.11   10.3.2.11   UDP 60  41261 → 9999 Len=5

5   5.375462    10.3.1.11   10.3.2.11   UDP 60  41261 → 9999 Len=4

```

On remarque que notre port 9999 est présent, et le protocole UDP accepte les messages d'origines sans les modifier, sans avoir besoin d'accusé de réception et de trafic supplémentaire.


## 2. SSH

Tout d'abord on va ce connecter en ssh de la VM client1 jusqu'à notre VM server
```
[dmathieu@lient1 ~]$ ssh 10.3.2.11 -p7777
The authenticity of host '[10.3.2.11]:7777 ([10.3.2.11]:7777)' can't be established.
ECDSA key fingerprint is SHA256:PGarFcZVigk8j9Fo6tH/4PIPt8TgcmjrfjywJqDGHy8.
ECDSA key fingerprint is MD5:50:5d:a5:30:d3:33:46:96:0a:0c:c2:10:af:18:68:9a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[10.3.2.11]:7777' (ECDSA) to the list of known hosts.
dmathieu@10.3.2.11's password:
Last login: Wed Mar 18 09:28:55 2020 from 10.3.2.1
[dmathieu@server ~]$ 
```

Le SSH est en TCP puisqu'il utilise le 3-Way Handshake 
On peut le voir grâce à la commande ```tcpdump```: 


```
147	123.516892	10.3.1.11	10.3.2.11	TCP	90	7777 → 19552 [PSH, ACK] Seq=1801 Ack=1409 Win=602 Len=36

147	123.516892	10.3.2.11	10.3.1.11	TCP	90	7777 → 19552 [PSH, ACK] Seq=1801 Ack=1409 Win=602 Len=36

148	123.557429	10.3.1.11	10.3.2.11	TCP	54	19552 → 7777 [ACK] Seq=1409 Ack=1837 Win=8209 Len=0
```


<div class="_14F1-b2e7pRNk2JiF8fep6 _9LhqAj9O47h_09bUe9mYI ss-ellipsis"></div></div></div><div class="_1M8xq1jPOAHRc0OSZxxS8_" style="width: 480px; height: 480px; left: 0px; top: 0px;"><div class="KRS9L9BsuEdhF-ACKiX8x" data-sticker="false"><div><div><video alt="deadpool 2 goodbye GIF by 20th Century Fox Home Entertainment" src="https://media0.giphy.com/media/8Bl38gdtUK7WDdi59y/giphy.mp4?cid=790b7611a3922ff72c0eb6cd0f2f9086478b95a4e94dbf8f&amp;rid=giphy.mp4" poster="https://media0.giphy.com/media/8Bl38gdtUK7WDdi59y/giphy_s.gif?cid=790b7611a3922ff72c0eb6cd0f2f9086478b95a4e94dbf8f&amp;rid=giphy_s.gif" autoplay="" loop="" playsinline="" style="width: 480px; height: 480px; left: 0px; top: 0px;"></video>
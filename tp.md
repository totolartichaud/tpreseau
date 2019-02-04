# TP Réseau 

## 1. ARP

### A. Manipulation 1 

#### 2 & 3 : La ligne affichée est le lien de connexion entre la VM et le SSH ce qui est affiché est l'id de la carte réseau dans la table ARP. 

#### 4 & 5 : 

Coté server :
```
10.2.0.1 dev enp0s8 lladdr 0a:00:27:00:00:36 DELAY
10.2.0.254 dev enp0s8 lladdr 08:00:27:5a:cf:3d STALE
```

Coté client :
```
10.1.0.254 dev enp0s8 lladdr 08:00:27:8e:60:ae STALE
10.1.0.1 dev enp0s8 lladdr 0a:00:27:00:00:15 DELAY
```

La ligne rajoutée représente la connexion des deux machines (server, client) à travers le routeur donc ajout dans la table ARP du routeur. 

### B. Manipulation 2 

#### 2 : 
```
10.1.0.1 dev enp0s8 lladdr 0a:00:27:00:00:15 DELAY
```

La ligne affichée correspond à la carte réseau par lequel est connecté le SSH.

#### 3 & 4 : 

```
10.2.0.10 dev enp0s9 lladdr 08:00:27:c7:54:68 STALE
10.1.0.10 dev enp0s8 lladdr 08:00:27:a3:8b:d6 STALE
10.1.0.1 dev enp0s8 lladdr 0a:00:27:00:00:16 REACHABLE
```

On remarque que les adresse des deux machines se sont ajoutées sur la table ARP de la VM routeur, tout simplement car c'est elle qui fait la connexion entre les deux réseaux.

### C. Manipulation 3 

Pour vider la table ARP de toutes les machines il faut exécuter la commande `netsh interface ip delete arpcache`, puis en affichant une première fois la table ARP on obtient : 
```
Interface : 192.168.1.18 --- 0x4
  Adresse Internet      Adresse physique      Type
  192.168.1.1           90-01-3b-d7-5d-c1     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.2.0.1 --- 0x8
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 169.254.150.203 --- 0x10
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.1.0.1 --- 0x16
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```
Puis en attendant un peu nous obtenons : 
```
Interface : 192.168.1.18 --- 0x4
  Adresse Internet      Adresse physique      Type
  192.168.1.1           90-01-3b-d7-5d-c1     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.2.0.1 --- 0x8
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 169.254.150.203 --- 0x10
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.1.0.1 --- 0x16
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```
Nous pouvons remarquer la présence de l'ip ` 239.255.255.250 ` qui correspond au protocole SSDP, ainsi que la présence de l'ip `224.0.0.22` qui est l'IP utilisée par le protocole IGMP.

### D. Manupilation 4 

Tout d'abord il faut faire la commande `sudo ip neigh flush all` sur chacune des machines. Puis sur la VM cliente : 
Avant connexion à google.com
```
10.1.0.1 dev enp0s8 lladdr 0a:00:27:00:00:16 DELAY
```
Après connexion à google.com :
```
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 REACHABLE
10.1.0.1 dev enp0s8 lladdr 0a:00:27:00:00:16 REACHABLE
```

On peut voir que l'ip `10.0.2.2` a été rajoutée à la table, elle correspond à l'IP local de CentOS, c'est donc cette IP qui fait le lien entre la VM et la carte NAT.

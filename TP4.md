# 🧠 TP4 Lab - Implement MST

## 👨‍💻 Réalisé par
**Nom :** Zaid Hankri  
**Filière :** E4CCSN  
**Date :** Octobre 2025  

---

## 🎯 Objectifs du TP

Ce TP a pour objectif de configurer et vérifier plusieurs méthodes de **routage inter-VLAN** sur des équipements Cisco, et d’examiner les tables de commutation et de routage utilisées dans le processus.

### ✅ Les objectifs spécifiques :
- **Partie 1 :** Construire le réseau et configurer les paramètres de base des périphériques et l'adressage de l'interface  
- **Partie 2 :** Mettre en œuvre et observer le MST    
- **Partie 3 :** CConfigurer, régler et vérifier le fonctionnement de base de MST 

---

## 🧩 Topologie du Réseau

Voici la topologie réalisée sur **Cisco Packet Tracer** :
<p align="center">
<img width="564" height="299" alt="image" src="https://github.com/user-attachments/assets/854e779d-d9a3-4083-98e3-9fa3c4be69f6" />
</p>

## 🖥️ Matériel utilisé
- **Switch D1** (Cisco 3650 avec IOS XE 16.9.4 universalk9 ou équivalent)  
- **Switch D2** (Cisco 3650 avec IOS XE 16.9.4 universalk9 ou équivalent)  
- **Switch A1** (Cisco 2960+ avec IOS 15.2 lanbase ou équivalent)  
- **PC** (Windows avec émulateur terminal, ex. Tera Term)  
- **Câbles console et Ethernet** pour la connexion et configuration des équipements  

---

## 🧱 Partie 1 – Créer le réseau et configurer les paramètres de base des périphériques et l'adressage des interfaces

### ⚙️ Étapes :
1. Créer le réseau et configurer les paramètres de base des périphériques et l'adressage des interfaces  

   a. Câbler le réseau selon la topologie.
<p align="center">
<img width="642" height="353" alt="image" src="https://github.com/user-attachments/assets/a882fe3c-592a-4eed-9fc3-bf4dffa47285" />
</p>

  b. Configure basic settings for each switch

Nous nous connectons à chaque commutateur via la console, entrons en mode de configuration globale et appliquons les paramètres de base. Les configurations de démarrage de chaque appareil sont présentées ci-dessous.

```bash
hostname D1
banner motd # D1, Multiple Spanning Tree #
spanning-tree mode rapid-pvst
line con 0
 exec-timeout 0 0
 logging synchronous
 exit
interface range g1/0/1-24, g1/1/1-4, g0/0
 shutdown
 exit
interface range g1/0/1, g1/0/5-6
 switchport mode trunk
 no shutdown
 exit
vlan 2
 name SecondVLAN
 exit
vlan 3
 name ThirdVLAN
 exit
vlan 4
 name FourthVLAN
 exit
vlan 5
 name FifthVLAN
 exit
```
<p align="center">
<img width="579" height="252" alt="image" src="https://github.com/user-attachments/assets/6b9e2eca-5c6c-45c2-90b6-c3bbaabe921a" />
</p>

## 🧱 Partie 2 – CMettre en œuvre et observer le MST  

1. Configurer MST sur D1 et D2

  a. On D1 and D2, issue the command spanning-tree mode mst.

```bash
D1(config)# spanning-tree mode mst
```
<p align="center">
<img width="711" height="38" alt="image" src="https://github.com/user-attachments/assets/88a2c7d0-1d12-4eaa-b961-a6b2afb7ee20" />
</p>

  b. À ce stade, sans configuration spécifique à MST, l'instance MST 0 est opérationnelle pour tous les VLAN. Exécutez la commande « show spanning-tree » : les informations de l'arbre de recouvrement concernent MST 0. Exécutez la commande « show spanning-tree mst » pour afficher les informations STP spécifiques à MST 0. Notez les informations affichées pour les interfaces g1/0/5 et g1/0/6, car elles sont connectées à un commutateur qui n'exécute pas MST. Leur type est P2P Bound (PVST).

```bash
D1# show spanning-tree
```
<p align="center">
  <img width="707" height="333" alt="image" src="https://github.com/user-attachments/assets/dcba8222-fd05-47a8-b8c2-a473f3137880" />
</p>

```bash
show spanning-tree mst
```
<p align="center">
  <img width="713" height="251" alt="image" src="https://github.com/user-attachments/assets/5e5d38e4-bb1f-4691-ad77-7958c54505ba" />
</p>

  c. Le fonctionnement de base de MST est identique à celui de Spanning Tree : il faut sélectionner un pont racine, puis les ports racine, et enfin les meilleurs chemins vers le pont racine depuis tous les ponts non racine. Dans le réseau actuel, D1 a été élu pont racine. La priorité du pont étant par défaut de 32 768, l'élection est basée sur l'adresse MAC de base inférieure de D1. Le commutateur choisi comme racine peut être différent dans votre topologie de laboratoire, mais les règles d'élection restent les mêmes. Exécutez la commande « show spanning-tree root » sur le commutateur A1. Ce dernier exécute cinq instances de Spanning Tree.

```bash
A1# show spanning-tree
```

<p align="center">
<img width="714" height="183" alt="image" src="https://github.com/user-attachments/assets/2a685481-ae91-42c8-a17d-bd7fdfa4c3d0" />
</p>

  d. Si nous exécutons la commande show spanning-tree root sur D2, le résultat sera différent. En effet, avec MST, une seule instance de l'algorithme spanning-tree s'exécute, quel que soit le nombre de VLAN qui lui sont associés.

```bash
D2# show spanning-tree
```
<p align="center">
<img width="710" height="177" alt="image" src="https://github.com/user-attachments/assets/53ca1eaa-1fa7-4d66-a622-e8a45e9f79fc" />
</p>

  e. Configurer A1 pour utiliser MST

```bash
A1(config)# spanning-tree mode mst
```

<p align="center">
<img width="712" height="67" alt="image" src="https://github.com/user-attachments/assets/c3ebab8d-cd75-4777-9c8c-816fadc97791" />
</p>

## 🧱 Partie 3 – Configurer, régler et vérifier le fonctionnement de base de MST  

Dans la partie précédente, vous avez configuré les trois commutateurs pour exécuter MST. Dans cette partie, vous allez configurer, ajuster et vérifier MST pour répondre aux exigences topologiques spécifiques.

1. Create and verify an MST configuration.

  MST permet aux ingénieurs réseau de réduire la charge du protocole Spanning Tree tout en fournissant des topologies Spanning Tree uniques pour les groupes de VLAN. La configuration MST doit être effectuée manuellement sur chaque commutateur individuellement.

  a. Entrez le mode de configuration MST à l'aide de la commande spanning-tree mst configuration.

<p align="center">
<img width="713" height="156" alt="image" src="https://github.com/user-attachments/assets/1a7932a3-7814-4c38-a8ad-33a3e2d254ed" />
</p>

  b. Exécutez la commande « show spanning-tree mst » pour vérifier que la configuration est en place.
Remarque : En mode de configuration « spanning-tree mst », vous pouvez utiliser les commandes « show current » et « show pending » pour afficher les paramètres de configuration actuels et en attente.


```bash
D1# show spanning-tree mst
```
<p align="center">
<img width="713" height="543" alt="image" src="https://github.com/user-attachments/assets/7d677cf0-a5dd-4f0d-90db-2efcfa45a98b" />
</p>

c. Cette configuration ne se propage pas aux autres commutateurs. Chaque commutateur échange des informations de résumé résumant les mappages VLAN-instance qu'il a configurés. Si un commutateur reçoit une BPDU avec un résumé différent, il suppose que l'expéditeur se trouve dans une autre région MST. Le résultat ci-dessous correspond à ce que montre A1 dans la topologie utilisée pour créer ce laboratoire. Notez que les ports connectés à D1 sont classés comme P2p Bound (RSTP).

```bash
A1# show spanning-tree mst
```
<p align="center">
<img width="711" height="293" alt="image" src="https://github.com/user-attachments/assets/f7733dea-e2f8-43e8-acad-c995b318b5c7" />
</p>

d. Configurez MST sur D1, modifiez le numéro de révision à 2 et ajoutez le VLAN 3 à l'instance 1 et le VLAN 5 à l'instance 2.

<p align="center">
<img width="712" height="483" alt="image" src="https://github.com/user-attachments/assets/3615c449-5cc0-48b1-8b0b-2b571c37284a" />
</p>


e. Configurez maintenant D2 et A1 avec les mêmes paramètres de configuration (nom CCNPv8, révision 2, instance 1 VLAN 2-3, instance 2 VLAN 4-5). Une fois la configuration terminée sur D2 et A1, le résultat de la commande show spanning-tree mst sur A1 devrait ressembler à celui-ci. Dans ce cas, D1 est la racine des trois instances.

<p align="center">
<img width="711" height="293" alt="image" src="https://github.com/user-attachments/assets/f7733dea-e2f8-43e8-acad-c995b318b5c7" />
</p>

2. Controlling the Root Bridge.

Tout comme avec PVST+ et Rapid PVST+, le pont racine actuel a été sélectionné en fonction de l'ID de pont le plus bas (comprenant la priorité, l'ID système étendu égal à l'ID VLAN et les valeurs d'adresse MAC de base).
La ​​priorité et l'ID système étendu étant identiques, l'adresse MAC du pont racine est numériquement inférieure à celle du pont local. Par conséquent, dans un réseau totalement non configuré, un seul commutateur sera sélectionné comme pont racine. Le choix du commutateur qui en résulte peut être judicieux ou non.
Avec PVST+ ou Rapid PVST+, la sélection du pont racine s'effectue pour chaque VLAN. Avec MST, le pont racine est basé sur des instances.
Il existe deux méthodes principales pour manipuler la configuration afin de contrôler l'emplacement du pont racine :
• La commande spanning-tree mst instance-id priority value permet de définir manuellement une valeur de priorité.
• La commande spanning-tree mst instance-id root {primary | secondary} permet de définir automatiquement une valeur de priorité.

La différence entre les deux est que la commande priority définira un nombre spécifique (multiple de 4096) comme priorité, tandis que la commande root primary définira la priorité du pont local à 24 576 (si le MAC du pont local est inférieur au MAC du pont racine actuel) ou 4 096 inférieur à la priorité de la racine actuelle (si le MAC du pont local est supérieur au MAC du pont racine actuel).
  
  a. Modifiez D1 et D2 afin que D1 soit choisi comme pont racine principal pour l'instance MST 1 et D2 comme pont racine principal pour l'instance MST 2. D1 doit être choisi comme pont racine secondaire pour l'instance MST 2, et D2 comme pont racine secondaire pour l'instance MST 1. Vous devrez modifier la configuration de D1 et D2. Les commandes utilisées pour D1 sont :

```bash
D1(config)# spanning-tree mst 1 root primary
D1(config)# spanning-tree mst 2 root secondary
```
<p align="center">
<img width="709" height="88" alt="image" src="https://github.com/user-attachments/assets/2830a877-8818-4a7f-9b16-7174a1e8e27a" />
</p>

3. Ajustez les valeurs de coût du port pour avoir un impact sur la sélection du port racine et désigné.

Dans la configuration actuelle du réseau, il existe deux chemins directs entre le commutateur A1 et le pont racine pour chaque MST. Les coûts des chemins et des ports sont évalués afin de déterminer le chemin le plus court vers le pont racine. En présence de plusieurs chemins de même coût vers le pont racine, des attributs supplémentaires doivent être évalués. Dans notre cas, le numéro d'interface le plus bas (par exemple, F0/1) est choisi comme port racine, et le numéro d'interface le plus haut (par exemple, F0/2) est placé dans un état de blocage de l'arbre de recouvrement.

Vous pouvez voir quels ports sont bloqués avec la commande show spanning-tree vlan-id ou show spanning-tree blockedports. Pour l'instant, examinez le VLAN 1 sur D1.

  a. Sur A1, émettez les commandes show spanning-tree vlan 1 et show spanning-tree blockedports.

```bash
A1# show spanning-tree mst 1
```
<p align="center">
<img width="711" height="293" alt="image" src="https://github.com/user-attachments/assets/f7733dea-e2f8-43e8-acad-c995b318b5c7" />
</p>

Remarque : Les modifications que vous vous apprêtez à implémenter sont considérées comme des modifications de topologie et pourraient avoir un impact significatif sur la structure globale de l'arbre de recouvrement de votre réseau de commutation. N'effectuez pas ces modifications sur un réseau de production sans une planification minutieuse et une coordination préalable.

  b. Sur A1, arrêtez les interfaces F0/1 et F0/2, attribuez un nouveau coût de port de 1 000 à F0/2 à l'aide de la commande spanning-tree mst 1 cost value, puis émettez la commande no shutdown sur les ports.

<p align="center">
<img width="713" height="303" alt="image" src="https://github.com/user-attachments/assets/085744a8-3bc5-4997-ad8b-b32b9a0389b8" />
</p>

Nombre de ports (segments) bloqués dans le système : 7
Le résultat indique que le port racine sélectionné par A1 pour le VLAN 1 est désormais l'interface e0/2, et que le coût du port (et de la racine) est désormais de 1 000.


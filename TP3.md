# 🧠 TP3 Lab - Implement Advanced STP Modifications and Mechanisms

## 👨‍💻 Réalisé par
**Nom :** Zaid Hankri  
**Filière :** E4CCSN  
**Date :** Octobre 2025  

---

## 🎯 Objectifs du TP

- **Partie 1 :** Build the Network and Configure Basic Device Settings and Interface Addressing  
- **Partie 2 :** Implement and Observe Various Topology Tuning Methods   
- **Partie 3 :** Implement and Observe Various Topology Protection Mechanisms   

---

## 🧩 Topologie du Réseau

Voici la topologie réalisée sur **Cisco Packet Tracer** :
<p align="center">
<img width="561" height="300" alt="image" src="https://github.com/user-attachments/assets/05572f4e-1e83-47ce-a922-c6568d7ee05e" />

</p>

•	2 Switches (Cisco 3650 with Cisco IOS XE release 16.9.4 universal image or comparable)  
•	1 Switch (Cisco 2960+ with Cisco IOS release 15.2 lanbase image or comparable)  
•	1 PC (Windows with a terminal emulation program, such as Tera Term)  
•	Console cables to configure the Cisco IOS devices via the console ports  
•	Ethernet cables as shown in the topology  


---

## 🧱 Partie 1 – Créer le réseau et configurer les paramètres de base des périphériques et l'adressage des interfaces

### ⚙️ Étapes :  

1. Câbler le réseau selon la topologie.

<p align="center">
<img width="905" height="384" alt="image" src="https://github.com/user-attachments/assets/14193ea4-5a29-46e3-8a43-46a8be0d9f1e" />
</p>

2. Configurer les paramètres de base pour chaque commutateur.

<p align="center">
<img width="625" height="141" alt="image" src="https://github.com/user-attachments/assets/e79d29a9-43ed-4101-8a15-db663125b426" />
</p>

<p align="center">
<img width="625" height="197" alt="image" src="https://github.com/user-attachments/assets/fe9dc298-dfa6-4ef0-8469-887ae7b93e2e" />
</p>

```bash
hostname A1
banner motd # A1, STP Tuning and Protection #
spanning-tree mode rapid-pvst
 line con 0
 exec-timeout 0 0
 logging synchronous
 exit
interface range f0/1-24, g0/1-2
 shutdown
 exit
interface range f0/1-4
 switchport mode trunk
 no shutdown
 exit
vlan 2
 name SecondVLAN
 exit
interface vlan 1
 ip address 10.0.0.3 255.0.0.0
 no shut
 exit
```

3. découvrir l’arbre couvrant par défaut.

Nos commutateurs sont configurés, les interfaces activées et le Rapid Spanning Tree a déjà convergé vers un réseau logique sans boucle. Avant de commencer ce TP, il est important de comprendre la topologie du Spanning Tree. Nous devons identifier l'emplacement du pont racine et des ports racine, désigné et alternatif sur chaque segment de chaque VLAN. Il peut être utile de visualiser ces informations. L'image ci-dessous détaille le fonctionnement du Spanning Tree pour l'équipement utilisé dans ce TP. La topologie du Spanning Tree est identique pour les VLAN 1 et 2.

<p align="center">
<img width="496" height="294" alt="image" src="https://github.com/user-attachments/assets/2c5f93da-1c5c-4d5c-a45b-8f7d62973b2a" />
</p>

## 🧱 Partie 2 – Mettre en œuvre et observer diverses méthodes de réglage de la topologie

### ⚙️ Étapes : 


1. Contrôle du pont racine.

Le pont racine actuel a été choisi en fonction de l'ID de pont le plus bas (comprenant la priorité, l'ID système étendu égal à l'ID VLAN et les valeurs d'adresse MAC de base).
La ​​priorité et l'ID système étendu étant identiques, l'adresse MAC du pont racine est numériquement inférieure à celle du pont local. Par conséquent, dans un réseau totalement non configuré, un seul commutateur sera choisi comme pont racine. Le choix du commutateur qui en résulte peut être souhaitable ou non.
Il existe deux méthodes principales pour manipuler la configuration afin de contrôler l'emplacement du pont racine :
• La commande spanning-tree vlan vlan-id priority value permet de définir manuellement une valeur de priorité.
• La commande spanning-tree vlan vlan-id root {primary | secondary} permet de définir automatiquement une valeur de priorité.

Nous modifions D1 et D2 afin que D1 soit choisi comme pont racine principal pour le VLAN 1 et D2 comme pont racine principal pour le VLAN 2. D1 doit être choisi comme pont racine secondaire pour le VLAN 2, et D2 comme pont racine secondaire pour le VLAN 1. Nous devons modifier la configuration de D1 et D2. Les commandes utilisées pour D1 sont les suivantes :
<p align="center">
<img width="625" height="60" alt="image" src="https://github.com/user-attachments/assets/3fb9f422-a672-4eb1-9cbc-f75ae6a4d896" />
</p>

Après avoir configuré D1 et D2, accédez à A1 et exécutez la commande « show spanning-tree root ». Ce résultat permet de différencier les ponts racines.
<p align="center">
<img width="626" height="500" alt="image" src="https://github.com/user-attachments/assets/84aebf09-e04a-4f79-84eb-f78d088712bd" />
</p>

À partir de la sortie ci-dessus, nous pouvons voir que le port racine du VLAN 1 /VLAN 2 est F0/1.

2. Ajustez les valeurs de coût du port pour influencer la sélection du port racine et du port désigné.

Dans la configuration actuelle du réseau, il existe deux chemins directs entre le commutateur A1 et le pont racine pour chaque VLAN. Les coûts des chemins et des ports sont évalués afin de déterminer le chemin le plus court vers le pont racine. En présence de plusieurs chemins de même coût vers le pont racine, des attributs supplémentaires doivent être évalués. Dans notre cas, le numéro d'interface le plus bas (par exemple, F0/1) est choisi comme port racine, et le numéro d'interface le plus élevé (par exemple, F0/2) est placé dans un état de rejet de l'arbre de recouvrement.

a. Sur A1, nous exécutons les commandes show spanning-tree vlan 1  
<p align="center">
<img width="625" height="250" alt="image" src="https://github.com/user-attachments/assets/69e771e5-fd2a-4424-ae06-fdd128d10e76" />
</p>

Comme on peut le constater, le port racine du VLAN 1 est F0/1. F0/2, F0/3 sont des ports de blocage alternatifs. Pour déterminer quel port devient le port racine sur les ponts non racine, nous devons modifier le coût ou la priorité du port. N'oubliez pas que cette modification peut également avoir un impact sur les commutateurs en aval.

b. Sur A1, nous fermons les interfaces F0/1 et F0/2, attribuons un nouveau coût de port à F0/2, puis nous n'exécutons aucun arrêt sur les ports  

```bash
A1# conf t
Enter configuration commands, one per line.  End with CNTL/Z.
A1(config)# interface range f0/1-2
A1(config-if-range)# shutdown
A1(config-if-range)# exit
A1(config)# interface f0/2
A1(config-if)# spanning-tree cost 12
A1(config-if)# exit
A1(config)# interface range f0/1-2
A1(config-if-range)# no shutdown
A1(config-if-range)# exit
A1(config)# end
```
<p align="center">
<img width="625" height="267" alt="image" src="https://github.com/user-attachments/assets/78614ec3-cdcc-49a7-ae68-3b53af710ea5" />
</p>

Nous vérifions maintenant que cela a un impact sur la sélection du port racine sur A1 à l'aide des commandes show spanning-tree vlan 1.
<p align="center">
<img width="625" height="253" alt="image" src="https://github.com/user-attachments/assets/32a3c8b7-b55c-4059-85e1-336c445bb2a2" />
</p>

À partir de la sortie, vous pouvez voir que le port racine sélectionné par A1 pour le VLAN 1 est désormais l'interface F0/2, et le coût du port (et de la racine) est désormais de 12. Il y a un autre impact sur le coût défini comme il l'a été.

c. nous ajustons la valeur du coût de l'interface F0/2 sur A1 à 18. Cela rendra à nouveau le port racine du VLAN 2 F0/3. 

```bash
A1(config)# interface range f0/1-2
A1(config-if-range)# shutdown
A1(config-if-range)# exit
A1(config)# interface f0/2
A1(config-if)# spanning-tree cost 18
A1(config-if)# exit
A1(config)# interface range f0/1-2
A1(config-if-range)# no shutdown
A1(config-if-range)# exit
A1(config)# end
A1#
A1# show spanning-tree root
```

<p align="center">
<img width="627" height="272" alt="image" src="https://github.com/user-attachments/assets/50833972-8e51-48bf-be7f-e873303aebcc" />
</p>

```bash
show spanning-tree root
```
<p align="center">
<img width="625" height="247" alt="image" src="https://github.com/user-attachments/assets/055eeb11-6968-4fa9-91c0-fb71f8c1fc00" />
</p>

3. ajustez les valeurs de priorité du port pour influencer la sélection du port racine.

La méthode suivante pour influencer la sélection du port racine est configurée sur le pont racine lui-même. Dans notre topologie réseau actuelle, A1 dispose de deux connexions au pont racine pour le VLAN 2, commutateur D2. Le port racine a été sélectionné, dans ce cas en fonction de l'ID de port le plus bas. L'ID de port est composé de deux valeurs : Prio (priorité) et Nbr (numéro).
   
a.	Sur A1, émettez la commande show spanning-tree vlan 2 et notez les valeurs d’ID de port répertoriées.

<p align="center">
<img width="533" height="80" alt="image" src="https://github.com/user-attachments/assets/fd599729-af4f-4eb9-9e6d-f8b187885dad" />
</p>

Comme prévu avec deux chemins de coût égal vers le pont racine, l’ID de port inférieur a été sélectionné comme port racine.

b. Modifiez la priorité du port de l’interface D2 G1/0/6 afin qu’il devienne le port préféré.

<p align="center">
<img width="629" height="447" alt="image" src="https://github.com/user-attachments/assets/1163af7f-37a8-4cc5-a2c8-93d5100ca7ce" />
</p>

Sur A1, la commande show spanning-tree vlan 2 et vous constaterez que F0/1 est désormais le port racine sélectionné. Cette sélection est basée sur la valeur de priorité inférieure de l'interface G1/0/5 de D1. Notez que cette valeur de priorité inférieure n'apparaît dans aucune sortie A1.

<p align="center">
<img width="626" height="252" alt="image" src="https://github.com/user-attachments/assets/af5d41f0-e2dd-454e-8841-85beebd7cf0e" />
</p>

4. Implement Spanning Tree Portfast.

Sur A1, nous exécutons la commande debug spanning-tree events, puis la commande no shutdown pour l'interface F0/23, attendons quelques secondes, puis la commande shutdown, suivie de end et undebug all. Le journal de sortie ressemblera à celui ci-dessous.

<p align="center">
<img width="625" height="229" alt="image" src="https://github.com/user-attachments/assets/2104975a-09db-4572-8fbb-4d41638e459f" />
</p>

L'exécution de la commande debug spanning-tree events était une tentative cruciale pour surveiller en temps réel les messages du protocole STP (changement d'état, élection de la racine). L'erreur % Invalid input suggère une syntaxe incomplète ou non supportée par cette version d'IOS. Malgré plusieurs tentatives pour corriger la syntaxe et obtenir les informations de débogage, l'échec a rendu impossible la validation et l'analyse du comportement dynamique du protocole STP, impactant la continuité du TP.









 

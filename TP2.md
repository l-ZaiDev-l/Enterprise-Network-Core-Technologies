# 🧠 TP2 Lab - STP Topology Change et RSTP

## 👨‍💻 Réalisé par
**Nom :** Zaid Hankri  
**Filière :** E4CCSN  
**Date :** Octobre 2025  

---

## 🎯 Objectifs
- **Partie 1 :** Construire le réseau et configurer les paramètres de base des équipements.  
- **Partie 2 :** Observer la convergence du Spanning Tree Protocol (STP) et les changements de topologie.  
- **Partie 3 :** Configurer et vérifier le Rapid Spanning Tree Protocol (RSTP).

---

## 🧩 Topologie du Réseau

Voici la topologie réalisée sur **Cisco Packet Tracer** :
<p align="center">
<img width="518" height="232" alt="image" src="https://github.com/user-attachments/assets/1741817d-60cf-4c66-bd77-eaf910dbee78" />
</p>

## 🖥️ Matériel utilisé
- **Switch D1** (Cisco 3650 avec IOS XE 16.9.4 universalk9 ou équivalent)  
- **Switch D2** (Cisco 3650 avec IOS XE 16.9.4 universalk9 ou équivalent)  
- **Switch A1** (Cisco 2960+ avec IOS 15.2 lanbase ou équivalent)  
- **PC** (Windows avec émulateur terminal, ex. Tera Term)  
- **Câbles console et Ethernet** pour la connexion et configuration des équipements  

---

## ⚙️ Partie 1 : Construire le réseau et configurer les paramètres de base  

### ⚙️ Étapes :

1. Configuration du routage inter-VLAN sur D1

- Connecter les switches et le PC selon le **schéma de topologie** fourni.  
- Vérifier toutes les connexions Ethernet et les ports utilisés.  

📸 *Capture : Topologie réseau câblée*  

<p align="center">
<img width="593" height="294" alt="image" src="https://github.com/user-attachments/assets/89001de8-1367-4432-a61f-2de0bd345637" />
</p>

---

2. Configuration de base des switches
   
   🔹 Switch D1, D2 et A1

Exemple de D2
<p align="center">
<img width="713" height="617" alt="image" src="https://github.com/user-attachments/assets/96eb01c0-d524-445e-9102-bc19cc061de9" />
</p>

```bash
hostname D2
banner motd # D2, STP Topology Change and RSTP Lab #
spanning-tree mode pvst
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
interface vlan 1
ip address 10.0.0.2 255.0.0.0
no shut
exit
```
3. Configurer l’horloge et sauvegarder
   
```bash
clock set UTC
write memory
```

## ⚙️ Partie 2 : Découverte du Spanning Tree par défaut

### ⚙️ Étapes :  

1. Identifier le Root Bridge  

 <p align="center"> 
<img width="745" height="186" alt="image" src="https://github.com/user-attachments/assets/014b7a34-b307-4046-9602-4c542bdc817e" />
</p>

```bash 
show spanning-tree root
```

2. Identifier le Root Ports  

Chaque switch a un seul Root Port, représentant le chemin le plus court vers le Root Bridge.
Exemple : sur D2 :

```bash
Gi1/0/1 Root FWD Cost 4
```
Expérimentation :  
```bash
interface g1/0/1
shutdown
```

Sur D2 :

```bash
Root Path Cost: 38, Root Port Cost: 19
```
La différence illustre port cost vs path cost.

3. Identifier les Designated Ports

 <p align="center"> 
<img width="747" height="276" alt="image" src="https://github.com/user-attachments/assets/90c3cd61-5ea4-427d-89af-5e43b5ecd259" />
</p>

Les Designated Ports restent actifs pour la topologie et la diffusion des BPDUs.
Chaque switch root a tous ses ports en Designated Port.
Sur D2 :

```bash
Gi1/0/5 Desg FWD Cost 19
Gi1/0/6 Desg FWD Cost 19
```

## ⚙️ Partie 3 : Observer la convergence STP et les changements de topologie 

### ⚙️ Étapes :

1. Activer RSTP sur chaque switch

```bash
 spanning-tree mode rapid-pvst
```
2. Observer les événements

```bash
debug spanning-tree events
interface g1/0/1
shutdown
```
3. Observer les événements

Configurer tous les switches en RSTP :

```bash
spanning-tree mode rapid-pvst
```
sur A1, l’interface VLAN1 redémarre en seulement 0.048 secondes → preuve de la rapidité.


## 🧾 Conclusion

Le TP a permis de :

Configurer STP et RSTP sur les switches pour éviter les boucles.

Observer la convergence du STP et la différence avec RSTP.

Vérifier la connectivité réseau et le rôle des Root et Designated Ports.

✅ Le réseau Layer 2 reste opérationnel même lors de changements de topologie, et RSTP améliore significativement le temps de convergence.


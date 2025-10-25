# 🧠 TP Cisco Packet Tracer – Inter-VLAN Routing

## 👨‍💻 Réalisé par
**Nom :** Zaid Hankri  
**Filière :** E4CCSN  
**Date :** Octobre 2025  

---

## 🎯 Objectifs du TP

Ce TP a pour objectif de configurer et vérifier plusieurs méthodes de **routage inter-VLAN** sur des équipements Cisco, et d’examiner les tables de commutation et de routage utilisées dans le processus.

### ✅ Les objectifs spécifiques :
- **Partie 1 :** Construire le réseau et configurer les paramètres de base  
- **Partie 2 :** Configurer et vérifier le routage Inter-VLAN sur un **commutateur de couche 3**  
- **Partie 3 :** Configurer et vérifier le routage Inter-VLAN basé sur un **routeur**  

---

## 🧩 Topologie du Réseau

Voici la topologie réalisée sur **Cisco Packet Tracer** :
<p align="center">
<img width="708" height="370" alt="image" src="https://github.com/user-attachments/assets/45f1ff84-01ef-4ea0-ba88-94f95c35ed18" />
</p>
> *Topologie du réseau utilisée pour le TP (R1, R3, D1, D2, et 4 PC).*

---

## 🧱 Partie 1 – Configuration de Base des Équipements

### ⚙️ Étapes :
1. Câbler le réseau selon la topologie.
   
<img width="1213" height="484" alt="image" src="https://github.com/user-attachments/assets/0a5a9750-f346-4f83-a0bb-7df771d97579" />  

3. Configurer les paramètres de base pour chaque équipement :<br>
   
   - Désactiver la recherche DNS (`no ip domain lookup`)<br>
     
   - Définir un nom d’hôte  <br>
     
   - Ajouter un **message de bannière**  <br>
   
   - Configurer la console (`exec-timeout`, `logging synchronous`)  <br>
   
   - Sauvegarder la configuration (`copy running-config startup-config`)  <br>
   

### 📸 Exemple de configuration de R1 :
<p align="center">
  <img width="627" height="99" alt="image" src="https://github.com/user-attachments/assets/37ba0486-01c5-453d-a9e6-aad2580fb6e8" />
</p>


```bash
no ip domain lookup
hostname R1
line con 0
 exec-timeout 0 0
 logging synchronous
 exit
banner motd # This is R1, Inter-VLAN Routing Lab #
```

## 🧩 Partie 2 – Configuration et Vérification de l’Inter-VLAN Routing sur un Switch Layer 3

### ⚙️ Étapes :  

1. Configuration du routage inter-VLAN sur D1
   
   🔹 Activer le routage IPv4 et IPv6 et Création des VLANs
   
<p align="center">
<img width="585" height="100" alt="image" src="https://github.com/user-attachments/assets/768e682d-bcb1-4581-a6f2-83ad18981814" />
</p>

```bash
D1(config)# ip routing
D1(config)# ipv6 unicast-routing
D1(config)# vlan 50
D1(config-vlan)# name Group50
D1(config)# vlan 60
D1(config-vlan)# name Group60
```
🔹 Affectation des ports aux VLANs
<p align="center">
<img width="582" height="191" alt="image" src="https://github.com/user-attachments/assets/80d91da0-be5c-45ae-988d-1901d5d9e603" />
</p>

```bash
D1(config)# interface g1/0/23
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 50
D1(config-if)# no shutdown

D1(config)# interface g1/0/24
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 60
D1(config-if)# no shutdown
```
🔹 Création des interfaces virtuelles SVI
<p align="center">
<img width="580" height="274" alt="image" src="https://github.com/user-attachments/assets/c5bcca8c-d9f1-4d43-bb0a-b21aa76a93f9" />
</p>

```bash
D1(config)# interface vlan 50
D1(config-if)# ip address 10.2.50.1 255.255.255.0
D1(config-if)# ipv6 address fe80::d1:2 link-local
D1(config-if)# ipv6 address 2001:db8:acad:1050::d1/64
D1(config-if)# no shutdown

D1(config)# interface vlan 60
D1(config-if)# ip address 10.2.60.1 255.255.255.0
D1(config-if)# ipv6 address fe80::d1:3 link-local
D1(config-if)# ipv6 address 2001:db8:acad:1060::d1/64
D1(config-if)# no shutdown
```
🔹 Configuration des PC  
<p align="center">
<img width="526" height="208" alt="image" src="https://github.com/user-attachments/assets/039ec08f-3626-4837-8929-bdb50226203e" />
</p>  

| Appareil | Adresse IPv4       | Adresse IPv6                | Passerelle par défaut               |
|----------|-----------------|----------------------------|-----------------------------------|
| PC1      | 10.2.50.50/24   | 2001:db8:acad:1050::50/64 | 10.2.50.1 / 2001:db8:acad:1050::d1 |
| PC2      | 10.2.60.50/24   | 2001:db8:acad:1060::50/64 | 10.2.60.1 / 2001:db8:acad:1060::d1 |  

🔹 Vérification de la connectivité entre PC1 et PC2
<p align="center">
<img width="666" height="118" alt="image" src="https://github.com/user-attachments/assets/8c59a91d-ea6c-47b0-b89f-f981bfaa124d" />
</p>  

2. Configuration du port routé et des routes statiques sur D1  

   🔹 Conversion du port G1/0/11 en port routé  
   
<p align="center">
<img width="626" height="138" alt="image" src="https://github.com/user-attachments/assets/062fd752-ef4e-4dbc-9230-f37649f6df22" />
</p> 

```bash
D1(config)# interface g1/0/11
D1(config-if)# no switchport
D1(config-if)# ip address 10.1.13.13 255.255.255.0
D1(config-if)# ipv6 address fe80::d1:1 link-local
D1(config-if)# ipv6 address 2001:db8:acad:10d1::d1/64
D1(config-if)# no shutdown
```
   🔹 Configuration des routes par défaut  
<p align="center">
<img width="625" height="43" alt="image" src="https://github.com/user-attachments/assets/c3052fd4-f6b0-4b34-ab14-4cb9138c790d" />
</p>  

```bash
D1(config)# interface g1/0/11
D1(config-if)# no switchport
D1(config-if)# ip address 10.1.13.13 255.255.255.0
D1(config-if)# ipv6 address fe80::d1:1 link-local
D1(config-if)# ipv6 address 2001:db8:acad:10d1::d1/64
D1(config-if)# no shutdown
```
3. Configuration du routeur R1
   
   🔹 Configuration des interfaces
   <p align="center">
   <img width="620" height="212" alt="image" src="https://github.com/user-attachments/assets/479d00cc-1401-4b2c-b240-648fed2f2e74" />
</p>  

```bash
R1(config)# interface g0/0/1
R1(config-if)# ip address 10.1.13.1 255.255.255.0
R1(config-if)# ipv6 address fe80::1:1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:10d1::1/64
R1(config-if)# no shutdown

R1(config)# interface s0/1/1
R1(config-if)# ip address 10.1.3.1 255.255.255.0
R1(config-if)# ipv6 address fe80::1:2 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1013::1/64
R1(config-if)# no shutdown

```
   🔹 Configuration du routage statique  
   
<p align="center">
<img width="622" height="78" alt="image" src="https://github.com/user-attachments/assets/aa8e060d-ea7f-40d7-88cd-973972a160eb" />
</p>  

```bash
R1(config)# ip route 10.2.0.0 255.255.0.0 10.1.13.13
R1(config)# ipv6 route 2001:db8:acad:1050::/64 2001:db8:acad:10d1::d1
R1(config)# ipv6 route 2001:db8:acad:1060::/64 2001:db8:acad:10d1::d1
R1(config)# ip route 0.0.0.0 0.0.0.0 10.1.3.3
R1(config)# ipv6 route ::/0 2001:db8:acad:1013::3
```
   🔹 Vérification de la connectivité R1<-->PC2 
<p align="center">
<img width="653" height="97" alt="image" src="https://github.com/user-attachments/assets/06dd580b-9426-448c-8ce2-1524e9eb52df" />
</p> 
   
```bash
R1# ping 10.2.60.50
R1# ping 2001:db8:acad:1060::50
```
## ⚡ Partie 3 – Configuration et Vérification du Routage Inter-VLAN Basé sur un Routeur

### ⚙️ Étapes :  

1. Configuration des VLANs sur D2
   
   🔹 Création des VLANs et VLAN natif

<p align="center">
<img width="627" height="113" alt="image" src="https://github.com/user-attachments/assets/66d7df11-dfd4-48c2-8d7f-4970f481d284" />
</p> 

```bash
D2(config)# vlan 75
D2(config-vlan)# name Group75
D2(config)# vlan 85
D2(config-vlan)# name Group85
D2(config)# vlan 999
D2(config-vlan)# name NativeVLAN
```
   🔹 Attribution des ports aux VLANs  

<p align="center">
   <img width="624" height="188" alt="image" src="https://github.com/user-attachments/assets/3e71928a-f683-4d92-90dc-dc0ddd32b37a" />
</p> 
   
```bash
D2(config)# interface g1/0/23
D2(config-if)# switchport mode access
D2(config-if)# switchport access vlan 75
D2(config-if)# no shutdown

D2(config)# interface g1/0/24
D2(config-if)# switchport mode access
D2(config-if)# switchport access vlan 85
D2(config-if)# no shutdown
```

   🔹 Création de l’interface VLAN 75

<p align="center">
<img width="626" height="142" alt="image" src="https://github.com/user-attachments/assets/19bd0df7-c197-46b0-88a7-0e5d308c481a" />
</p> 
   
```bash
D2(config)# interface vlan75
D2(config-if)# ip address 10.3.75.14 255.255.255.0
D2(config-if)# ipv6 address fe80::d2:1 link-local
D2(config-if)# ipv6 address 2001:db8:acad:3075::d2/64
D2(config-if)# no shutdown
```

   🔹 Configuration du trunk vers R3  

<p align="center">
<img width="626" height="99" alt="image" src="https://github.com/user-attachments/assets/482497d5-e588-4640-ae6e-fd8b5a2b91ea" />
</p> 

```bash
D2(config)# interface g1/0/11
D2(config-if)# switchport mode trunk
D2(config-if)# switchport trunk native vlan 999
D2(config-if)# switchport trunk allowed vlan 75,85,999
D2(config-if)# no shutdown
```

2. Configuration des VLANs sur D2
  
   🔹 Configuration du trunk vers R3
   
```bash
   R3(config)# ipv6 unicast-routing
```

   🔹 Configuration des subinterfaces pour chaque VLAN  

<p align="center">
<img width="625" height="512" alt="image" src="https://github.com/user-attachments/assets/10797e78-078a-4a1d-a023-ddbc8428cf18" />
</p>

```bash
R3(config)# interface g0/0/1
R3(config-if)# no shutdown

R3(config)# interface g0/0/1.75
R3(config-subif)# encapsulation dot1q 75
R3(config-subif)# ip address 10.3.75.1 255.255.255.0
R3(config-subif)# ipv6 address fe80::3:2 link-local
R3(config-subif)# ipv6 address 2001:db8:acad:3075::1/64
R3(config-subif)# no shutdown

R3(config)# interface g0/0/1.85
R3(config-subif)# encapsulation dot1q 85
R3(config-subif)# ip address 10.3.85.1 255.255.255.0
R3(config-subif)# ipv6 address fe80::3:3 link-local
R3(config-subif)# ipv6 address 2001:db8:acad:3085::1/64
R3(config-subif)# no shutdown

R3(config)# interface g0/0/1.999
R3(config-subif)# encapsulation dot1q 999 native
R3(config-subif)# no shutdown
```

   🔹 Configuration des PC 3 et 4  

<p align="center">
<img width="516" height="197" alt="image" src="https://github.com/user-attachments/assets/35c30051-991d-462f-9b35-60cae39e0433" />
</p>

| Appareil | Adresse IPv4       | Adresse IPv6                | Passerelle par défaut               |
|----------|-----------------|----------------------------|-----------------------------------|
| PC3      | 10.3.75.50/24   | 2001:db8:acad:3075::50/64 | 10.3.75.1 / 2001:db8:acad:3075::1 |
| PC4      | 10.3.85.50/24   | 2001:db8:acad:3085::50/64 | 10.3.85.1 / 2001:db8:acad:3085::1 |

3. Configuration du routage statique pour la connectivité totale
  
   🔹 Configuration de l’interface S0/1/1 sur R3
   
<p align="center">
<img width="624" height="151" alt="image" src="https://github.com/user-attachments/assets/7ac15fd5-5a08-4d40-990a-a3d60f6da15f" />
</p>

```bash
R3(config)# interface s0/1/1
R3(config-if)# ip address 10.1.3.3 255.255.255.0
R3(config-if)# ipv6 address fe80::3:1 link-local
R3(config-if)# ipv6 address 2001:db8:acad:1013::3/64
R3(config-if)# no shutdown
```

   🔹 Configuration de la route par défaut  
   
<p align="center">
<img width="624" height="36" alt="image" src="https://github.com/user-attachments/assets/c5b15e22-ed3c-40dd-bd76-3124de298253" />
</p>

```bash
R3(config)# ip route 0.0.0.0 0.0.0.0 10.1.3.1
R3(config)# ipv6 route ::/0 2001:db8:acad:1013::1
```

   🔹 Vérification finale de la connectivité PC3 <--> PC2

<p align="center">
<img width="669" height="180" alt="image" src="https://github.com/user-attachments/assets/6c10c28a-4c46-4024-944a-8758918075dc" />
</p>

```bash
PC3> ping 10.2.60.50
PC3> ping 2001:db8:acad:1060::50
```

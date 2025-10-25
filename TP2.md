# 🧠 TP Réseau – Inter-VLAN Routing avec Cisco Packet Tracer

## 👨‍💻 Réalisé par
**Nom :** Zaid Hankri  
**Filière :** Développement Web & Réseau  
**Date :** Octobre 2025  

---

## 🎯 Objectif du TP
L’objectif de ce TP est de **configurer le routage inter-VLAN** entre plusieurs réseaux à l’aide d’un routeur et de switches Cisco, afin de permettre la communication entre différentes VLANs.

---

## 🧩 Topologie du réseau

Voici la topologie conçue sur **Cisco Packet Tracer** :

![Topologie du réseau](images/topology.png)

> *Capture d’écran de la topologie réseau dans Packet Tracer.*

---

## ⚙️ Configuration des équipements

### 🔹 1. Configuration du routeur (R1)
```bash
enable
configure terminal
hostname R1
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.10.10.1 255.255.255.0
 exit
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.20.20.1 255.255.255.0
 exit

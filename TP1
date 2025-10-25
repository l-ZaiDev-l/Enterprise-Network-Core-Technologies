# 🧠 TP Cisco Packet Tracer – Inter-VLAN Routing (Layer 3 & Router-based)

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

![Topologie du réseau](images/topology.png)

> *Topologie du réseau utilisée pour le TP (R1, R3, D1, D2, et 4 PC).*

---

## 🧱 Partie 1 – Configuration de Base des Équipements

### ⚙️ Étapes :
1. Câbler le réseau selon la topologie.

2. Configurer les paramètres de base pour chaque équipement :
   - Désactiver la recherche DNS (`no ip domain lookup`)
   - Définir un nom d’hôte
   - Ajouter un **message de bannière**
   - Configurer la console (`exec-timeout`, `logging synchronous`)
   - Sauvegarder la configuration (`copy running-config startup-config`)

### 📸 Exemple de configuration de R1 :
```bash
no ip domain lookup
hostname R1
line con 0
 exec-timeout 0 0
 logging synchronous
 exit
banner motd # This is R1, Inter-VLAN Routing Lab #



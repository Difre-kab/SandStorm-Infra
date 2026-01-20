# Plan VLAN et adressage IP

## 1. Vue d’ensemble

## 2. Tableau des VLAN

## 3. Plan d’adressage IP (172.20.0.0/16)

## 4. Segmentation par département

## 5. Contraintes d’accès (haut niveau)

| VLAN | Name            | Network        | Mask | VIP (HSRP)     | Notes |
|-----:|-----------------|----------------|------|---------------|-------|
| 99   | NET-MGMT        | 172.20.99.0    | /24  | 172.20.99.1   | Switch/AP/Infrastructure management |
| 110  | SERVERS         | 172.20.1.0     | /24  | 172.20.1.1    | AD/DNS/DHCP/File/Print/WSUS |
| 200  | DMZ             | 172.20.200.0   | /24  | 172.20.200.1  | WEB (.10) / MAIL (.20) |
| 10   | HR              | 172.20.10.0    | /24  | 172.20.10.1   | Separated (required) |
| 20   | SALES           | 172.20.20.0    | /24  | 172.20.20.1   | Separated from Finance |
| 21   | FINANCE         | 172.20.21.0    | /24  | 172.20.21.1   | Separated from Sales |
| 30   | SOCIAL-PR       | 172.20.30.0    | /24  | 172.20.30.1   | Department VLAN |
| 40   | AUDIO-VISUAL    | 172.20.40.0    | /24  | 172.20.40.1   | Department VLAN |
| 50   | PRODUCTION      | 172.20.50.0    | /23  | 172.20.50.1   | ~200 users |
| 60   | PROGRAMMING     | 172.20.60.0    | /23  | 172.20.60.1   | ~120 users |
| 70   | WRITERS         | 172.20.70.0    | /24  | 172.20.70.1   | Department VLAN |
| 80   | RnD             | 172.20.80.0    | /24  | 172.20.80.1   | Department VLAN |
| 90   | TEST-QA         | 172.20.90.0    | /24  | 172.20.90.1   | Separated (required) |
| 91   | BETA-TESTERS    | 172.20.91.0    | /24  | 172.20.91.1   | Internet-only |
| 92   | ALPHA-TESTERS   | 172.20.92.0    | /26  | 172.20.92.1   | ~20 users |
| 100  | IT              | 172.20.100.0   | /24  | 172.20.100.1  | RDP authorized to all |
| 101  | MANAGED-DEVICES | 172.20.101.0   | /24  | 172.20.101.1  | Managed IT devices |
| 140  | MANAGEMENT      | 172.20.140.0   | /27  | 172.20.140.1  | VPN authorized (phase 3) |
| 130  | WIFI-CORP       | 172.20.130.0   | /24  | 172.20.130.1  | Corporate WiFi |
| 131  | WIFI-GUEST      | 172.20.131.0   | /24  | 172.20.131.1  | Internet-only guests |
| 120  | VOICE           | 172.20.120.0   | /22  | 172.20.120.1  | >650 IP phones |


- Le plan d’adressage repose sur le bloc privé 172.20.0.0/16.
- Chaque VLAN dispose d’un sous-réseau dédié et non chevauchant.
- Les tailles de sous-réseaux sont adaptées aux besoins :
  - /22 pour la VoIP
  - /23 pour les départements à forte densité
  - /26 et /27 pour les périmètres restreints
- L’adresse VIP HSRP est systématiquement positionnée en .1.


La segmentation réseau est définie selon les directives explicites du client afin de garantir la sécurité, la séparation des privilèges et la protection des données sensibles.

Les réseaux suivants doivent être isolés de manière logique via des VLANs dédiés :
- Ressources Humaines (HR)
- Sales et Finance
- Testing et QA
- Management
- Réseaux WiFi
- Département IT
- Managed IT Devices

L’ensemble de la segmentation est réalisé à l’intérieur de la plage d’adressage privée 172.20.0.0/16.

Les équipements réseau locaux (commutateurs et routeurs) doivent supporter cette segmentation et permettre l’application des politiques de sécurité associées. Le design prévoit également une topologie de basculement (failover) lorsque cela est possible afin d’assurer la continuité de service.

Le dimensionnement des VLANs est basé sur le nombre d’utilisateurs par département :
- Production : environ 200 utilisateurs
- Testing et QA : environ 200 utilisateurs (incluant Alpha, Beta et QA)
- Programming : environ 120 utilisateurs
- Audio-Visual et Social Media / PR : environ 60 utilisateurs chacun
- HR, Sales, Finance, Writers et R&D : entre 20 et 60 utilisateurs
- Management : 2 utilisateurs

Cette segmentation permet de limiter les accès inter-départements, de réduire la surface d’attaque et de garantir un contrôle centralisé par l’équipe IT.


Les contraintes d’accès réseau sont définies selon les règles fonctionnelles suivantes :

- Le département IT dispose d’un accès transversal à l’ensemble des VLANs afin d’assurer l’administration et le support.
- L’accès RDP vers les autres réseaux est strictement réservé au VLAN IT.
- Les réseaux Alpha-Testers, Beta-Testers et WiFi Guest sont limités à un accès Internet uniquement via NAT.
- Les départements Management et IT sont les seuls autorisés à accéder au réseau via VPN.
- Les réseaux internes sensibles (HR, Finance, Sales, R&D) ne sont pas accessibles depuis les réseaux invités ou de test.
- La DMZ est accessible depuis Internet mais reste isolée du réseau interne.

Ces contraintes définissent les intentions de sécurité et serviront de base aux règles de filtrage détaillées lors des phases suivantes.


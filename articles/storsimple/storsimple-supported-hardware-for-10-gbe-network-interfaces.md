<properties 
   pageTitle="Interfaces matériel pour la solution de 10 StorSimple | Microsoft Azure"
   description="Décrit les se (SFP) enfichable pris en charge compacts, câbles et commutateurs pour les interfaces réseau solution 10 sur votre appareil StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Matériel pris en charge pour les interfaces réseau solution 10 sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des informations sur le matériel supplémentaire qui fonctionne avec votre appareil Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Liste des appareils testés par Microsoft

Microsoft a testé la suivantes se de (SFP) enfichable compacts, câbles et commutateurs pour vérifier qu’ils fonctionnent de façon optimale avec des appareils. (Les tableaux suivants seront mise à jour comme nouveau matériel est testé.)

### <a name="sfp-transceivers"></a>Se SFP +

|Rendre|Modèle|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>Câbles

|S. Non. |Rendre|Modèle|
|---|---|---|
| 1.|Cisco|SFP-H10GB-CU1M|
| 2.|Cisco|SFP-H10GB-CU2M|
| 3.|Cisco|SFP-H10GB-CU3M|
| 4.|Tripp légère|N820 - 05M (OM3)|

### <a name="switches"></a>Commutateurs

|S. Non.|Rendre|Modèle|
|---|---|---|
| 1. |Cisco|N3K-C3172PQ-10GE|
| 2. |Cisco|N3K-C3048-ZM-F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Liste des appareils testés dans le champ

Cette section contient la liste des périphériques qui ont été déployées dans le champ par les clients StorSimple. Ces n’ont pas été testés par Microsoft, mais sont susceptibles de travailler avec votre appareil StorSimple.
 
| Paramètre                         | Valeur                                    |
|-----------------------------------|------------------------------------------|
| Commutateur rendre                     | Juniper                                  |
| Commutateur de modèle                    | ex4550 32F                               |
| Version du système d’exploitation commutateur | JunOS 12.3R9.4                           |
| Modèle de carte                     | Ports intégrée (PIC 0)                    |
| Création de l’émetteur récepteur                  | Juniper                                  |
| Modèle émetteur/récepteur               | Numéro de référence 740 021308 <br></br> Numéro de référence 740 030658                   |
| Version du logiciel émetteur récepteur    | Révision 01 Version 0.0 (signalées)            |
| Modèle de câble                     | Recto-verso cavalier LC/LC 50/125µ, OM3, LSZH |
| Modèle de StorSimple                | 8600                                     |
| Version du logiciel StorSimple     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Liste des appareils testés par fournisseur OEM (Mellanox)  

Mellanox a testé la suivantes se de (SFP) enfichable compacts, câbles et commutateurs pour vérifier qu’ils fonctionnent de façon optimale avec les interfaces de réseau Mellanox tels que les interfaces de réseau solution 10 sur votre appareil StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Câbles et les modules pris en charge par Mellanox 

Le tableau suivant répertorie les câbles et les modules pris en charge par Mellanox. Ces n’ont pas été testés par Microsoft, mais sont susceptibles de travailler avec votre appareil StorSimple.

| S. Non. | Vitesse | Modèle                 | Description                                            | Rendre                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | Solution 10| CAB-SFP-SFP - 1M        | câble cuivre passif SFP + 10 Go/s 1m                   | Arista                |
| 2.     | Solution 10| CAB-SFP-SFP - 2M        | câble cuivre passif SFP + 10 Go/s 2m                   | Arista                |
| 3.     | Solution 10| CAB-SFP-SFP - 3M        | câble cuivre passif SFP + 10 Go/s 3m                   | Arista                |
| 4.     | Solution 10| CAB-SFP-SFP - 5M        | câble cuivre passif SFP + 10 Go/s 5m                   | Arista                |
| 5.     | Solution 10| Cisco SFP-H10GBCU1M   | Câble SFP + Cisco                                       | Cisco                 |
| 6.     | Solution 10| Cisco SFP-H10GBCU3M   | Câble SFP + Cisco                                       | Cisco                 |
| 7.     |Solution 10 | Cisco SFP-H10GBCU5M   | Câble SFP + Cisco                                       | Cisco                 |
| 8.     | Solution 10| HP J9281B X242 10 G    | SFP + SFP + 1m DAS câble cuivre             | HP                    |
| 9.     | Solution 10| 455883 B21 HP BLc     | 10 Go SR SFP + cesser d’utiliser                                       | HP                    |
| 10.    | Solution 10| 455886 B21 HP BLc     | 10 Go longue distance SFP + cesser d’utiliser                                       | HP                    |
| 11.    |Solution 10 | 487649 B21 HP BLc     | Câble en cuivre SFP + 0,5 m 10GbE                           | HP                    |
| 12.    |Solution 10 | 487652 B21 HP BLc     | Câble en cuivre SFP + 1m 10GbE                             | HP                    |
| 13.    |Solution 10 | 487655 B21 HP BLc     | Câble en cuivre SFP + 3m 10GbE                             | HP                    |
| 14.    |Solution 10 | 487658 B21 HP BLc     | Câble en cuivre SFP + 7m 10GbE                             | HP                    |
| 15.    |Solution 10 | 537963 B21 HP BLc     | Câble en cuivre SFP + 5m 10GbE                             | HP                    |
| 16.    |Solution 10 | HP AP784A             | Câble série C passif cuivre SFP + 3m                  | HP                    |
| 17.    |Solution 10 | HP AP785A             | Câble série C passif cuivre SFP + 5m                  | HP                    |
| 18.    |Solution 10 | HP AP818A             | 1m série B Active SFP cuivre + câble                   | HP                    |
| 19.    |Solution 10 | HP AP819A             | série B Active SFP cuivre + câble 3m                   | HP                    |
| 20.    |Solution 10 | HP J9150A             | X132 10 G SFP + LC SR émetteur récepteur                        | HP                    |
| 21.    |Solution 10 | HP J9151A             | X132 10 G SFP + LC longue distance émetteur récepteur                        | HP                    |
| 22.    |Solution 10 | HP J9283B             | X242 10 G SFP + SFP + 3 m DAC câble                        | HP                    |
| 23.    |Solution 10 | HP J9285B             | X242 10 G SFP + SFP + 7 m DAC câble                        | HP                    |
| 24.    | Solution 10| HP JD095B             | X240 10 G SFP + SFP + 0,65 DAC câble m                     | HP                    |
| 25.    | Solution 10| HP JD096B             | X240 10 G SFP + SFP + 1,2 m DAC câble                      | HP                    |
| 26.    | Solution 10| HP JD097B             | X240 10 G SFP + SFP + 3 m père câble                        | HP                    |
| 27.    | Solution 10| MAM1Q00A QSA Mellanox | QSFP à SFP + carte                                   | Technologies Mellanox |
| 28.    | Solution 10| MC2309124-006 Mt      | Câble cuivre passif 1 x SFP+ à QSFP 10 Go/s 24awg 7 m   | Technologies Mellanox |
| 29.    | Solution 10| MC2309124-007 Mt      | Câble cuivre passif 1 x SFP+ à QSFP 10 Go/s 24awg 7 m   | Technologies Mellanox |
| 30.    | Solution 10| MC2309130-003 Mt      | Câble cuivre passif 1 x SFP+ à QSFP 10 Go/s 30awg 3 m   | Technologies Mellanox |
| 31.    | Solution 10| MC2309130 00A Mt      | Câble cuivre passif 1 x SFP+ à QSFP 10 Go/s 30awg 0,5 m | Technologies Mellanox |
| 32.    | Solution 10| MC3309124-005 Mt      | Cuivre passif câble 1 24awg de 10 Go/s x SFP+ 5 m           | Technologies Mellanox |
| 33.    | Solution 10| MC3309124-007 Mt      | Cuivre passif câble 1 24awg de 10 Go/s x SFP+ 7 m           | Technologies Mellanox |
| 34.    | Solution 10| MC3309130-003 Mt      | Cuivre passif câble 1 30awg de 10 Go/s x SFP+ 3 m           | Technologies Mellanox |
| 35.    | Solution 10| MC3309130 00A Mt      | Cuivre passif câble 1 30awg de 10 Go/s x SFP+ 0,5 m         | Technologies Mellanox |

### <a name="switches-supported-by-mellanox"></a>Commutateurs pris en charge par Mellanox 

Le tableau suivant répertorie les commutateurs pris en charge par Mellanox. Ces n’ont pas été testés par Microsoft, mais sont susceptibles de travailler avec votre appareil StorSimple.

| S. Non. | Vitesse | Modèle | Description                                                         | Rendre              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | 10GbE | 516733-B21  | Commutateur de carte HP ProCurve 6120XG 10GbE Ethernet                      | HP    |
| 2.     | 10GbE | 538113-B21  | Module de passerelle 10GbE HP (PTM)                                  | HP    |
| 3.     | 10GbE | EN4093      | IBM PureFlex système TISSU EN4093 10Gigabit commutateur Scalable Module | IBM   |
| 4.     | 1GbE  | 3020        | Carte de commutateur Cisco Catalyst 3020 1GbE                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Carte de commutateur Cisco Catalyst 3020 X 1GbE                              | Cisco |
| 6.     | 1GbE  | 438030-B21  | Module de commutateur 1GbE HP - GbE2c couche 2/3 Ethernet carte basculer       | HP    |
| 7.     | 1GbE  | 6120G       | Carte de commutateur HP ProCurve 6120G/XG 1GbE                              | HP    |

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les composants matériels StorSimple et l’état](storsimple-monitor-hardware-status.md).

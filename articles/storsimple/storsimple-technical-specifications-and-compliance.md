<properties 
   pageTitle="Spécifications techniques StorSimple | Microsoft Azure"
   description="Décrit les spécifications techniques et les informations de conformité des normes pour les composants matériels StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Spécifications et conformité pour le périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Les composants matériels de votre appareil Microsoft Azure StorSimple respectent les spécifications techniques et des normes décrites dans cet article. Les spécifications techniques décrivent la puissance et refroidissement Modules (PCM pour), disques, capacité de stockage et les pièces jointes. Les informations de conformité traitent des éléments tels que des normes internationales, sécurité et ainsi que câblage.


## <a name="power-and-cooling-module-specifications"></a>Spécifications Power et Module  

Le périphérique StorSimple a deux 100 240V double ventilateurs, compatible SB Power refroidissement Modules (PCM pour). Cela fournit une configuration d’alimentation redondants. Si un PCM échoue, le périphérique continue à fonctionner normalement sur l’autres PCM jusqu'à ce que le module défectueux est remplacé.  

Le boîtier EBOD utilise un PCM W 580 et encadrement primaire un PCM W 764. Les tableaux suivants répertorient les spécifications techniques associées aux PCMs.

| Spécification           | 580 W PCM (EBOD)                                    | 764 W PCM (primaire)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Puissance de sortie maximale    | 580 W                                               | 764                                                |
| Fréquence               | 50/60Hz                                            | 50/60Hz                                           |
| Sélection de plage tension | Automatique allant : 90-264 V CA, 47/63 Hz               | Automatique allant : 90-264 V CA, 47/63 Hz               |
| Maximale courant  | 20 A                                                | 20 A                                               |
| Correction du facteur de puissance | > tension d’entrée nominale 95 %                          | > tension d’entrée nominale 95 %                         |
| Harmonique               | Répond à EN61000-3-2                                   | Répond à EN61000-3-2                                  |
| Sortie                  | Tension de mode veille 5 v @ 2.0 A                          | Tension de mode veille 5 v @ 2.7 A                         |
|                         | + 5 v @ 42 A                                          | + 5 v @ 40 A                                         |
|                         | + 12 v @ 38 A                                         | + 12 v @ 38 A                                        |
| Chaud           |  Oui                                                | Oui                                                |
| Commutateurs et voyants       | Commutateur/OFF AC et indicateur d’état quatre voyants     | Commutateur/OFF AC et indicateur d’état six voyants     |
| Encadrement refroidissement       | Axial ventilateurs avec contrôle de la vitesse des ventilateurs variable  | Axial ventilateurs avec contrôle de la vitesse des ventilateurs variable |

 
## <a name="power-consumption-statistics"></a>Statistiques de consommation électrique  

Le tableau suivant répertorie les données de consommation power classique (valeurs réelles peuvent varier à partir de la publiés) pour les différents modèles d’un périphérique StorSimple. 
 
 Conditions | 240 V CA | 240 V CA | 240 V CA | 110 V CA | 110 V CA | 110 V CA 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Lecteurs lentes, ventilateurs inactives | 1,45 A  |0,31 kW | 1057.76 BTU/h | 3.19 A | 0.34 kW | 1160.13 BTU/h 
 Lecteurs lentes, ventilateurs accéder à | 1,54 A | 0,33 kW | 1126.01 BTU/h | 3.27 A | 0,36 kW | 1228.37 BTU/h 
 Ventilateurs, unités rapides inactives, deux panneau optimisé | 2.14 A | 0,49 kW  | 1671.95 BTU/h | 4,99 A | 0.54 kW | 1842.56 BTU/h 
 Ventilateurs rapides, lecteurs inactif, une unité d’alimentation optimisé une inactives | 2.05 A | 0.48 kW | 1637.83 BTU/h | 4.58 A | 0,50 kW | 1706.07 BTU/h 
 Unités rapides, ventilateurs accédez, deux panneau optimisé | 2.26 A | 0.51 kW | 1740.19 BTU/h | 4,95 A | 0.54 kW | 1842.56 BTU/h 
 Ventilateurs rapides, lecteurs accéder à, une unité d’alimentation optimisé à une inactives | 2.14 A |0,49 kW | 1671.95 BTU/h | 4.81 A  | 0,53 kW | 1808.44 BTU/h 

## <a name="disk-drive-specifications"></a>Spécifications de lecteur de disque  

Votre appareil StorSimple prend en charge jusqu'à 12 disques série joint SCSI (sa) facteur 3,5 formulaire. Les lecteurs réels peuvent être un mélange de SSD (SSDs) ou disques durs (disques durs), en fonction de la configuration du produit. Les emplacements de lecteur de disque 12 se trouvent dans une configuration de 3 par 4 devant le boîtier. Le boîtier EBOD permet de stockage supplémentaire pour un autre 12 lecteurs de disques. Il s’agit toujours disques durs.  

## <a name="storage-specifications"></a>Spécifications de stockage
Les appareils StorSimple disposer d’une combinaison de lecteurs de disques durs et SSD pour la 8100 et 8600. La capacité totale utilisable pour la 8100 et 8600 sont environ 15 et 38 To respectivement. Le tableau suivant répertorie les détails de SSD, disque dur et la capacité de cloud dans le contexte de la capacité de la solution StorSimple.

| Modèle d’appareil / capacité                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Nombre de lecteurs de disques durs (disques durs)              |   8                                                     |  19                                                     |
| Nombre de SSD (SSDs)            |   4                                                     |  5                                                      |
| Capacité du disque dur unique                            |   4 TO                                                  |  4 TO                                                   |
| Capacité SSD unique                            |   400 GO                                                |  800 GO                                                 |
| Capacités                                 |   4 TO                                                  | 4 TO                                                    |
| Capacité du disque dur                            | TO 14                                                   | 36 TO                                                   |
| Capacité SSD                            | 800 GO                                                  | 2 TO                                                    |
| Capacité utilisable total *                         | ~ 15 TO                                                 | ~ 38 TO                                                 |
| Capacité maximale solution (y compris le cloud)    | 200 TO                                                  | 500 TO                                                  |


<sup> * </sup> -  *La capacité totale utilisable inclut la capacité disponible pour les données, les métadonnées et buffers.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensions encadrement et limites de poids  

Les tableaux suivants répertorient les différentes spécifications encadrement pour les dimensions et épaisseur.  

### <a name="enclosure-dimensions"></a>Dimensions de l’encadrement
Le tableau suivant répertorie les dimensions du boîtier en millimètres et en pouces.

|Encadrement |Millimètres |Pouces |
|----------|------------|-------| 
| Hauteur |87,9 | 3,46 |
| Largeur entre le bord de montage | 483 | 19.02 |
| Largeur entre le corps du boîtier | 443 | 17.44 |
| Axe de profondeur à partir du bord de montage à extrémité du corps de l’encadrement | 577 | 22.72 |
| Axe de profondeur Panneau de configuration opérations à extrémité plus éloignée d’encadrement | 630.5 | 24.82 |
| Axe de profondeur à partir du bord de montage à extrémité plus éloignée d’encadrement |   603 | 23.74 |

### <a name="enclosure-weight"></a>Poids encadrement  

Selon la configuration, un boîtier principal entièrement chargé peut évaluer de 21 à 33 kg et nécessite deux personnes gérer. 
 
| Encadrement | Poids |
|-----------|--------| 
| Poids maximal (dépend de la configuration) |30 kg – 33 kg |
| Vide (sans lecteurs ajustées) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Caractéristiques de l’environnement encadrement  

Cette section répertorie les spécifications relatives à l’environnement du boîtier. La température, humidité, altitude, chocs, vibrations, orientation, sécurité et compatibilité électromagnétique (CEM) sont inclus dans cette catégorie.  

### <a name="temperature-and-humidity"></a>Température et humidité

| Encadrement        | Plage de température  | Ambiance humidité relative | Température humide maximale   |
|------------------|----------------------------|---------------------------|--------------------|
| Opérationnel      | TEMPÉRATURE DE 5-35° C (41° F - 95° F)    | 20-80 % non-condensation- | 28° C (82° F)        |
| Non opérationnels  | -TEMPÉRATURE DE 40 À 70° C (40° F - 158° F) | 5-100 % sans condensation  | 29° C (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Air, altitude, chocs, vibrations, orientation, sécurité et EMC
 
| Encadrement          | Spécifications opérationnelles                                                |
|--------------------|---------------------------------------------------------------------------| 
| Flux d’air            | Air est avant vers l’arrière. Système doit être géré avec une installation basse pression et d’échappement arrière. Créé par rack portes et obstacles pression ne doit pas dépasser 5 pascals (colonne d’eau 0,5 mm). |
| Altitude, opérationnel  | -30 mètres à 3045 mètres-100 à 10 000 pieds avec température de fonctionnement maximale valeur nominale de 5 ° C au-dessus pieds 7000. |
| Altitude, non opérationnel  | mètres-305 à 12 192 mètres-1,000 à 40 000 pieds |
| Shock, opérationnel  | sinus de 10 ms ½ 5g |
| Shock, non opérationnel  | sinus de 10 ms ½ 30g |
| Vibrations, opérationnelle  | 0.21g RMS 5 et 500 Hz aléatoire |
| Vibrations, non opérationnel  | 1,04 g RMS 2 200 Hz aléatoire |
| Vibrations, déplacement  | sinus de 2 200 Hz 3g |
| Orientation et montage  | montage en rack 19"(2 unités EIA) |
| Rails de rack  | Pour l’ajuster à la profondeur minimale 700 mm (31.50 pouces) racks conformes 297 IEC |
| Sécurité et approbations  |   CE et UL US 61000-3, CEI 61000-3, UL 61000-3 |
| EMC  | EN55022 (DONT - A), FCC A |

## <a name="international-standards-compliance"></a>Conformité aux normes internationaux
Votre périphérique Microsoft Azure StorSimple est conforme aux normes internationales suivantes :  

- CE - FR 60950-1  
- Rapport de bloc de contrôle pour IEC 60950-1  
- UL et cUL UL 60950-1  

## <a name="safety-compliance"></a>Conformité de sécurité  

Votre périphérique Microsoft Azure StorSimple remplit les évaluations de sécurité suivantes :  

- Approbation de type de produit système : UL, cUL, CE  
- Conformité de sécurité : UL 60950, IEC 60950, EN 60950  

## <a name="emc-compliance"></a>Conformité EMC 

Votre appareil Microsoft Azure StorSimple répond aux évaluations EMC suivantes.  

### <a name="emissions"></a>Émissions

Le périphérique est compatible avec EMC pour émission effectuées et radiée.  

- Émissions effectués sur le nombre de niveaux : CFR 47 partie 15 classe A EN55022 Classe A dont classe A  
- Émissions radiées le nombre de niveaux : CFR 47 partie 15 classe A EN55022 Classe A dont classe A   

### <a name="harmonics-and-flicker"></a>Harmonique et Flickr  

L’appareil est conforme à EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Niveaux d’immunité limite  
L’appareil est conforme à EN55024.  

## <a name="ac-power-cord-compliance"></a>Conformité de cordon d’alimentation CA
  
Du plug-in et l’assembly de cordon d’alimentation complète doivent respecter les normes appropriées pour le pays dans lequel l’appareil est utilisé, et ils doivent disposer d’autorisations de sécurité sont acceptables dans ce pays. Les tableaux suivants répertorient les normes pour l’Europe et aux États-Unis.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Câbles d’alimentation AC - USA (doit être NRTL répertorié)

| Composant       | Spécification                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Type de cordon       | Ed ou SVT, 18 AWG minimum, 3 conducteurs, longueur maximale mètres 2.0 |
| Plug-in            | Plug-in NEMA 5-15P terre type pièce jointe évaluation 120 V, 10 A ; ou IEC 320 C14, 250 V, 10 A |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

### <a name="ac-power-cords---europe"></a>Câbles d’alimentation AC - Europe

| Composant       | Spécification                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Type de cordon       | Harmoniser, H05-VVF-3G1.0                                         |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

## <a name="supported-network-cables"></a>Câbles réseau pris en charge  

Pour les interfaces de réseau solution 10, données 2 et 3 de données, reportez-vous à la [liste des câbles réseau prises en charge et les modules](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à déployer un appareil StorSimple dans votre centre de données. Pour plus d’informations, voir [déployer votre appareil local](storsimple-deployment-walkthrough-u2.md).  

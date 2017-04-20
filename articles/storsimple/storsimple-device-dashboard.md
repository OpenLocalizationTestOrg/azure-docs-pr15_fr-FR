<properties
   pageTitle="Utiliser le tableau de bord device Manager StorSimple | Microsoft Azure"
   description="Décrit le tableau de bord périphérique de service Manager StorSimple et comment l’utiliser pour afficher des indicateurs de stockage et initiateurs connectés et trouver le numéro de série et le nom qualifié."
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

# <a name="use-the-storsimple-manager-device-dashboard"></a>Utiliser le tableau de bord device Manager StorSimple

## <a name="overview"></a>Vue d’ensemble

Le tableau de bord device Manager StorSimple vous donne un aperçu des informations pour un périphérique StorSimple spécifique, contrairement au tableau de bord de service, qui vous donne des informations sur l’ensemble des appareils inclus dans votre solution Microsoft Azure StorSimple.

![Page de tableau de bord du périphérique](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Le tableau de bord contient les informations suivantes :

- **Zone de graphique** , vous pouvez voir les métriques de stockage correspondante dans la zone du graphique en haut du tableau de bord. Dans ce graphique, vous pouvez afficher les mesures pour le stockage principal total (la quantité de données écrites par des hôtes sur votre appareil) et le stockage cloud total utilisé par votre appareil sur une période donnée.

     Dans ce contexte, fait référence à la quantité de données écrites par l’hôte de *stockage principal* et peut être arrêtée vers le bas en type de volume : *stockage hiérarchisé principal* inclut des données stockées localement et données hiérarchisé vers le cloud ; *primaire localement épinglées stockage* inclut uniquement les données stockées localement. *Stockage cloud*, est quant à eux, une mesure du montant total des données stockées dans le cloud. Cela inclut des sauvegardes et hiérarchisée des données. Notez que les données stockées dans le nuage sont dédupliquées et compressées, alors que le stockage principal indique la quantité d’espace de stockage utilisé avant que les données sont dédupliquées et compressées. (Vous pouvez comparer ces deux nombres pour avoir une idée du taux de compression). Pour les deux principaux et stockage cloud, les montants affichés dépend de la fréquence de suivi que vous configurez. Par exemple, si vous choisissez une fréquence d’une semaine, le graphique affiche des données pour chaque jour de la semaine précédente.

     Vous pouvez configurer le graphique comme suit :

     - Pour afficher la quantité de stockage cloud consommée au fil du temps, sélectionnez l’option de **Stockage CLOUD utilisé** . Pour afficher l’espace de stockage total qui a été écrite par l’hôte, sélectionnez les options **Primaire hiérarchisé stockage utilisé** et **primaire localement ÉPINGLÉES stockage** . Dans l’illustration, les deux options sont sélectionnées ; Par conséquent, le graphique affiche les montants de stockage pour le stockage principal et cloud. Notez que n’importe quel espace de stockage principal utilisé avant d’installer la mise à jour 2 est représenté par la ligne **Primaire hiérarchisé stockage utilisé** .
     - Utilisez le menu déroulant dans le coin supérieur droit du graphique pour spécifier une période de temps 1 semaine, 1 mois, 3 mois ou 1 an. Notez que le graphique de niveau supérieur est actualisé qu’une seule fois par jour et par conséquent refléteront totaux de celle du jour précédent.

     Pour plus d’informations, voir [utiliser le service Manager StorSimple pour surveiller votre appareil StorSimple](storsimple-monitor-device.md).

- **Vue d’ensemble de l’utilisation** – dans la zone **vue d’ensemble de l’utilisation** , vous voyez la quantité d’espace de stockage principal utilisé, la quantité de stockage mis en service et la capacité maximale de stockage pour votre appareil. En comparant ces nombres de l’utilisation de la quantité maximale de stockage disponible, vous pouvez voir en un clin de œil si vous avez besoin d’espace de stockage supplémentaire. Notez que cette vue d’ensemble est mis à jour toutes les 15 minutes et, en raison de la différence de la fréquence de mise à jour, peut afficher différents nombres celle affichée dans la zone de graphique ci-dessus, qui est mis à jour tous les jours. Pour plus d’informations, voir [utiliser le service Manager StorSimple pour surveiller votre appareil StorSimple](storsimple-monitor-device.md).


- **Alertes** – la zone **alertes** contient une vue d’ensemble des alertes pour votre appareil. Alertes sont regroupées par gravité et un compteur est fourni du nombre d’alertes pour chaque niveau de gravité. Cliquez sur la gravité d’alerte pour ouvrir une vue dans l’étendue de l’onglet alertes pour afficher uniquement les alertes de ce niveau gravité pour cet appareil.

- **Tâches** – le module **projets** affiche le résultat de l’activité tâche récente. Cela peut vous assurer que le système fonctionne comme prévu, ou il peut vous informer que vous devez les corriger. Pour afficher plus d’informations sur les tâches terminées récemment, cliquez sur **tâches a réussi dans les dernières 24 heures**.

- La zone **coup de œil rapide** sur la droite du tableau de bord fournit des informations utiles telles que le modèle d’appareil, numéro de série, état, la description et nombre de volumes.

Vous pouvez également configurer le basculement et afficher initiateurs connectés à partir du tableau de bord appareil.

Les tâches courantes qui peuvent être effectuées sur cette page sont :

- Afficher les initiateurs connectés

- Trouver le numéro de série d’appareil

- Trouver la cible appareil nom qualifié

## <a name="view-connected-initiators"></a>Afficher les initiateurs connectés

Vous pouvez afficher les initiateurs qui sont connectés à votre appareil en cliquant sur le lien **Afficher connectés initiateurs** affichée dans la zone **Aperçu rapide** de votre tableau de bord appareil. Cette page fournit une liste sous forme de tableau des initiateurs avez réussi à connecter à votre appareil. Pour chaque initiateur, vous pouvez voir :

- L’iSCSI complet nom (nom qualifié) de l’auteur connecté.

- Le nom de l’enregistrement de contrôle d’accès (blocage) qui permet de cet initiateur connecté.

- L’adresse IP de l’auteur connecté.

- Les interfaces réseau qui l’initiateur est connecté à votre périphérique de stockage. Celles-ci peuvent varier de données 0 à 5 de données.

- Tous les volumes l’initiateur connecté est autorisé à accéder en fonction de la configuration de blocage actuelle.

Si vous voyez initiateurs inattendus dans cette liste ou celles attendus n’êtes pas visible, passez en revue la configuration de votre blocage. Un maximum de 512 initiateurs peuvent se connecter à votre appareil.

## <a name="find-the-device-serial-number"></a>Trouver le numéro de série d’appareil

Vous devrez peut-être le numéro de série appareil lorsque vous configurez Microsoft chemins multiples / (o) sur l’appareil. Procédez comme suit pour rechercher le numéro de série d’appareil.

#### <a name="to-find-the-device-serial-number"></a>Pour trouver le numéro de série d’appareil

1. Accédez à **appareils** > **tableau de bord**.

2. Dans le volet droit du tableau de bord, recherchez la zone **coup de œil** .

3. Faites défiler vers le bas et recherchez le numéro de série.

## <a name="find-the-device-target-iqn"></a>Trouver la cible appareil nom qualifié

Vous devrez peut-être la cible du périphérique nom qualifié lorsque vous configurez le défi négociation d’authentification Protocol () sur votre appareil StorSimple. Procédez comme suit pour rechercher la cible du périphérique nom qualifié.

### <a name="to-find-the-device-target-iqn"></a>Pour trouver la cible du périphérique nom qualifié

1. Accédez à **appareils** > **tableau de bord**.

1. Dans le volet droit du tableau de bord, recherchez la zone **coup de œil** .

1. Faites défiler vers le bas, puis recherchez la cible du nom qualifié.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [tableau de bord de service Manager StorSimple](storsimple-service-dashboard.md).
- En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

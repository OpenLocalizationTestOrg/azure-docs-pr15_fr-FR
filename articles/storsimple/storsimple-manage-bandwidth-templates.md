<properties
   pageTitle="Gérer vos modèles de bande passante StorSimple | Microsoft Azure"
   description="Décrit comment gérer les modèles de bande passante StorSimple, qui vous permettent de contrôler la bande passante."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utiliser le service StorSimple Manager pour gérer les modèles de bande passante StorSimple

## <a name="overview"></a>Vue d’ensemble

Modèles de bande passante permettent de configurer l’utilisation de la bande passante réseau entre plusieurs planifications heure du jour en cascade les données à partir de l’appareil StorSimple dans le cloud.

Avec les planifications de bande passante, vous pouvez :

- Spécifier des plannings de bande passante personnalisée selon les utilisations de réseau la charge de travail.

- Gestion centralisée et réutiliser les plannings sur plusieurs périphériques de manière transparente et facile.

> [AZURE.NOTE] Cette fonctionnalité est disponible uniquement pour les périphériques physiques StorSimple et non pour les périphériques virtuels.

Tous les modèles de bande passante de votre service sont affichées dans un format tabulaire et contiennent les informations suivantes :

- **Nom** : un nom unique attribué au modèle de bande passante lors de sa création.

- **Planification** – le nombre de planifications contenus dans un modèle de bande passante donnée.

- **Utilisé par** : le nombre de volumes à utiliser les modèles de bande passante.

La page Gestionnaire de StorSimple service **configurer** dans le portail classique Azure vous permet de gérer les modèles de bande passante.

Vous pouvez également trouver des informations supplémentaires pour aider à configurer les modèles de bande passante dans :

- Questions et réponses à propos des modèles de bande passante
- Meilleures pratiques pour les modèles de bande passante

## <a name="add-a-bandwidth-template"></a>Ajouter un modèle de bande passante

Procédez comme suit pour créer un modèle de bande passante.

#### <a name="to-add-a-bandwidth-template"></a>Pour ajouter un modèle de bande passante

1. Dans la page de **configuration** du service StorSimple Manager, cliquez sur **Ajouter/modifier la bande passante modèle**.

2. Dans la boîte de dialogue **Ajouter/modifier la bande passante modèle** :

   1. Dans la liste déroulante **modèle** , sélectionnez **Créer nouveau** pour ajouter un nouveau modèle de bande passante.
   2. Spécifiez un nom unique pour votre modèle de bande passante.

3. Définir une **planification de la bande passante**. Pour créer une planification :

   1. Dans la liste déroulante, sélectionnez les jours de la semaine que n’est configuré pour l’échéancier. Vous pouvez sélectionner plusieurs jours en activant les cases à cocher situées avant les jours correspondantes dans la liste.
   2. Sélectionnez l’option **Toute la journée** si la planification est appliquée pour la journée entière. Lorsque cette option est activée, vous ne pouvez plus spécifier une **Heure de début** ou une **Heure de fin**. Exécution de la planification de 12:00:00 à 11:59 PM.
   3. Dans la liste déroulante, sélectionnez une **Heure de début**. Il s’agit lorsque commence à l’échéancier.
   4. Dans la liste déroulante, sélectionnez une **Heure de fin**. Il s’agit lorsque l’Échéancier s’arrête.

         > [AZURE.NOTE] Planifications qui se chevauchent ne sont pas autorisées. Si les heures de début et de fin entraîne un chevauchement, vous verrez un message d’erreur à cet effet.

   5. Indiquez le **taux de bande passante**. Il s’agit de la bande passante en mégabits par seconde (Mbps) utilisé par votre périphérique StorSimple opérations impliquant le cloud (chargements et téléchargements). Fournir un nombre compris entre 1 et 1 000 pour ce champ.

   6. Cliquez sur l’icône ![icône](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Le modèle que vous avez créés est ajouté à la liste des modèles de bande passante dans la page **configuration** de service.

    ![Créer le nouveau modèle de bande passante](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Cliquez sur **Enregistrer** dans la partie inférieure de la page, puis sur **Oui** lorsque vous êtes invité à confirmer. Cela sera enregistrée les modifications de configuration que vous avez apportées.

## <a name="edit-a-bandwidth-template"></a>Modifier un modèle de bande passante

Effectuez les opérations suivantes pour modifier un modèle de bande passante.

### <a name="to-edit-a-bandwidth-template"></a>Pour modifier un modèle de bande passante

1. Cliquez sur **Ajouter/modifier la bande passante modèle**.

2. Dans la boîte de dialogue **Ajouter/modifier la bande passante modèle** :

   1. Dans la liste déroulante **modèle** , choisissez un modèle de bande passante existant que vous souhaitez modifier.
   2. Effectuez vos modifications. (Vous pouvez modifier les paramètres existants).
   3. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Vous verrez le modèle modifié dans la liste des modèles de bande passante dans la page Configuration de service.

3. Pour enregistrer vos modifications, cliquez sur **Enregistrer** dans la partie inférieure de la page. Cliquez sur **Oui** lorsque vous êtes invité à confirmer.

> [AZURE.NOTE] Vous ne pas être autorisé à enregistrer vos modifications si l’échéancier modifié chevauche une planification existante dans le modèle de bande passante que vous modifiez.

## <a name="delete-a-bandwidth-template"></a>Supprimer un modèle de bande passante

Procédez comme suit pour supprimer un modèle de bande passante.

#### <a name="to-delete-a-bandwidth-template"></a>Pour supprimer un modèle de bande passante

1. Dans la liste des modèles de bande passante de votre service sous forme de tableau, sélectionnez le modèle que vous souhaitez supprimer. Une icône Supprimer (**x**) s’affiche à l’extrême droite du modèle sélectionné. Cliquez sur l’icône **x** pour supprimer le modèle.

2. Vous demandera confirmation. Cliquez sur **OK** pour continuer.

Si le modèle est utilisé par les volumes, vous ne pas être autorisé à supprimer. Vous verrez un message d’erreur indiquant que le modèle est en cours d’utilisation. Une boîte de dialogue de message d’erreur s’affiche vous indiquant que toutes les références au modèle doivent être supprimées.

Vous pouvez supprimer toutes les références au modèle en accédant à la page **Volume conteneurs** et en modifiant les conteneurs de volume qui utilisent ce modèle afin qu’ils utilisent un autre modèle ou un paramètre de bande passante personnalisé ou illimité. Lorsque toutes les références ont été supprimées, vous pouvez supprimer le modèle.

## <a name="use-a-default-bandwidth-template"></a>Utiliser un modèle de bande passante par défaut

Un modèle de bande passante par défaut est fourni et est utilisé par les conteneurs de volume par défaut pour appliquer des contrôles de la bande passante lors de l’accès le cloud. Le modèle par défaut sert également une référence prête pour les utilisateurs de créer leurs propres modèles. Les détails de ce modèle par défaut sont les suivantes :

- **Nom** – illimité nuit et les week-ends

- **Planification** – une planification unique du lundi au vendredi qui s’applique un taux de bande passante de 1 Mbps entre 8 AM et PM 5 appareil. La bande passante est définie sur illimité pour le reste de la semaine.

Le modèle par défaut peut être modifié. L’utilisation de ce modèle (y compris les versions modifiées) est suivie.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Créer un modèle de bande passante sur une journée entière qui commence à une certaine date

Suivez cette procédure pour créer un calendrier qui commence à un moment donné et s’exécute toute la journée. Dans l’exemple, l’échéancier démarre à 9 le matin et s’exécute jusqu'à 9 h du matin. Il est important de noter que les heures de début et de fin pour une planification donnée doit se trouver sur le même calendrier 24 heures et ne peut pas s’étalent sur plusieurs jours. Si vous devez configurer des modèles de bande passante qui s’étalent sur plusieurs jours, vous devrez utiliser plusieurs calendriers (comme illustré dans l’exemple).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Pour créer un modèle de bande passante sur une journée entière

1. Créer une planification qui commence à 9 le matin et s’exécute jusqu'à minuit.

2. Ajouter une autre planification. Configurer la planification deuxième exécuter à partir de minuit jusqu’au 9 AM le matin.

3. Enregistrer le modèle de bande passante.

La planification composite puis démarre à la fois de votre choix et exécuter une journée entière.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Questions et réponses à propos des modèles de bande passante

**Q**. Que se passe-t-il à des contrôles de la bande passante lorsque vous êtes en séparant les plannings ? (Une planification terminée et une autre n’a pas encore commencé.)

**A**. Dans ce cas, nous utiliserons sans contrôle de la bande passante. Cela signifie que le périphérique peut utiliser la bande passante illimitée lors de la hiérarchisation des données dans le cloud.

**Q**. Modifier des modèles de bande passante sur un appareil en mode hors connexion ?

**A**. Vous ne pourrez pas modifier les modèles de bande passante sur les conteneurs de volumes si le périphérique correspondant est en mode hors connexion.

**Q**. Peut modifier un modèle de bande passante associé à un conteneur de volume lorsque les volumes associés sont en mode hors connexion ?

**A**. Vous pouvez modifier un modèle de bande passante associé à un conteneur de volume dont les volumes sont en mode hors connexion. Notez que lorsque les volumes sont en mode hors connexion, aucune donnée ne sera en cascade à partir de l’appareil dans le cloud.

**Q**. Vous pouvez supprimer un modèle par défaut ?

**A**. Mais vous pouvez supprimer un modèle par défaut, elle n’est pas recommandé pour le faire. L’utilisation d’un modèle par défaut, y compris les versions modifiées, est suivie. Les données de suivi sont analysées et au fil du temps, sont utilisées pour améliorer le modèle par défaut.

**Q**. Comment déterminer que vos modèles de bande passante doivent être modifiées ?

**A**. Un des signes que vous devez modifier les modèles de bande passante est lorsque vous démarrez s’affichent le réseau ralentir ou effet Maigri plusieurs fois par jour. Dans ce cas, surveiller le réseau de stockage et l’utilisation en consultant les graphiques les performances et débit du réseau.

À partir des données de débit réseau, identifiez l’heure du jour et les conteneurs de volume dans lequel la critique réseau se produit. Si cela se produit lorsque des données sont en cours hiérarchisées vers le cloud (obtenir ces informations à partir de performances e/s pour tous les conteneurs de volume pour l’appareil vers le cloud), vous devez modifier les modèles de bande passante associés à votre conteneurs en volume.

Une fois que les modèles modifiés sont en cours d’utilisation, vous devrez surveiller le réseau pour la latence significative. Si ces persistent, vous devez revoir vos modèles de bande passante.

**Q**. Que se passe-t-il si vous ont plusieurs conteneurs de volume sur mon appareil planifie qui se chevauchent, mais différentes limites s’appliquent à chaque ?

**A**. Supposons que vous disposez d’un appareil avec 3 conteneurs de volume. Les programmes associés à ces conteneurs complètement se chevauchent. Pour chacun de ces conteneurs, les limites de bande passante utilisés sont respectivement 5, 10 et 15 Mbits/s. Lorsqu’e/s se produisent sur tous ces conteneurs en même temps, la valeur minimale de 3 limites de bande passante peut-être être appliquée : dans ce cas, 5 Mbps que celles-ci sortant requêtes partager la même file d’attente.

## <a name="best-practices-for-bandwidth-templates"></a>Meilleures pratiques pour les modèles de bande passante

Suivez ces conseils pratiques pour votre appareil StorSimple :

- Configurer les modèles de bande passante sur votre appareil pour activer la variable de la limitation du débit réseau par le périphérique à des moments différents de la journée. Ces modèles de bande passante lorsqu’il est utilisé avec des plannings de sauvegarde peuvent exploiter efficacement la bande passante réseau supplémentaires pour les opérations de cloud en dehors des heures.

- Calculer la bande passante réelle nécessaire pour un déploiement spécifique en fonction de la taille du déploiement et la récupération nécessaires heure objectifs de.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

<properties
pageTitle="Utiliser des données à partir d’une base de données SQL Server en local dans l’apprentissage automatique | Azure"
description="Utiliser des données à partir d’une base de données SQL Server en local pour effectuer analytique avancé avec apprentissage automatique Azure."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Effectuer analytique avancé avec apprentissage automatique Azure à l’aide de données à partir d’une base de données SQL Server en local


Souvent les entreprises qui fonctionnent avec les données locales souhaite tirer parti de l’échelle et souplesse du cloud pour leur apprentissage charges de travail de l’ordinateur. Mais ils ne voulez pas interrompre leurs processus métier actuels et le flux de travail en déplaçant leurs données locales au cloud. Azure apprentissage automatique prend désormais en charge la lecture de vos données à partir d’une base de données SQL Server en local et formation puis score un modèle avec ces données. Vous n’avez plus manuellement copier et synchroniser les données entre le cloud et votre serveur local. En revanche, le module **Importer des données** dans Azure Machine apprentissage Studio peut désormais lire directement à partir de votre base de données SQL Server en local pour votre formation et les travaux de score. 

Cet article fournit une vue d’ensemble de l’entrée en local de données SQL server dans apprentissage automatique Azure. Il part du principe que vous avez l’habitude de concepts d’apprentissage automatique Azure tels que les espaces de travail, des modules, jeux de données, expériences, *etc.*..

> [AZURE.NOTE] Cette fonctionnalité n’est pas disponible pour les espaces de travail gratuits. Pour plus d’informations sur les tarifs d’apprentissage automatique et les niveaux, voir [Azure Machine apprentissage tarifs](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Installer la passerelle de gestion des données de Microsoft

Pour accéder à une base de données SQL Server en local dans l’apprentissage automatique Azure, vous devez télécharger et installer la passerelle de gestion des données Microsoft. Lorsque vous configurez la connexion de la passerelle dans Machine apprentissage Studio vous aurez la possibilité de télécharger et installer la passerelle à l’aide de la boîte de dialogue **téléchargement et la passerelle de données de Registre** décrite ci-dessous.

Vous pouvez également installer la passerelle de gestion des données à l’avance en téléchargeant et en exécutant le package d’installation MSI à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Sélectionnez la dernière version, sélectionnez 32 bits ou 64 bits en fonction de votre ordinateur. Le fichier MSI peut également servir à mettre à niveau d’une passerelle de gestion des données existantes vers la dernière version, avec tous les paramètres sont conservés.

La passerelle comporte les conditions préalables suivantes :

- Les versions de système d’exploitation Windows pris en charge sont Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.
- La configuration recommandée pour l’ordinateur passerelle est au moins de 2 GHz, 4 cœurs, 8 Go de RAM et disque 80 Go.
- Si l’ordinateur hôte en veille prolongée, la passerelle ne pourrez pas répondre aux demandes de données. Par conséquent, configurez un plan d’alimentation sur l’ordinateur avant d’installer la passerelle. L’installation de la passerelle affiche un message si l’ordinateur est configuré pour la mise en veille prolongée.
- Étant donné que l’activité de copie se produit à une fréquence spécifique, l’utilisation des ressources (processeur, mémoire) sur l’ordinateur également respecte la même avec pointe et temps d’inactivité. L’utilisation des ressources dépend également intensément la quantité de données est déplacées. Lorsque plusieurs tâches sont en cours vous allez observer l’utilisation des ressources remonter pendant les heures de pointe. Pendant la configuration minimale ci-dessus est suffisante techniquement, vous souhaiterez peut-être avoir une configuration avec davantage de ressources que la configuration minimale requise en fonction de votre charge spécifique pour le déplacement de données.

Vous devez prendre en compte ce qui suit lors de la configuration et à l’aide d’une passerelle de gestion des données :

- Vous pouvez installer qu’une seule instance de la passerelle de gestion des données sur un seul ordinateur.
- Vous pouvez utiliser une passerelle unique pour plusieurs sources de données locale.
- Vous pouvez vous connecter plusieurs passerelles sur des ordinateurs différents à la même source de données locale.
- Vous configurez une passerelle pour qu’un seul espace de travail à la fois. Passerelles ne peut pas être partagés entre les espaces de travail pour le moment.
- Vous pouvez configurer plusieurs passerelles pour un seul espace de travail. Par exemple, vous souhaiterez peut-être utiliser une passerelle qui est connectée à votre test des sources de données au cours du développement et une passerelle de production lorsque vous êtes prêt à effectuent.
- La passerelle n’a pas besoin d’être sur le même ordinateur que la source de données, mais rester plus près de la source de données réduit la durée de la passerelle pour vous connecter à la source de données. Nous vous conseillons d’installer la passerelle sur un ordinateur qui est différent de celui qui héberge la source de données locales afin que la passerelle et source de données ne compétition pour les ressources.
- Si vous disposez déjà d’une passerelle installée sur votre ordinateur service Power BI ou Azure Data Factory scénarios, installer une passerelle séparée pour apprentissage automatique Azure sur un autre ordinateur. 

    > [AZURE.NOTE] Vous ne pouvez pas exécuter la passerelle de gestion des données et Power BI passerelle sur le même ordinateur.

- Vous devez utiliser la passerelle de gestion des données d’apprentissage automatique Azure même si vous utilisez Azure ExpressRoute pour d’autres données. Vous devez considérer votre source de données comme source de données locale (qui est protégé par un pare-feu) même lorsque vous utilisez ExpressRoute et utilisez la passerelle de gestion des données pour établir la connectivité entre apprentissage automatique et la source de données. 

Vous trouverez des informations détaillées sur les conditions préalables d’installation, des étapes d’installation et des conseils de dépannage dans l’article, de [déplacer des données entre sources locales et nuage avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), à partir de la section, [Considérations relatives à l’aide de la passerelle de gestion des données à](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Données de pénétration de votre base de données SQL Server en local dans apprentissage automatique Azure


Dans cette procédure pas à pas, vous configurer une passerelle de gestion des données dans un espace de travail d’apprentissage automatique Azure, configurez-le et puis lire les données d’une base de données SQL Server locale.

> [AZURE.TIP] Avant de commencer, désactiver le bloqueur de fenêtres de votre navigateur pour `studio.azureml.net`. Si vous utilisez le navigateur Google Chrome, téléchargez et installez parmi les différentes plug-ins disponibles auprès de Google Chrome WebStore, [Cliquez sur une seule fois application Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Étape 1 : Créer une passerelle

La première étape consiste à créer et configurer la passerelle pour accéder à votre base de données SQL locale.

1. Connectez-vous à [Azure Machine apprentissage Studio](https://studio.azureml.net/Home/) et sélectionnez l’espace de travail que vous voulez travailler dans.

2. Cliquez sur la carte de **paramètres** sur la gauche, puis cliquez sur l’onglet **Passerelles de données** dans la partie supérieure.

3. Cliquez sur **Nouvelle passerelle de données** dans la partie inférieure de l’écran.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. Dans la boîte de dialogue **nouvelle passerelle de données** , entrez le **Nom de la passerelle** et ajoutez éventuellement une **Description**. Cliquez sur la flèche dans le coin inférieur droit pour accéder à l’étape suivante de la configuration.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. Dans le téléchargement et Registre passerelle boîte de dialogue données, copiez la clé d’inscription passerelle dans le Presse-papiers.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Si vous n’avez pas encore téléchargé et installé la passerelle de gestion des données Microsoft, puis cliquez sur **Télécharger la passerelle de gestion des données**. Cela vous permet d’accéder à du Microsoft Download Center où vous pouvez sélectionner la version de passerelle que vous avez besoin, téléchargez-le et installez-le. Vous pouvez trouver des informations détaillées sur les conditions préalables d’installation, des étapes d’installation et des conseils de dépannage dans les sections de début de l’article [déplacer des données entre sources locales et nuage avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Une fois la passerelle est installée, le Gestionnaire de Configuration de passerelle de gestion des données s’ouvre et la boîte de dialogue **Enregistrer la passerelle** est affichée. Collez la **Clé d’inscription passerelle** que vous avez copiée dans le Presse-papiers et cliquez sur **Enregistrer**.

8. Si vous disposez déjà d’une passerelle installée, exécuter le Gestionnaire de Configuration de passerelle de gestion des données et cliquez sur **Modifier la clé**, collez la  **Clé d’inscription passerelle** que vous avez copiée dans le Presse-papiers, cliquez sur **OK**.

9. Une fois l’installation terminée, la boîte de dialogue **Enregistrer la passerelle** pour le Gestionnaire de Configuration de passerelle Microsoft données gestion s’affiche. Collez la clé d’inscription passerelle que vous avez copiée dans le Presse-papiers ci-dessus, puis cliquez sur **Enregistrer**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. La configuration de la passerelle est terminée lorsque les valeurs suivantes sont définis sous l’onglet **accueil** dans le Gestionnaire de Configuration de passerelle Microsoft données gestion :

    - **Nom de la passerelle** et **nom de l’Instance** sont définis sur le nom de la passerelle.

    - **L’enregistrement** est défini sur **inscrit**.

    - **Statut** est défini sur **démarré**.

    - La barre d’état en bas affiche **connecté au Service de nuage de passerelle de gestion des données** avec une coche verte.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure Machine apprentissage Studio également est mis à jour lors de l’enregistrement a réussi.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. Dans la boîte de dialogue **télécharger et inscrire la passerelle de données** , activez la case à cocher pour terminer l’installation. La page **paramètres** affiche l’état de la passerelle sous la forme « Online ». Dans le volet de droite, vous trouverez statut et autres informations utiles.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. Dans le commutateur Gestionnaire de Configuration de passerelle gestion des données Microsoft à l’onglet **certificats** . Le certificat spécifié sous cet onglet permet de chiffrement/déchiffrement des informations d’identification pour le magasin de données locales que vous spécifiez dans le portail. Il s’agit le certificat par défaut créé. Microsoft recommande cette modification à votre propre certificat que vous effectuez une sauvegarde dans votre système de gestion des certificats. Cliquez sur **Modifier** pour utiliser votre propre certificat à la place.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (facultatif) Si vous voulez activer la journalisation détaillée afin de résoudre les problèmes de la passerelle, dans le Gestionnaire de Configuration de passerelle données gestion Microsoft basculez vers l’onglet **Diagnostics** et cochez l’option **Activer la journalisation détaillée aux fins de dépannage** . Vous trouverez les informations de journalisation dans l’Observateur d’événements Windows sous **Services journaux des Applications et**  - &gt; nœud de **Passerelle de gestion des données** . Vous pouvez également utiliser l’onglet **Diagnostics** pour tester la connexion à une source de données locale à l’aide de la passerelle.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

La passerelle configurer processus dans l’apprentissage automatique Azure est terminée.
Vous êtes maintenant prêt à utiliser vos données locales.

Vous pouvez créer et configurer plusieurs passerelles dans Studio pour chaque espace de travail. Par exemple, vous devrez une passerelle de votre choix pour vous connecter à vos sources de données de test pendant le développement et une passerelle différente de vos sources de données de production. Azure apprentissage automatique vous donne la possibilité de configurer plusieurs passerelles en fonction de votre environnement d’entreprise. Actuellement, vous ne pouvez pas partager une passerelle entre les espaces de travail et qu’une seule passerelle peut être installée sur un seul ordinateur. Pour plus d’informations sur l’installation de la passerelle, consultez [Considérations relatives à l’aide de la passerelle de gestion des données à](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) l’article, [déplacer des données entre sources locales et nuage avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Étape 2 : Utilisation de la passerelle pour lire les données à partir d’une source de données locale

Après avoir configuré la passerelle, vous pouvez ajouter un module **Importer des données** à une expérience dont les données à partir de la base de données SQL Server en local est une entrée.

1.  Dans Studio d’apprentissage Machine, cliquez sur l’onglet **expériences** , cliquez sur **+ Nouveau** dans le coin inférieur gauche et sélectionnez **Expérience vide** (ou sélectionnez une des différentes expériences sur des échantillons disponibles).

2.  Rechercher et faites glisser le module **Importer des données** à la zone de dessin expérience.

3.  Cliquez sur **Enregistrer en tant que** sous la zone de dessin. Entrez « Azure Machine apprentissage local SQL Server didacticiel » pour le nom de l’expérience, sélectionnez l’espace de travail, puis cliquez sur la coche **OK** .

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Cliquez sur le module **Importer des données** pour le sélectionner, puis dans le volet de **Propriétés** à droite de la zone de dessin, sélectionnez « Base de données locale SQL » dans la liste déroulante **source de données** .

5.  Sélectionnez la **passerelle de données** installé et enregistré. Vous pouvez configurer une autre passerelle en sélectionnant « (Ajouter nouveau passerelle données...) ».

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Entrez le **nom du serveur de base de données** et SQL **nom de la base de données**, ainsi que la SQL **requête de base de données** que vous souhaitez exécuter.

7.  Cliquez sur **les valeurs d’entrée** sous **nom d’utilisateur et mot de passe** et entrez vos informations d’identification de base de données. Vous pouvez utiliser l’authentification Windows ou l’authentification SQL Server selon la manière dont est configuré votre SQL Server locale.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    Le message « valeurs requises » remplacera par « jeu de valeurs » avec une coche verte. Vous devez simplement taper les informations d’identification une seule fois, sauf si les informations de base de données ou le mot de passe change. Azure apprentissage automatique utilise le certificat que vous avez fourni lorsque vous avez installé la passerelle pour chiffrer les informations d’identification dans le cloud. Azure stockera jamais les informations d’identification locales sans chiffrement.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Cliquez sur **exécuter** pour exécuter l’expérience.

Une fois que l’expérience se termine en cours d’exécution vous pouvez visualiser les données que vous avez importé à partir de la base de données en cliquant sur le port de sortie du module **Importer des données** et en sélectionnant **visualiser**.

Une fois que vous avez terminé de développer votre expérience, vous pouvez déployer et mettre votre modèle. Au moyen du Service de l’exécution du lot, les données à partir de la base de données SQL Server en local configuré dans le module **Importer les données** sont lues, mais utilisées pour le score. Vous pouvez utiliser le Service de réponse de demande de données locales de notation, Microsoft recommande l’utilisation [complément Excel](machine-learning-excel-add-in-for-web-services.md) à la place. Pour l’instant, écriture dans un SQL Server local base de données via **Exporter des données** n'est pas pris en charge dans votre expériences ou les services web publiés.


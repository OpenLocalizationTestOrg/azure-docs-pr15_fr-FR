<properties 
    pageTitle="Publier apprentissage automatique service à Azure Marketplace web | Microsoft Azure" 
    description="Comment publier votre Service Web de Azure Machine régulièrement vos connaissances sur la Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Publier le Service Web de formation Machine Azure vers Azure Marketplace 

La Azure Marketplace offre la possibilité de publier des services web apprentissage automatique Azure comme payée ou libérer des services pour la consommation par les clients externes. Cet article fournit une vue d’ensemble de ce processus avec des liens vers des instructions pour vous aider à démarrer. À l’aide de ce processus, vous pouvez rendre vos services web disponible pour les développeurs de consommer dans leurs applications.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Vue d’ensemble de la publication 

Voici la procédure de publication d’un service web d’apprentissage automatique Azure dans Azure Marketplace :

1. Créer et publier un service Machine apprentissage requête-réponse (enregistrements de ressource)
2. Déployez le service en production et obtenir les informations de point de terminaison clé d’API et OData.
3. Utiliser l’URL du service web publiées pour publier sur [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. Une fois envoyé, votre offre est examinée et doit être approuvés avant vos clients pouvez commencer à acheter. Le processus de publication peut prendre quelques jours ouvrés. 

## <a name="walk-through"></a>Passez en revue
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Étape 1 : Créer et publier un service Machine apprentissage requête-réponse (enregistrements de ressource)###
 Si vous n'avez pas encore fait, veuillez consultez ce [expliquant en détail](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Étape 2 : Déployez le service en production et obtenir la clé d’API et les informations de point de terminaison OData###
1. À partir du [Portail classique Azure](http://manage.windowsazure.com), sélectionnez l’option **d’Apprentissage automatique** dans la barre de navigation gauche, puis sélectionnez votre espace de travail. 

2. Cliquez sur l’onglet **SERVICES WEB** , puis sélectionnez le service web que vous voulez publier sur le marché.

    ![Azure Marketplace][workspace]

3. Sélectionnez le point de terminaison que vous voulez avoir la place de marché consommer. Si vous n’avez pas créé les points de terminaison supplémentaires, vous pouvez sélectionner le point de terminaison **par défaut** .

4. Une fois que vous avez cliqué sur le point de terminaison, vous ne pourrez pas voir la **Clé d’API**. Vous avez besoin de cette partie des informations plus tard à l’étape 3, donc effectuer une copie de celui-ci.

    ![Azure Marketplace][apikey]

5. Cliquez sur la méthode de **Requête/réponse** , à ce stade, que nous ne prennent pas en charge les services d’exécution lot publication à la place de marché. Vous accédez alors à la page de l’aide de l’API de la méthode de requête/réponse.

6. Copiez l' **Adresse de point de terminaison OData**, vous devez ces informations ultérieurement à l’étape 3.

    ![Azure Marketplace][odata]




déployez le service en production.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Étape 3 : Utiliser l’URL du service web publiées pour publier sur Azure Marketplace (DataMarket)###

1.  Accédez à [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2.  Cliquez sur le lien **Publier** en haut de la page. Vous accédez alors au [Portail de publication de Microsoft Azure](https://publish.windowsazure.com)
3.  Cliquez sur la section **éditeurs** d’enregistrer en tant qu’éditeur.
4.  Lorsque vous créez une nouvelle offre, sélectionnez les **Services de données**, puis cliquez sur **créer un nouveau Service de données**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  Sous **Plans** fournissent des informations sur votre offre, notamment un plan de tarification. Décidez si vous propose un service gratuit ou payant. Pour obtenir payé, fournissent des informations de paiement tels que vos informations bancaires et taxes.

6.  Sous **Marketing** fournissent des informations sur votre offre, telles que le titre et une description pour votre offre.

7.  Sous **tarification** vous pouvez définir le prix de vos plans de certains pays ou laissez le système « autoprice » dans votre offre.

8. Sous l’onglet **Service de données** , cliquez sur **Service Web** comme **Source de données**.

    ![Azure Marketplace][image2]

9.  Obtenir la clé URL et API de service web à partir du portail classique Azure, comme expliqué à l’étape 2 ci-dessus.

10. Dans la boîte de dialogue Mise en Service de données Office Marketplace, collez l’adresse de point de terminaison OData dans la zone de texte **URL du Service** .

11. Pour l' **authentification**, sélectionnez **en-tête** en tant que le **Schéma d’authentification**.

    - Entrez « Autorisation » pour le **nom de l’en-tête**.
    - Pour la **Valeur de l’en-tête**, entrez « Porteur » (sans guillemets), cliquez sur la barre **d’espace** , puis collez la clé de l’API.
    - Activez la case à cocher **ce Service est OData** .
    - Cliquez sur **Tester la connexion** pour tester la connexion.

12. Sous **catégories**, vérifiez le **Qu'apprentissage automatique** est sélectionnée.

13. Lorsque vous avez terminé saisie de toutes les métadonnées concernant votre offre, cliquez sur **Publier**, puis **transmission mis en œuvre**. À ce stade, vous serez informé des problèmes restants dont vous avez besoin pour la résolution.

14. Une fois que vous avez vérifié achèvement de tous les problèmes en attente, cliquez sur **demander une approbation pour diffuser en Production**. Le processus de publication peut prendre quelques jours ouvrés. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

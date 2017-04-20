<properties
    pageTitle="Redimensionner une application dans Azure | Microsoft Azure"
    description="Découvrez comment mettre à l’échelle d’une application dans le Service d’application Azure pour ajouter des capacités et fonctionnalités."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="cephalin"/>

# <a name="scale-up-an-app-in-azure"></a>Redimensionner une application dans Azure #

Cet article vous explique comment adapter votre application dans Azure Application Service. Il existe deux flux de travail pour échelle mise à l’échelle, vers le haut et horizontale et cet article explique l’échelle de flux de travail.

- [Évoluer](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenez plus processeur, mémoire, espace disque et des fonctionnalités supplémentaires telles que des machines virtuelles dédiés (machines virtuelles), les domaines personnalisés, les certificats, les intermédiaires emplacements, autoscaling et bien plus encore. Évoluer en modifiant le niveau de tarification de l’offre de Service d’application appartenant à votre application.
- [Mise à l’échelle](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): augmenter le nombre d’instances de machine virtuelle qui s’exécutent votre application.
Vous pouvez intégrer jusqu'à jusqu'à 20 instances, selon votre niveau de tarification. [Environnements de Service d’application](../app-service/app-service-app-service-environments-readme.md) dans niveau **Premium** continueront à augmenter le nombre de horizontale à 50 instances. Pour plus d’informations sur la mise à l’échelle, voir le [nombre d’instances de l’échelle manuellement ou automatiquement](../monitoring-and-diagnostics/insights-how-to-scale.md). Il vous allez découvrir comment utiliser autoscaling, qui est à l’échelle du nombre d’instances automatiquement en fonction des plannings et des règles prédéfinies.

Les paramètres d’échelle ne prennent que quelques secondes pour appliquer et affecte toutes les applications dans votre [plan de services d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Ils ne nécessitent pas vous permettent de modifier votre code ou redéployez votre application.

Pour plus d’informations sur les tarifs et les fonctionnalités des offres de Service d’application individuels, voir les [Détails de tarification pour l’application Service](/pricing/details/web-sites/).  

> [AZURE.NOTE] Avant de transférer un plan de services d’application à partir de la couche **libre** , vous devez d’abord supprimer le [plafond](/pricing/spending-limits/) en place pour votre abonnement Azure. Pour afficher ou modifier les paramètres de votre abonnement Microsoft Azure Application Service, voir [Microsoft Azure abonnements][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Évoluer votre niveau de tarification

1. Dans votre navigateur, ouvrez le [portail Azure][portal].

2. Dans la carte de votre application, cliquez sur **tous les paramètres**, puis cliquez sur **Échelle vers le haut**.

    ![Accédez à évoluer votre application Azure.][ChooseWHP]

4. Choisissez votre niveau, puis cliquez sur **Sélectionner**.

    L’onglet **Notifications** clignote en vert **succès** une fois l’opération terminée.

<a name="ScalingSQLServer"></a>
## <a name="scale-related-resources"></a>Mettre à l’échelle des ressources connexes
Si votre application dépend d’autres services, tels que la base de données SQL Azure ou le stockage Azure, vous pouvez également évoluer ces ressources en fonction de vos besoins. Ces ressources ne sont pas mis à l’échelle avec le plan de services d’application et doivent être mise à l’échelle séparément.

1. Dans **Essentials**, cliquez sur le lien de **groupe de ressources** .

    ![Échelle de votre application Azure les ressources associées](./media/web-sites-scale/RGEssentialsLink.png)

2. Dans la partie **Résumé** de la cuillère de **groupe de ressources** , cliquez sur une ressource que vous voulez mettre à l’échelle. La capture d’écran suivante montre une ressource de base de données SQL et une ressource de stockage Azure.

    ![Accédez à la carte de groupe de ressources d’évoluer votre application Azure](./media/web-sites-scale/ResourceGroup.png)

3. Pour une ressource de base de données SQL, cliquez sur **paramètres** > **couche tarification** pour adapter le prix de niveau.

    ![Redimensionner la base de données SQL principale pour votre application Azure](./media/web-sites-scale/ScaleDatabase.png)

    Vous pouvez également activer [geo réplication](../sql-database/sql-database-geo-replication-overview.md) pour votre instance de base de données SQL.

    Pour une ressource de stockage Azure, cliquez sur **paramètres** > **Configuration** à l’échelle des options de stockage.

    ![Redimensionner le compte de stockage Azure utilisé par votre application Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>
## <a name="learn-about-developer-features"></a>En savoir plus sur les fonctionnalités pour les développeurs
Selon le niveau de prix, les fonctionnalités destinées aux développeurs suivantes sont disponibles :

### <a name="bitness"></a>Nombre de bits ###

- Les niveaux de **base**, **Standard**et **Premium** prend en charge les applications 32 bits et 64 bits.
- Les niveaux de plan de **partagé** et **Free** prend en charge uniquement aux applications 32 bits.

### <a name="debugger-support"></a>Débogage prise en charge ###

- Prise en charge débogueur est disponible pour les modes **Free** **partagé**et **simple** à une connexion par plan de services d’application.
- Prise en charge débogueur est disponible pour les modes **Standard** et **Premium** sur cinq connexions simultanées par plan de services d’application.

<a name="OtherFeatures"></a>
## <a name="learn-about-other-features"></a>En savoir plus sur les autres fonctionnalités

- Pour obtenir des informations détaillées sur tous les autres fonctionnalités dans les plans de services d’application, y compris les tarifs et fonctionnalités utiles pour tous les utilisateurs (y compris les développeurs), voir les [Détails de tarification pour l’application Service](/pricing/details/web-sites/).

>[AZURE.NOTE] Si vous voulez commencer avec le Service d’application Azure avant de vous inscrivez à un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751) où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte bancaire ne sont requis et il n’existe aucun engagements.

<a name="Next Steps"></a>
## <a name="next-steps"></a>Étapes suivantes

- Pour commencer avec Azure, voir [Version d’évaluation gratuite de Microsoft Azure](/pricing/free-trial/).
- Pour plus d’informations sur les tarifs, prise en charge et SLA, consultez les liens suivants.

    [Pour plus d’informations des transferts de données](/pricing/details/data-transfers/)

    [Plans d’assistance Azure Microsoft](/support/plans/)

    [Contrats de niveau de service](/support/legal/sla/)

    [Détails sur tarification pour la base de données SQL](/pricing/details/sql-database/)

    [Machine virtuelle et les tailles de Service Cloud pour Microsoft Azure][vmsizes]

    [Application Service tarifs détails](/pricing/details/app-service/)

    [Service d’application tarifs détails - connexions SSL](/pricing/details/web-sites/#ssl-connections)

- Pour plus d’informations sur le Service d’application Azure recommandations, y compris la création d’une architecture scalable et résistante, voir [meilleures pratiques : Azure Application Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Pour les vidéos sur la mise à l’échelle des applications de Service d’application, consultez les ressources suivantes :

    - [Quand à l’échelle des sites Web Azure - avec Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
    - [Mise à l’échelle de sites Web Azure, processeur ou planifiée - avec Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
    - [Comment Azure échelle de sites Web - avec Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)


<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

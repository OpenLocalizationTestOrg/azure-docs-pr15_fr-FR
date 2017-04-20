<properties
   pageTitle="Comment créer une demande d’assistance pour SQL Data Warehouse | Microsoft Azure"
   description="Comment créer une demande d’assistance dans SQL Azure Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Comment créer une demande d’assistance pour Data Warehouse SQL
 
Si vous rencontrez des problèmes avec votre Data Warehouse SQL, créez une prise en charge de billets afin que notre équipe technique peut vous aider.

## <a name="create-a-support-ticket"></a>Créer une demande d’assistance

1. Ouvrez le [portail Azure][].

2. Dans l’écran d’accueil, cliquez sur la vignette **aide + prise en charge** .

    ![Aide + prise en charge](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. Dans l’aide de + carte prise en charge, cliquez sur **demande de support technique de créer**.

    ![Nouvelle demande de support](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Sélectionnez le **Type de requête**.

    ![Type de requête](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Par défaut, chaque SQL server (par exemple, myserver.database.windows.net) possède un **Quota DTU** de 45 000. Ce quota est simplement une limite de sécurité. Vous pouvez augmenter votre quota en créant un tickets de support et en sélectionnant *Quota* comme type de requête. Pour calculer votre DTU doit, multipliez le 7.5 par le total [que DWU][] nécessaire. Par exemple, vous souhaitez héberger deux DW6000s sur un serveur SQL server, puis vous devez demander un quota DTU de 90 000.  Vous pouvez afficher votre consommation DTU actuelle à partir de la carte de serveur SQL dans le portail. Compter les bases de données en pause et reprises vers le quota DTU. 

5. Sélectionnez l' **abonnement** qui héberge la base de données avec le problème que vous signalez.

    ![Abonnement](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Sélectionnez **Data Warehouse SQL** en tant que la ressource.

    ![Ressource](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Sélectionnez votre [Azure prend en charge le plan][].

    - Support de **facturation, gestion de quota et l’abonnement** est disponible à tous les niveaux de prise en charge.
    - Prise en charge **pour le dépannage** est fourni par [développeur][], [Standard][], [Professionnel Direct][] ou assistance [Premier][] . Saut de résoudre les problèmes sont les problèmes rencontrés par les clients lors de l’utilisation Azure lorsqu’il existe une attente raisonnablement que Microsoft a provoqué le problème.
    - **Conseils pour les développeurs** et **les services de conseil** sont disponibles sur les niveaux de support [Premier][] et [Professionnel Direct][] . 
    
    Si vous avez souscrit prend en charge de plan, vous pouvez également signaler SQL Data Warehouse problèmes dans le [portail en ligne Microsoft Premier][]correspondants.  En savoir plus sur les différentes offres de prise en charge, y compris l’étendue, temps de réponse, les tarifs, etc., consultez [Azure prend en charge les plans][Azure prend en charge le plan] .  Pour les questions fréquemment posées sur Azure prise en charge, voir [Azure prend en charge des questions fréquentes][].  

    ![Plan d’assistance](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Sélectionnez le **Type de problème** et la **catégorie**.

    ![Catégorie de type de problème](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Décrire le problème et sélectionnez le niveau d’impact sur l’entreprise.

    ![Description du problème](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Vos **informations de contact** pour cette tickets de support sera renseignée. Mettre à jour si nécessaire.

    ![Informations de contact](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Cliquez sur **créer** pour envoyer la demande de support.


## <a name="monitor-a-support-ticket"></a>Surveiller une demande d’assistance

Une fois que vous avez soumis la demande de support, l’équipe de support Azure vous contactera. Pour vérifier votre statut de la demande et des détails, cliquez sur **Gérer les demandes d’assistance** dans le tableau de bord.

![Vérifier l’état](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Autres ressources

En outre, vous pouvez vous connecter à la Communauté SQL Data Warehouse [Débordement de pile][] ou sur le [forum MSDN de magasin de données de SQL Azure][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portail Azure]: https://portal.azure.com/
[Plan de support Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Pour les développeurs]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Direct Professionnel]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[FAQ sur l’assistance Azure]: https://azure.microsoft.com/support/faq/
[Portail en ligne Microsoft Premier]: https://premier.microsoft.com/
[Débordement de la pile]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Forum MSDN de magasin de données SQL Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


<properties 
    pageTitle="Créer et gérer les connexions hybride | Microsoft Azure" 
    description="Apprenez à créer une connexion hybride, gérer les connexions et installer le Gestionnaire de connexions hybride. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Créer et gérer les connexions hybride


## <a name="overview-of-the-steps"></a>Vue d’ensemble des étapes
1. Créer une connexion hybride en entrant le **nom d’hôte** ou le **nom de domaine complet** de la ressource locale dans votre réseau privé.
2. Lier vos applications web Azure ou les applications mobiles Azure à la connexion hybride.
3. Installer le Gestionnaire de connexions hybride sur la ressource locale et connectez-vous à la connexion hybride spécifique. Le portail Azure fournit une expérience d’un simple clic pour installer et se connecter.
4. Gérer les connexions hybride et leur clé de connexion.

Cette rubrique répertorie ces étapes. 

> [AZURE.IMPORTANT] Il est possible de définir un point de terminaison hybride connexion à une adresse IP. Si vous utilisez une adresse IP, vous pouvez ou ne pénètrent pas la ressource en local, en fonction de votre client. La connexion hybride dépend du client effectuant une recherche DNS. Dans la plupart des cas, le __client__ est votre code de l’application. Si le client n’effectue une recherche DNS, (il n’essaie pas de résoudre l’adresse IP comme s’il s’agissait d’un nom de domaine (x.x.x.x)), puis le trafic n’est pas envoyé via la connexion hybride.
>
> Par exemple (pseudocode), vous définissez **10.4.5.6** en tant que votre hôte local :
> 
> **Le scénario suivant fonctionne :**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Le scénario suivant ne fonctionne pas :**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Créer une connexion hybride

Une connexion hybride peut être créée dans le portail Azure à l’aide des applications Web **ou** à l’aide des Services BizTalk. 

**Pour créer des connexions hybride à l’aide des applications Web**, voir [Se connecter des applications Web Azure à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md). Vous pouvez également installer le Gestionnaire de connexion hybride (HCM) à partir de votre application web, qui est la méthode préférée. 

**Pour créer des connexions hybride dans les Services BizTalk**:

1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation gauche, sélectionnez **Services BizTalk** , puis sélectionnez votre BizTalk Service. 

    Si vous n’avez pas un BizTalk Service existant, vous pouvez [créer un BizTalk Service](biztalk-provision-services.md).
3. Sélectionnez l’onglet **Connexions hybride** :  
![Onglet Connexions hybride][HybridConnectionTab]

4. Sélectionnez **créer une connexion hybride** ou sélectionnez le bouton **Ajouter** dans la barre des tâches. Entrez les informations suivantes :

    Propriété | Description
--- | ---
Nom | Le nom de connexion hybride doit être unique et ne peut pas être le même nom que le BizTalk Service. Vous pouvez entrer n’importe quel nom mais être spécifiques avec son objectif. Voici quelques exemples :<br/><br/>Paie*SQL Server*<br/>SupplyList*SharepointServer*<br/>Clients*serveurOracle*
Nom d’hôte | Entrez le nom d’hôte complet, le nom d’hôte ou l’adresse IPv4 de la ressource locale. Voici quelques exemples :<br/><br/>mySQLServer<br/>*mySQLServer*. *Domaine*. corp*votresociété*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *votresociété*.com<br/>10.100.10.10<br/><br/>Si vous utilisez l’adresse IPv4, notez que votre code client ou une application peut ne pas résoudre l’adresse IP. Consultez la Remarque importante en haut de cette rubrique.
Port | Entrez le numéro de port de la ressource locale. Par exemple, si vous utilisez les applications Web, entrez le port 80 ou le port 443. Si vous utilisez SQL Server, entrez le port 1433.

5. Activez la case à cocher pour terminer l’installation. 

#### <a name="additional"></a>Supplémentaires

- Plusieurs connexions hybride peut être créées. Voir la [Services BizTalk : graphique des éditions](biztalk-editions-feature-chart.md) pour le nombre de connexions autorisées. 
- Chaque connexion hybride est créée avec une paire de chaînes de connexion : Application clés que clés envoyer et en local qui ÉCOUTENT. A pour chaque paire principale et une clé secondaire. 


## <a name="LinkWebSite"></a>Créer un lien votre Azure Application Service Web App ou une application Mobile

Pour lier une application Web ou une application Mobile dans le Service d’application Azure à une connexion hybride existante, sélectionnez **utiliser une connexion existante hybride** dans la carte de connexions hybride. Voir [accès local ressources à l’aide de connexions hybride dans le Service d’application Azure](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Installer le Gestionnaire de connexions hybrides en local

Après la création d’une connexion hybride, installez le Gestionnaire de connexions hybride sur la ressource locale. Il peut être téléchargé à partir de vos applications web Azure ou de votre BizTalk Service. Étapes de Services BizTalk : 

1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation gauche, sélectionnez **Services BizTalk** , puis sélectionnez votre BizTalk Service. 
3. Sélectionnez l’onglet **Connexions hybride** :  
![Onglet Connexions hybride][HybridConnectionTab]
4. Dans la barre des tâches, sélectionnez **Le programme d’installation locale**:  
![Programme d’installation locale][HCOnPremSetup]
5. Sélectionnez **installer et configurer** pour exécuter ou télécharger le Gestionnaire de connexions hybride sur le système local. 
6. Activez la case à cocher pour démarrer l’installation. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Supplémentaires
- Gestionnaire de connexions hybride peut être installé sur les systèmes d’exploitation suivants :

    - Windows Server 2008 R2 (.NET Framework 4.5 + et Windows Management Framework 4.0 + requis)
    - Windows Server 2012 (Windows Management Framework 4.0 + requis)
    - Windows Server 2012 R2


- Après avoir installé le Gestionnaire de connexions hybride, les événements suivants se produisent : 

    - La connexion hybride hébergés sur Azure est automatiquement configurée pour utiliser la chaîne de connexion Application principal. 
    - La ressource locale est configurée automatiquement pour utiliser la chaîne de connexion locale principal.

- Le Gestionnaire de connexions hybride doit utiliser une chaîne de connexion valide en local pour l’autorisation. Les applications Mobile Azure Web Apps doit utiliser une chaîne de connexion application valide pour l’autorisation.
- Vous pouvez mettre à l’échelle hybride connexions par l’installation d’une autre instance du Gestionnaire de connexion hybride sur un autre serveur. Configurer le récepteur locale pour utiliser la même adresse comme le premier récepteur local. Dans ce cas, le trafic est répartis de manière aléatoire (alternées) entre les récepteurs active en local. 


## <a name="ManageHybridConnection"></a>Gérer les connexions hybride
Pour gérer vos connexions hybride, vous pouvez :

- Utiliser le portail Azure et accédez à votre BizTalk Service. 
- Utiliser des [API REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copier/régénérer les chaînes de connexion hybride

1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation gauche, sélectionnez **Services BizTalk** , puis sélectionnez votre BizTalk Service. 
3. Sélectionnez l’onglet **Connexions hybride** :  
![Onglet Connexions hybride][HybridConnectionTab]
4. Sélectionnez la connexion hybride. Dans la barre des tâches, sélectionnez **Gérer la connexion**:  
![Gérer les Options][HCManageConnection]

    **Gérer la connexion** répertorie les chaînes de connexion Application et en local. Vous pouvez copier les chaînes de connexion ou régénérer la clé d’accès utilisée dans la chaîne de connexion. 

    **Si vous sélectionnez régénérer**, la touche d’accès partagés utilisées dans la chaîne de connexion est modifié. Procédez comme suit :
    - Dans le portail classique Azure, sélectionnez **Clés de synchronisation** dans l’application Azure.
    - Exécutez à nouveau **Le programme d’installation locale**. Lorsque vous exécutez de nouveau le programme d’installation locale, la ressource locale est configurée automatiquement pour utiliser la chaîne de connexion primaire mis à jour.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Utiliser Stratégie de groupe pour contrôler les ressources locales utilisées par une connexion hybride

1. Télécharger les [modèles d’administration du Gestionnaire de connexions hybride](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraire les fichiers.
3. Sur l’ordinateur qui modifie la stratégie de groupe, procédez comme suit :  

    - Copier la. Fichiers ADMX dans le dossier *%WINROOT%\PolicyDefinitions* .
    - Copier la. Fichiers ADML vers le dossier *%WINROOT%\PolicyDefinitions\en-us* .

Une fois copiées, vous pouvez utiliser l’éditeur de stratégie de groupe pour modifier la stratégie.




## <a name="next"></a>Suivant

[Connecter des applications Web Azure à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Se connecter à locale SQL Server à partir des applications Web Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Vue d’ensemble des connexions hybride](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Voir aussi

[API REST de gestion des Services BizTalk sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services : Graphique des éditions](biztalk-editions-feature-chart.md)  
[Créer un BizTalk Service à l’aide du portail classique Azure](biztalk-provision-services.md)  
[BizTalk Services : Onglets de tableau de bord, surveiller et échelle](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 

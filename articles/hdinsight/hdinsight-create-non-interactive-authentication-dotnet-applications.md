<properties
    pageTitle="Créer l’authentification non interactive HDInsight .NET applications | Microsoft Azure"
    description="Apprenez à créer des applications .NET HDInsight l’authentification non interactive."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Créer des applications .NET HDInsight authentification non interactif

Vous pouvez exécuter votre application .NET Azure HDInsight sous identité de l’application (non interactif) ou sous l’identité de l’utilisateur connecté de l’application (interactive). Pour obtenir un exemple de l’application interactif, voir [Soumettre Hive/cochon/Sqoop des travaux à l’aide du Kit de développement .NET HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Cet article vous explique comment créer l’authentification non interactive .NET application pour vous connecter à Azure HDInsight et soumettre une tâche Hive.

À partir de votre application .NET, vous devez :

- votre ID de client l’abonnement Azure
- l’ID de client Azure Directory application
- le répertoire Azure application code secret.  

Le processus principal inclut les étapes suivantes :

2. Créer une application Azure Directory.
2. Attribuer des rôles à l’application AD.
3. Développer votre application cliente.


##<a name="prerequisites"></a>Conditions préalables

- Cluster HDInsight. Vous pouvez créer un en suivant les instructions figurant dans le [didacticiel de mise en route](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Créer l’application du répertoire Azure 
Lorsque vous créez une application Active Directory, il crée réellement l’application et un service principal. Vous pouvez exécuter l’application sous l’identité de l’application.

Pour l’instant, vous devez utiliser le portail classique Azure pour créer une nouvelle application Active Directory. Cette fonctionnalité sera ajoutée au portail Azure dans une version ultérieure. Vous pouvez également effectuer ces étapes via PowerShell Azure ou Azure infrastructure du langage commun. Pour plus d’informations sur l’utilisation de PowerShell ou infrastructure du langage commun avec le service principal, voir [service d’authentification principal avec le Gestionnaire de ressources Azure](../resource-group-authenticate-service-principal.md).

**Pour créer une application Azure Directory**

1.  Connectez-vous au [portail classique Azure]( https://manage.windowsazure.com/).
2.  Sélectionnez **Active Directory** dans le volet gauche.

    ![Azure AD portail classique](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Sélectionnez le répertoire que vous souhaitez utiliser pour la création de la nouvelle application. Il doit être celui qui existe déjà.
4.  Cliquez sur **Applications** à partir du haut pour répertorier les applications existantes.
5.  Cliquez sur **Ajouter** à partir du bas pour ajouter une nouvelle application.
6.  Entrez le **nom**et sélectionnez **une application Web et/ou API Web**puis cliquez sur **suivant**.

    ![nouvelle application azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Entrez **authentification URL** et **Application ID URI**. Pour l' **URL de session**, fournissent URI à un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour l’application ID URI, fournissent l’URI qui identifie votre application. Puis sur **Terminer**.
Il faut quelques instants pour créer l’application.  Une fois que l’application est créée, le portail vous montre la page d’activité rapide de la nouvelle application. Ne fermez pas le portail. 

    ![nouvelles propriétés d’application azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Pour obtenir l’application cliente ID et le code secret**

1.  Dans la page d’application AD nouvellement créée, cliquez sur **configurer** dans le menu supérieur.
2.  Créez une copie de **Réf Client**. Vous en aurez besoin dans votre application .NET.
3.  Sous **touches**, cliquez sur la liste déroulante **Sélectionner durée** , puis sélectionnez **1 an** ou **2 ans**. La valeur de clé ne s’affichera pas jusqu'à ce que vous enregistrez la configuration.
4.  Cliquez sur **Enregistrer** dans la partie inférieure de la page. Lorsque le code secret s’affiche, effectuez une copie de la clé. Vous en aurez besoin dans votre application .NET.

##<a name="assign-ad-application-to-role"></a>Attribuer application AD au rôle

Vous devez affecter l’application à un [rôle](../active-directory/role-based-access-built-in-roles.md) à lui accorder des autorisations permettant d’effectuer des actions. Vous pouvez définir l’étendue au niveau de l’abonnement, le groupe de ressources ou la ressource. Les autorisations sont héritées à des niveaux inférieurs d’étendue (par exemple, ajouter qu'une application au rôle de lecteur pour un groupe de ressources signifie qu’il peut lire le groupe de ressources et les ressources qu’il contient). Dans ce didacticiel, vous allez définir l’étendue au niveau du groupe de ressources.  Le portail classique Azure ne prenant pas en charge les groupes de ressources, ce composant doit être effectuée à partir du portail Azure. 

**Pour ajouter le rôle de propriétaire de l’application AD**

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le volet gauche, cliquez sur **Groupe de ressources** .
3.  Cliquez sur le groupe de ressources qui contient le cluster HDInsight où vous exécuterez votre requête Hive plus loin dans ce didacticiel. S’il y a trop de groupes de ressources, vous pouvez utiliser le filtre.
4.  Cliquez sur **l’accès** à partir de la carte cluster.

    ![icône de cloud et thunderbolt = démarrage rapide](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  Cliquez sur **Ajouter** à partir de la carte **d’utilisateurs** .
6.  Suivez les instructions pour ajouter le rôle de **propriétaire** de l’application AD que vous avez créé dans la dernière procédure. Lorsque vous réalisez avec succès, vous doit voir l’application répertoriée dans la carte les utilisateurs dotés du rôle propriétaire.


##<a name="develop-hdinsight-client-application"></a>Développer l’application cliente HDInsight

Créer une application de console c# .net suivant les instructions figurant dans les [tâches Hadoop soumettre dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Puis remplacez la méthode GetTokenCloudCredentials avec les éléments suivants :

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Pour récupérer l’ID de client par le biais PowerShell :

    Get-AzureRmSubscription

Ou, Azure infrastructure du langage commun :

    azure account show --json

      
## <a name="see-also"></a>Voir aussi

- [Soumettre des travaux Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Créer une application Active Directory et principal du service à l’aide du portail](../resource-group-create-service-principal-portal.md)
- [Authentifier principal du service avec le Gestionnaire de ressources Azure](../resource-group-authenticate-service-principal.md)
- [Contrôle d’accès basé sur un rôle Azure](../active-directory/role-based-access-control-configure.md)

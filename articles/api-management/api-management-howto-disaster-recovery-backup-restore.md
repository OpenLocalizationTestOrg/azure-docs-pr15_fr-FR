<properties 
    pageTitle="Comment mettre en œuvre sinistre à l’aide de sauvegarde de service et restaurer gestion des API Azure | Microsoft Azure" 
    description="Découvrez comment utiliser la sauvegarde et de restauration pour effectuer sinistre Gestion des API Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Comment mettre en œuvre sinistre à l’aide de sauvegarde de service et restaurer gestion des API Azure

En choisissant de publier et de gérer votre API via la gestion de l’API Azure vous tirez parti de nombreux tolérance de panne et les capacités de l’infrastructure que vous devriez dans le cas contraire à concevoir, implémenter et gérer. La plateforme Azure réduit une grande partie des défaillances potentielles en une fraction du coût.

Pour restaurer à partir de la disponibilité des problèmes affectant la région dans lequel la gestion de l’API service est hébergé vous doivent être prêts à reconstituer votre service dans une zone différente à tout moment. En fonction de vos objectifs de disponibilité et les objectifs de temps, vous souhaiterez peut-être réserver un service de sauvegarde dans une ou plusieurs régions et essayez de mettre à jour leur configuration et leur contenu synchronisé avec le service actif. La sauvegarde de service et fonctionnalité restaurer fournit le bloc de construction nécessaire pour implémenter votre stratégie de récupération d’urgence.

Ce guide montre comment authentifier les requêtes Azure le Gestionnaire de ressources et comment faire pour sauvegarder et restaurer vos instances de service de gestion de l’API.

>[AZURE.NOTE] Le processus de sauvegarde et restauration d’une instance de service de gestion de l’API de reprise permet également de réplication des instances de service de gestion de l’API pour les scénarios de mise en attente.
>
>Notez que chaque sauvegarde expire après 7 jours. Si vous essayez de restaurer une sauvegarde après l’expiration de la période d’expiration de 7 jours, la restauration échoue avec un `Cannot restore: backup expired` message.

## <a name="authenticating-azure-resource-manager-requests"></a>Les demandes d’authentification Azure le Gestionnaire de ressources

>[AZURE.IMPORTANT] L’API REST de sauvegarde et de restauration utilise le Gestionnaire de ressources Azure et contient un mécanisme d’authentification différent les API REST permettant de gérer vos entités de gestion de l’API. Les étapes décrites dans cette section décrivent comment authentifier les requêtes Azure le Gestionnaire de ressources. Pour plus d’informations, voir [demandes d’authentification Azure directeur des ressources](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Toutes les tâches que vous effectuez sur les ressources à l’aide du Gestionnaire de ressources Azure doivent être authentifié avec Azure Active Directory à l’aide de la procédure suivante.

-   Ajouter une application pour le client Azure Active Directory.
-   Définir des autorisations pour l’application que vous avez ajouté.
-   Obtenir le jeton pour l’authentification des demandes au Gestionnaire de ressources Azure.

La première étape consiste à créer une application Azure Active Directory. Connectez-vous au [Portail classique Azure](http://manage.windowsazure.com/) à l’aide de l’abonnement qui contient votre instance de service de gestion de l’API et accédez à l’onglet **Applications** pour votre Azure Active Directory par défaut.

>[AZURE.NOTE] Si le répertoire par défaut Azure Active Directory n’est pas visible à votre compte, contactez l’administrateur de l’abonnement Azure pour octroyer les autorisations requises à votre compte. Pour plus d’informations sur l’emplacement de votre répertoire par défaut, voir [localiser votre répertoire par défaut](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Créer l’application Azure Active Directory][api-management-add-aad-application]

Cliquez sur **Ajouter**, **Ajouter une application de développe de mon organisation**, puis sélectionnez **application cliente Native**. Entrez un nom descriptif, puis cliquez sur la flèche suivant. Entrez une URL de l’espace réservé tel que `http://resources` pour **Rediriger URI**, telle qu’elle est un champ obligatoire, mais la valeur n’est pas utilisée ultérieurement. Cliquez sur la case à cocher pour enregistrer l’application.

Une fois que l’application est enregistrée, cliquez sur **configurer**, faites défiler jusqu'à la section **autorisations à d’autres applications** , puis cliquez sur **Ajouter une application**.

![Ajouter des autorisations][api-management-aad-permissions-add]

Sélectionnez **Windows** **Azure API de gestion des services** , puis cliquez sur la case à cocher pour ajouter l’application.

![Ajouter des autorisations][api-management-aad-permissions]

Cliquez sur **Autorisations déléguée** à côté de l’application **Windows** **Azure Service Gestion des API** nouvellement ajoutée, activez la case à cocher pour la **Gestion des services Azure Access (preview)**, puis cliquez sur **Enregistrer**.

![Ajouter des autorisations][api-management-aad-delegated-permissions]

Avant d’appeler l’API qui génèrent la sauvegarde et de rétablir, il est nécessaire d’obtenir un jeton. L’exemple suivant utilise le package nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pour récupérer le jeton.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Remplacer `{tentand id}`, `{application id}`, et `{redirect uri}` en suivant les instructions suivantes.

Remplacer `{tenant id}` de l’id de client de l’application Azure Active Directory que vous venez de créer. Vous pouvez accéder à l’id en cliquant sur les **points de terminaison de vue**.

![Points de terminaison][api-management-aad-default-directory]

![Points de terminaison][api-management-endpoint]

Remplacer `{application id}` et `{redirect uri}` à l’aide de l' **Id de Client** et l’URL de la section **Rediriger URI** à partir de l’onglet **configuration** de votre application Azure Active Directory. 

![Ressources][api-management-aad-resources]

Une fois que les valeurs sont spécifiés, l’exemple de code doit renvoyer un jeton similaire à l’exemple suivant.

![Jetons][api-management-arm-token]

Avant d’appeler les opérations de sauvegarde et de restauration décrites dans les sections suivantes, définissez l’en-tête de demande d’autorisation de votre appel reste.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Un service de gestion de l’API de sauvegarde
Pour sauvegarder une gestion de l’API de service problème la requête HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Dans cet exemple :

* `subscriptionId`-id de l’abonnement contenant le service de gestion des API vous essayez de sauvegarde
* `resourceGroupName`-une chaîne sous forme de « Api - Default-{service région} » où `service-region` identifie la région Azure où la gestion de l’API de service que vous tentez de sauvegarde est hébergé, par exemple :`North-Central-US`
* `serviceName`-le nom du service de gestion de l’API vous effectuez une sauvegarde de spécifiée au moment de sa création
* `api-version`-remplacer par`2014-02-14`

Dans le corps de la demande, spécifiez le nom du compte cible stockage Azure touche d’accès rapide, nom du conteneur blob et nom de la sauvegarde :

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Définir la valeur de la `Content-Type` en-tête de demande pour `application/json`.

La sauvegarde est une opération longue qui peut prendre plusieurs minutes.  Si la demande a réussi et le processus de sauvegarde a été démarrée vous recevrez un `202 Accepted` code d’état de réponse avec un `Location` en-tête.  Vérifiez 'GET' demandes à l’URL dans la `Location` en-tête pour déterminer l’état de l’opération. Alors que la sauvegarde est en cours vous continuent à recevoir un code d’état « 202 accepté ». Code de réponse `200 OK` indiquera réussite de l’opération de sauvegarde.

**Remarque**:

- **Conteneur** spécifié dans le corps de la demande **doivent exister**.
* Alors que la sauvegarde est en cours **ne doivent pas essayer les opérations de gestion des services** tels que de référence (SKU) de mise à niveau ou mettre à niveau, modification du nom de domaine, etc.. 
* Restauration d’une **sauvegarde est garantie uniquement pendant 7 jours** depuis le moment de sa création. 
* **Données d’utilisation** utilisées pour la création d’analytique rapports **n’est pas inclus** dans la sauvegarde. Utilisez [L’API REST de gestion de l’API d’Azure][] pour extraire périodiquement des rapports analytique de conservation.
* La fréquence à laquelle vous effectuez des sauvegardes service affectent votre objectif de point de récupération. Pour le réduire nous vous conseillons de mise en œuvre des sauvegardes régulières, ainsi que des sauvegardes à la demande après avoir apporté des modifications importantes apportées à votre service de gestion de l’API.
* **Modifications** apportées à la configuration du service (par exemple, API, stratégies, apparence portail développeur) lors de la sauvegarde est en cours **ne sont pas inclus dans la sauvegarde et par conséquent seront perdues**.

## <a name="step2"> </a>Restaurer un service de gestion de l’API
Pour restaurer une gestion de l’API service à partir d’une sauvegarde créée précédemment Vérifiez la requête HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Dans cet exemple :

* `subscriptionId`-id de l’abonnement contenant le service de gestion des API vous restaurez une sauvegarde dans
* `resourceGroupName`-une chaîne sous forme de « Api - Default-{service région} » où `service-region` identifie la région Azure où le service de gestion de l’API vous restaurez une sauvegarde dans est hébergé, par exemple,`North-Central-US`
* `serviceName`-le nom de la gestion de l’API service en cours de restauration dans spécifié au moment de sa création
* `api-version`-remplacer par`2014-02-14`

Dans le corps de la demande, spécifiez l’emplacement du fichier de sauvegarde, par exemple, nom de compte de stockage Azure touche d’accès rapide, nom du conteneur blob et nom de la sauvegarde :

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Définir la valeur de la `Content-Type` en-tête de demande pour `application/json`.

La restauration est une opération longue peut prendre jusqu'à 30 ou plusieurs minutes pour terminer.  Si la demande a réussi et le processus de restauration a été démarrée vous recevrez un `202 Accepted` code d’état de réponse avec un `Location` en-tête.  Vérifiez 'GET' demandes à l’URL dans la `Location` en-tête pour déterminer l’état de l’opération. Lorsque la restauration est en cours vous continuent à recevoir 'accepté 202' code d’état. Code de réponse `200 OK` indiquera réussite de l’opération de restauration.

>[AZURE.IMPORTANT] **La référence SKU** du service en cours restauré dans **doit correspondre à** la référence SKU du service sauvegardé en cours de restauration.
>
>**Modifications** apportées à la configuration du service (par exemple, API, stratégies, apparence portail développeur) lors de la restauration est en cours **peuvent être remplacés**.

## <a name="next-steps"></a>Étapes suivantes
Consultez les blogs Microsoft suivants pour les deux premières différents du processus de sauvegarde et de restauration.

-   [Réplication API Azure Gestion des comptes](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Nous vous remercions à Gisela pour sa contribution à cet article.
-   [Gestion des API Azure : Sauvegarde et restauration de la Configuration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   L’approche détaillée en Stuart ne correspond pas à l’aide officiel mais il est très intéressante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Gestion des API Azure API REST]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 

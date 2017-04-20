<properties 
    pageTitle="Mettre à jour Media Services après touches d’accès de stockage des yeux | Microsoft Azure" 
    description="Cet article vous donner des conseils pour mettre à jour Media Services après yeux touches d’accès de stockage." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Mise à jour Media Services après touches d’accès de stockage des yeux

Lorsque vous créez un nouveau compte Azure Media Services, vous êtes également invité à sélectionner un compte de stockage Azure qui permet de stocker votre contenu multimédia. Notez que vous pouvez [Ajouter plusieurs comptes de stockage](meda-services-managing-multiple-storage-accounts.md) à votre compte Media Services.

Création d’un nouveau compte de stockage, Azure génère deux 512 bits stockage touches d’accès rapide, qui sont utilisés pour authentifier l’accès à votre compte de stockage. Pour renforcer la sécurité vos connexions de stockage, il est recommandé de régulièrement régénérer et faire pivoter votre clé d’accès de stockage. Deux touches d’accès (principales et secondaires) sont fournies afin de vous permettent de conserver les connexions au compte de stockage à l’aide d’une touche d’accès rapide alors que vous régénérez autres touche d’accès rapide. Cette procédure est également appelée « touches d’accès rapide propagée ».

Media Services dépend une clé de stockage qui lui sont fournie. Plus précisément, les indicateurs qui sont utilisés pour diffuser en continu ou télécharger vos ressources dépendent de la touche d’accès de stockage spécifié. Lors de la création d’un compte AMS prend une dépendance sur la touche d’accès de stockage principal par défaut, mais en tant qu’utilisateur vous pouvez mettre à jour la clé de stockage AMS a. Vous devez vous assurer afin d’indiquer quelle clé utiliser en suivant les étapes décrites dans cette rubrique Media Services. En outre, en déplaçant les touches d’accès de stockage, vous devez veillez à mettre à jour votre Locator donc ne sera pas interrompu dans votre service de diffusion en continu (cette étape est également décrite dans la rubrique).

>[AZURE.NOTE]Si vous avez plusieurs comptes de stockage, vous devez effectuer cette procédure avec chaque compte de stockage.
>
>Avant d’exécuter les étapes décrites dans cette rubrique sur un compte de production, veillez à tester sur un compte de production avant.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Étape 1 : Régénérer touche d’accès rapide stockage secondaire

Précédez régénérer clé de stockage secondaire. Par défaut, la clé secondaire n’est pas utilisée par Media Services.  Pour plus d’informations sur la restauration de clés de stockage, voir [Comment : affichage, copie et le stockage régénérer les touches d’accès](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Étape 2 : Services de support mise à jour pour utiliser la nouvelle clé de stockage secondaire

Mettre à jour Media Services pour utiliser la touche d’accès de stockage secondaire. Vous pouvez utiliser une des deux méthodes suivantes pour synchroniser la clé de stockage régénérée avec Media Services.

- Utiliser le portail Azure : pour rechercher le nom et la clé des valeurs, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres apparaît à droite. Dans la fenêtre Paramètres, sélectionnez clés. En fonction de la touche de stockage souhaité pour les Services multimédia à synchroniser avec, sélectionnez la clé primaire de synchroniser ou synchroniser bouton clé secondaire. Dans ce cas, utilisez la touche secondaire.

- Utiliser la gestion des Services de support API REST.

L’exemple suivant montre comment construire la https://endpoint/***subscriptionId/services/mediaservices/comptes/nom de compte*/StorageAccounts/*storageAccountName*demande/Key afin de synchroniser la clé de stockage spécifié avec les Services de support. Dans ce cas, la valeur de clé de stockage secondaire est utilisée. Pour plus d’informations, voir [Comment : API REST d’utilisation Media Services gestion](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Une fois cette étape, mettre à jour Locator existant (qui ont dépendance de l’ancienne clé de stockage) comme indiqué dans l’étape suivante.

>[AZURE.NOTE]Attendre 30 minutes avant d’effectuer les opérations avec Media Services (par exemple, création de nouveaux Locator) afin d’éviter toute incidence sur les tâches en attente.

##<a name="step-3-update-locators"></a>Étape 3 : Repères de mise à jour

>[AZURE.NOTE]Lors du déploiement touches d’accès de stockage, vous devez veillez à mettre à jour votre Locator existant, il n’est pas interrompu dans votre service de diffusion en continu.

Patientez au moins 30 minutes après avoir synchronisé la nouvelle clé de stockage avec AMS. Ensuite, vous pouvez recréer votre Locator à la demande afin qu’ils prennent dépendance sur la touche de stockage spécifié et mettre à jour l’URL existante.

Notez que lorsque vous mettez à jour (ou recréez) un repère associations de sécurité, l’URL toujours changera.

>[AZURE.NOTE] Pour vous assurer que vous conservez les URL existantes de votre Locator à la demande, vous devez supprimer le repère existant et créer un nouveau avec le même ID.

L’exemple .NET ci-dessous montre comment recréer un repère avec le même ID.

privé ILocator RecreateLocator(CloudMediaContext context, ILocator locator) statique {/ / enregistrer les propriétés de locator existant.
var bien = locator. Actif ; var accessPolicy = locator. AccessPolicy ; var locatorId = locator. ID ; var startDate = locator. Heure de début ; var locatorType = locator. Tapez ; var locatorName = locator. Nom ;

Supprimer l’ancienne locator.
Locator. Delete() ;

Si (locator. ExpirationDateTime < = DateTime.UtcNow) {lever une Exception nouvelle (String.Format ( » ne peut pas recréer locator Id = {0}, car le délai d’expiration locator est passée », locator. ID)) ; }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Étape 5 : Régénérer touche d’accès rapide stockage principal

Régénérer la touche d’accès de stockage principal. Pour plus d’informations sur la restauration de clés de stockage, voir [Comment : affichage, copie et le stockage régénérer les touches d’accès](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Étape 6 : Services de support mise à jour pour utiliser la nouvelle clé de stockage principal
    
Utiliser la même procédure comme décrit à [l’étape 2](media-services-roll-storage-access-keys.md#step2) , mais cette fois-ci synchroniser nouveau stockage principal touche d’accès rapide avec le compte Media Services.

>[AZURE.NOTE]Attendre 30 minutes avant d’effectuer les opérations avec Media Services (par exemple, création de nouveaux Locator) afin d’éviter toute incidence sur les tâches en attente.

##<a name="step-7-update-locators"></a>Étape 7 : Repères de mise à jour  

Après 30 minutes, vous pouvez recréer votre Locator à la demande afin qu’ils prennent dépendance sur la nouvelle clé de stockage principal et mettre à jour l’URL existante.

Utilisez la procédure décrite à [l’étape 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Accusés de réception 

Nous aimerions confirmer les suivi des personnes qui contribué à la création de ce document : Cenk Dingiloglu, Gada Milan, Seva Titov.

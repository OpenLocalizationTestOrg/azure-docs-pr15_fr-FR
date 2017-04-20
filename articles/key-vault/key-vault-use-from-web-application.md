<properties
    pageTitle="Utiliser l’archivage sécurisé clé Azure à partir d’une Application Web | Microsoft Azure"
    description="Utilisez ce didacticiel pour vous aider à apprendre à utiliser l’archivage sécurisé de clé Azure à partir d’une application web."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Utiliser l’archivage sécurisé clé Azure à partir d’une Application Web #

## <a name="introduction"></a>Introduction  
Utilisez ce didacticiel pour vous aider à apprendre à utiliser l’archivage sécurisé de clé Azure à partir d’une application web dans Azure. Il vous guide dans le processus d’accès à un code secret à partir d’un archivage sécurisé de clé Azure afin qu’il puisse être utilisé dans votre application web.

**Durée estimée d’exécution :** 15 minutes


Pour plus d’informations générales sur l’archivage sécurisé de clé Azure, consultez [Quel est l’archivage sécurisé de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez disposer des éléments suivants :

- URI d’un code secret dans un archivage sécurisé de clé Azure
- Un ID de Client et un code Secret Client pour une application web enregistré avec Azure Active Directory qui accèdent à votre l’archivage sécurisé clé
- Une application web. Nous vous exécutez les étapes d’une application ASP.NET MVC déployée dans Azure comme une application Web.

> [AZURE.NOTE]  Il est essentiel que vous avez terminé la procédure décrite dans [Prise en main l’archivage sécurisé de clé Azure](key-vault-get-started.md) pour ce didacticiel afin que vous ayez URI à un code secret et l’ID de Client et le Client Secret pour une application web.

L’application web accédant à l’archivage sécurisé clé est celui qui est enregistré dans Azure Active Directory et a autorisé à accéder à votre l’archivage sécurisé clé. Si ce n’est pas le cas, revenez à Registre une Application dans le didacticiel mise en route et répétez les étapes répertoriées.

Ce didacticiel est conçu pour les développeurs web que comprendre les bases de la création d’applications web sur Azure. Pour plus d’informations sur Azure Web Apps, voir [vue d’ensemble des applications Web](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Ajouter des Packages Nuget ##
Il existe deux packages que votre application web doit avoir installé.

- Bibliothèque de l’authentification Active Directory - contient des méthodes pour interagir avec Azure Active Directory et la gestion d’identité de l’utilisateur
- Bibliothèque de l’archivage sécurisé clé Azure - contient des méthodes pour interagir avec l’archivage sécurisé de clé Azure


Les deux de ces packages peuvent être installés à l’aide de la Console du Gestionnaire de Package à l’aide de la commande de Package d’installation.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Modifiez le fichier Web.Config ##
Il existe trois paramètres de l’application qui doivent être ajoutés au fichier web.config comme suit.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Si vous allez pas héberger votre application comme une application Web Azure, vous devez ajouter les valeurs réelles identifiant du client, Secret Client et URI Secret à web.config. Dans le cas contraire laisser ces valeurs factices, car nous ajouterons les valeurs réelles dans le portail Azure pour un niveau de sécurité supplémentaire.


## <a id="gettoken"></a>Ajouter une méthode pour obtenir un accès à un jeton ##
Pour pouvoir utiliser l’API de l’archivage sécurisé clé vous avez besoin d’un jeton d’accès. Le Client de l’archivage sécurisé clé gère les appels à l’API de l’archivage sécurisé clé, mais vous devez lui fournir une fonction qui obtient le jeton d’accès.  

Voici le code pour obtenir un accès à un jeton d’Azure Active Directory. Ce code peut trouver n’importe où dans votre application. J’aime ajouter une classe utilitaires ou EncryptionHelper.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> À l’aide d’un ID de Client et un Secret Client est le meilleur moyen d’authentifier une application Azure AD. Et l’utiliser dans votre application web permet une séparation des tâches et davantage de contrôle sur la gestion de clés. Mais il s’appuie sur plaçant le code Secret Client dans vos paramètres de configuration qui peuvent être comme risquées en plaçant le code secret que vous voulez protéger dans vos paramètres de configuration pour certains. Pour plus d’informations sur l’utilisation d’un ID de Client et un certificat au lieu d’ID de Client et le code Secret Client authentifier l’application Azure AD voir ci-dessous.



## <a id="appstart"></a>Récupérez le secret sur Démarrer l’Application ##
Nous devons maintenant code à l’API de l’archivage sécurisé clé d’appel et de récupérer le code secret. Le code suivant peut être placé n’importe où dans la mesure où elle est appelée avant que vous avez besoin d’utiliser. J’ai placer ce code dans l’événement de démarrer l’Application dans Global.asax afin qu’elle s’exécute une fois sur Démarrer et rend le code secret disponibles pour l’application.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Ajouter des paramètres de l’application dans le portail Azure (facultatif) ##
Si vous avez une application Web Azure vous pouvez désormais ajouter les valeurs réelles pour AppSettings dans le portail Azure. En procédant ainsi, les valeurs réelles ne sera pas dans le fichier web.config mais protégées à l’aide du portail où vous disposez des fonctionnalités de contrôle d’accès séparé. Les valeurs suivantes seront remplacés par les valeurs que vous avez entrées dans votre fichier web.config. Assurez-vous que les noms sont identiques.

![Paramètres de l’application affichées dans le portail d’Azure][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Vous authentifier avec un certificat au lieu d’un code Secret Client
Un autre moyen d’authentifier une application Azure AD est à l’aide d’un ID de Client et un certificat au lieu d’un ID de Client et le code Secret de Client. Voici les étapes pour utiliser un certificat dans une application Web Azure :

1. Obtenir ou créer un certificat
2. Associer le certificat à une application Azure AD
3. Ajouter du code à votre application Web pour utiliser le certificat
4. Ajouter un certificat à votre application Web


**Obtenir ou créer un certificat** Dans notre cas, nous fera un certificat de test. Voici quelques des commandes que vous pouvez utiliser dans une invite de commandes pour les développeurs de créer un certificat. Modifier le répertoire vers l’emplacement souhaité pour les fichiers de certificat à créer.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Prenez note de la date de fin et le mot de passe pour la .pfx (dans cet exemple : 31/07/2016 et test123). Vous aurez besoin ci-dessous.

Pour plus d’informations sur la création d’un certificat de test, voir [Comment : créer votre propre tester certificat](https://msdn.microsoft.com/library/ff699202.aspx)


**Associer le certificat avec une application Azure AD** Maintenant que vous avez un certificat, vous devez associer avec une application Azure AD. Le portail de gestion Azure charge mais pas cela immédiatement. Au lieu de cela, vous devez utiliser Powershell. Voici les commandes dont vous avez besoin pour exécuter :

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Après avoir exécuté ces commandes, vous pouvez voir l’application dans Azure Active Directory. Si vous ne voyez pas l’application à tout d’abord, recherchez « Applications propriétaire de mon entreprise » au lieu de « Applications ma société utilise ».

Pour en savoir plus sur les objets d’Application Azure AD et ServicePrincipal objets, voir [objets Application et Principal du Service](../active-directory/active-directory-application-objects.md)



**Ajout de code pour votre application Web pour utiliser le certificat** Nous allons maintenant ajouter le code dans votre application Web pour accéder au certificat et utilisez-le pour l’authentification.

Tout d’abord présente le code d’accès le certificat.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Notez que l’emplacement StoreLocation est CurrentUser au lieu de LocalMachine. Et que nous sommes en fournissant « false » à la méthode Find, car nous utilisons un certificat de test.


L’option suivant est code qui utilise le CertificateHelper et crée un ClientAssertionCertificate qui est nécessaire pour l’authentification.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Voici le nouveau code pour obtenir le jeton d’accès. Cette option remplace la méthode GetToken ci-dessus. Je lui ai donné un nom différent pour faciliter la tâche.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

J’ai tout ce code mis en cours d’utilitaires de mon projet Web App pour faciliter leur utilisation.

La dernière modification du code se trouve dans la méthode Application_Start. Tout d’abord, nous avons besoin appeler la méthode GetCert() pour charger le ClientAssertionCertificate. Et nous modifiez la méthode de rappel que nous fournissez lorsque vous créez un nouveau KeyVaultClient. Notez que ce remplace le code que nous avions ci-dessus.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Ajouter un certificat à votre application Web via le portail d’Azure** Ajout d’un certificat dans votre application Web est un processus simple en deux étapes. Tout d’abord, accédez au portail Azure et accédez à votre application Web. Dans la carte de paramètres pour votre application Web, cliquez sur l’entrée pour « Custom domains et SSL ». Sur la carte qui s’ouvre, vous pourrez télécharger le certificat que vous avez créé précédemment, KVWebApp.pfx, vérifiez que vous n’oubliez pas le mot de passe pour la pfx.

![Ajout d’un certificat pour une application Web dans le portail Azure][2]


La dernière chose que vous devez effectuer consiste à ajouter un paramètre d’Application à votre application Web qui contient le nom du site Web\_charge\_certificats et une valeur de *. Cela garantit que tous les certificats sont chargés. Si vous souhaitez charger uniquement les certificats que vous avez téléchargées, vous pouvez entrer une liste séparée par des virgules leurs empreintes.

Pour plus d’informations sur l’ajout d’un certificat pour une application Web, voir [Utilisation des certificats dans les Applications de sites Web Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Ajouter un certificat pour l’archivage sécurisé clé comme un secret** Au lieu de télécharger votre certificat au service Web App directement, vous pouvez stocker dans l’archivage sécurisé clé comme un code secret et déployer à partir de là. Il s’agit d’un processus en deux étapes qui est décrit dans le billet de blog suivant, [Déploiement d’Azure Web application certificat via l’archivage sécurisé clé](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Étapes suivantes ##


Pour les références de programmation, voir [Azure clé l’archivage sécurisé c# API référence du Client](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png

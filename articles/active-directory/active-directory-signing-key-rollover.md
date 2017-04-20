<properties
    pageTitle="Connecter substitution de la clé Azure AD | Microsoft Azure"
    description="Cet article décrit les pratiques recommandées substitution de la clé signature pour Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Signature de substitution de la clé dans Azure Active Directory

Cette rubrique décrit ce que vous devez savoir sur les clés publiques sont utilisées dans Azure Active Directory (AD Azure) pour signer des jetons de sécurité. Il est important de noter que ces survol clés périodiquement et, en cas d’urgence, peut être déployée immédiatement. Toutes les applications qui utilisent Azure AD doivent être en mesure de gérer le processus de substitution de la clé par programme ou établir un processus de survol manuelle périodiquement. Poursuivre la lecture pour comprendre le fonctionnement de l’aide des touches, comment évaluer l’impact de la zone réactive à votre application et mettre à jour votre application ou établir un processus périodique survol manuel pour gérer les clés survol si nécessaire.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Vue d’ensemble des clés de signature dans Azure Active Directory

Azure AD utilise le chiffrement de clé publique normes industrielles pour établir l’approbation entre lui-même et les applications qui l’utilisent. Dans la pratique, cela fonctionne de la manière suivante : Azure AD utilise une clé de signature est constitué par une paire de clés publique et privée. Lorsqu’un utilisateur se connecte à une application qui utilise Azure AD pour l’authentification, Azure AD crée un jeton de sécurité qui contient des informations sur l’utilisateur. Ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être envoyé à l’application. Pour vérifier que le jeton est valide et réellement origines à partir d’Azure AD, l’application doit valider signature du jeton à l’aide de la clé publique exposée par Azure AD se trouvant dans le client [OpenID connecter un document de découverte](http://openid.net/specs/openid-connect-discovery-1_0.html) ou SAML/Web-Fed [document de métadonnées de fédération](active-directory-federation-metadata.md).

Pour des raisons de sécurité, Azure AD signature pellicule clés périodiquement et, dans le cas d’urgence, peuvent être annulés immédiatement. N’importe quelle application qui s’intègre à Azure AD doit être préparée à gérer un événement de substitution de la clé quel que soit la fréquence à laquelle il peut se produire. Si elle n’et que votre application tente d’utiliser une clé qui a expiré pour vérifier la signature sur un jeton, la demande de connexion échouera.

Plus d’une clé valide est toujours disponible dans le document de découverte OpenID se connecter et le document de métadonnées de fédération. Votre application Préparez-vous à utiliser chacune des touches spécifiées dans le document, dans la mesure où une clé peut-être être appliquée plus tôt, une autre peut être son remplacement et ainsi de suite.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Comment déterminer si votre application est affectée et qu’il faut faire

Comment votre application gère substitution de la clé dépend de variables telles que le type d’application ou le protocole d’identité et de bibliothèque a été utilisé. Les sections ci-dessous évaluer si les applications les plus courants sont affectés par la substitution de la clé et fournissent des instructions sur la mise à jour l’application à prendre en charge la substitution automatique ou mettre à jour manuellement la clé.

* [Applications clientes native l’accès aux ressources](#nativeclient)
* [Applications Web / API l’accès aux ressources](#webclient)
* [Applications Web / API de protection des ressources et créés à l’aide de Services d’application Azure](#appservices)
* [Applications Web / API de protection des ressources à l’aide de .NET OWIN OpenID Connect, ou pages Web ou WindowsAzureActiveDirectoryBearerAuthentication logiciels intermédiaires](#owin)
* [Applications Web / API de protection des ressources à l’aide de logiciels intermédiaires .NET Core OpenID connecter ou JwtBearerAuthentication](#owincore)
* [Applications Web / API de protection des ressources à l’aide du module de passport azure ad Node.js](#passport)
* [Applications Web / API de protection des ressources et créé avec Visual Studio 2015](#vs2015)
* [Applications Web protection des ressources et créé avec Visual Studio 2013](#vs2013)
* [API Web de protection des ressources et créé avec Visual Studio 2013](#vs2013_webapi)
* [Applications Web protection des ressources et créé avec Visual Studio 2012](#vs2012)
* [Protection des ressources et créé avec Visual Studio 2010, o 2008 à l’aide de Windows Identity Foundation des applications Web](#vs2010)
* [Applications Web / API de protection des ressources à l’aide de toutes les bibliothèques ou implémenter manuellement les protocoles pris en charge](#other)

Ce guide est **non** applicable pour :

* Applications ajoutées à partir de la galerie des applications Azure AD (y compris personnalisées) ont des directives spécifiques en ce qui concerne les clés de signature. [Plus d’informations.](active-directory-sso-certs.md)
* Locale applications publiées via un proxy de l’application n’ont pas à vous soucier de clés de signature.

### <a name="nativeclient"></a>Applications clientes native l’accès aux ressources

Applications qui accèdent uniquement à des ressources (ex : Microsoft Graph, KeyVault, API Outlook et autres APIs Microsoft) généralement uniquement obtenir un jeton et passent jusqu’au propriétaire de la ressource. Étant donné qu’ils sont protège pas toutes les ressources, ils inspecter pas le jeton et n’avez donc pas besoin pour vous assurer qu’il est correctement connecté.

Les applications clientes native, ou non bureau ou portable, entrent dans cette catégorie et par conséquent ne sont pas affectées par le survol.

### <a name="webclient"></a>Applications Web / API l’accès aux ressources

Applications qui accèdent uniquement à des ressources (ex : Microsoft Graph, KeyVault, API Outlook et autres APIs Microsoft) généralement uniquement obtenir un jeton et passent jusqu’au propriétaire de la ressource. Étant donné qu’ils sont protège pas toutes les ressources, ils inspecter pas le jeton et par conséquent n’avez pas besoin pour vous assurer qu’il est correctement connecté.

Applications et web API qui utilisent le flux application seule (informations d’identification client / certificat client), entrent dans cette catégorie et par conséquent ne sont pas affectés par le survol.

### <a name="appservices"></a>Applications Web / API de protection des ressources et créés à l’aide de Services d’application Azure

Authentification Azure application Services / fonctionnalité d’autorisation (EasyAuth) a déjà la logique nécessaire pour gérer automatiquement substitution de la clé.

### <a name="owin"></a>Applications Web / API de protection des ressources à l’aide de .NET OWIN OpenID Connect, ou pages Web ou WindowsAzureActiveDirectoryBearerAuthentication logiciels intermédiaires

Si votre application utilise le .NET OWIN OpenID Connect, ou pages Web ou logiciels intermédiaires WindowsAzureActiveDirectoryBearerAuthentication, qu’il possède déjà la logique nécessaire pour gérer automatiquement substitution de la clé.

Vous pouvez vérifier que votre application utilise un de ces en recherchant un des extraits de code suivants dans votre application Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Applications Web / API de protection des ressources à l’aide de logiciels intermédiaires .NET Core OpenID connecter ou JwtBearerAuthentication

Si votre application utilise logiciels intermédiaires .NET Core OWIN OpenID connecter ou JwtBearerAuthentication, qu’il possède déjà la logique nécessaire pour gérer automatiquement substitution de la clé.

Vous pouvez vérifier que votre application utilise un de ces en recherchant un des extraits de code suivants dans votre application Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Applications Web / API de protection des ressources à l’aide du module de passport azure ad Node.js

Si votre application utilise le module ad passport Node.js, qu’il possède déjà la logique nécessaire pour gérer automatiquement substitution de la clé.

Vous pouvez vous assurer que votre application passport-annonce en recherchant l’extrait suivant dans votre application app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Applications Web / API de protection des ressources et créé avec Visual Studio 2015

Si votre application a été créée à l’aide d’un modèle d’application web dans Visual Studio 2015 et que vous avez sélectionné des **Comptes de l’école et de travail** dans le menu **Modifier l’authentification** , qu’il possède déjà la logique nécessaire pour gérer automatiquement substitution de la clé. Cette logique, incorporée dans logiciels intermédiaires OWIN OpenID connecter récupère et met en cache les touches du document de découverte OpenID se connecter et régulièrement actualise les.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application peut-être pas la logique de substitution de la clé nécessaire. Vous devrez écrire vous-même, ou suivez les étapes décrites dans [applications Web / API à l’aide de toutes les bibliothèques ou implémenter manuellement les protocoles pris en charge.](#other).

### <a name="vs2013"></a>Applications Web protection des ressources et créé avec Visual Studio 2013

Si votre application a été créée à l’aide d’un modèle d’application web dans Visual Studio 2013 et que vous avez sélectionné **Comptes professionnels** dans le menu **Modifier l’authentification** , qu’il possède déjà la logique nécessaire pour gérer automatiquement substitution de la clé. Cette logique stocke identificateur unique de votre organisation et les informations de clé signature dans deux tables de base de données associés au projet. Vous pouvez trouver la chaîne de connexion pour la base de données dans le fichier Web.config du projet.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application peut-être pas la logique de substitution de la clé nécessaire. Vous devrez écrire vous-même, ou suivez les étapes décrites dans [applications Web / API à l’aide de toutes les bibliothèques ou implémenter manuellement les protocoles pris en charge.](#other).

Les étapes suivantes vous aideront à vérifier que la logique fonctionne correctement dans votre application.

1. Dans Visual Studio 2013, ouvrez la solution et puis cliquez sur l’onglet **Explorateur de serveurs** dans la fenêtre de droite.
2. Développez les **connexions de données**, **DefaultConnection**et **Tables**. Identifier le tableau **IssuingAuthorityKeys** , avec le bouton droit dessus, puis cliquez sur **Afficher les données de tableau**.
3. Dans la table **IssuingAuthorityKeys** , il sera au moins une ligne, ce qui correspond à la valeur de l’empreinte numérique pour la clé. Supprimez toutes les lignes du tableau.
4. Avec le bouton droit de la table **clients** , puis cliquez sur **Afficher les données de tableau**.
5. Dans la table **clients** , il sera au moins une ligne, ce qui correspond à un identificateur de client répertoire unique. Supprimez toutes les lignes du tableau. Si vous ne pas supprimer les lignes dans la table **clients** et la table **IssuingAuthorityKeys** , vous obtenez une erreur lors de l’exécution.
6. Créer et exécuter l’application. Une fois que vous êtes connecté à votre compte, vous pouvez arrêter l’application.
7. Revenir à l' **Explorateur de serveurs** et observez les valeurs du tableau **IssuingAuthorityKeys** et **clients** . Vous pouvez constater qu’ils ont été remplis automatiquement avec les informations appropriées dans le document de métadonnées de fédération.

### <a name="vs2013"></a>API Web de protection des ressources et créé avec Visual Studio 2013

Si vous avez créé un site web application API dans Visual Studio 2013 à l’aide du modèle API Web et puis sélectionné **Comptes professionnels** dans le menu **Modifier l’authentification** , vous possédez déjà la logique nécessaire dans votre application.

Si vous avez configuré manuellement l’authentification, suivez les instructions ci-dessous pour savoir comment configurer votre API Web pour mettre automatiquement à jour ses informations clées.

L’extrait de code suivant montre comment obtenir les dernières clés à partir du document de métadonnées de fédération, puis utiliser le [Gestionnaire de jetons JWT](https://msdn.microsoft.com/library/dn205065.aspx) pour valider le jeton. L’extrait de code suppose que vous utiliserez votre propre mécanisme de mise en cache pour conserver la clé pour valider les futurs jetons à partir d’Azure AD, qu’il s’agisse dans une base de données, le fichier de configuration ou un autre emplacement.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Applications Web protection des ressources et créé avec Visual Studio 2012

Si votre application a été créée dans Visual Studio 2012, vous avez utilisé probablement l’identité et l’outil d’accès pour configurer votre application. Il est également possible que vous utilisez le [Registre de nom de l’émetteur validation (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). Le VINR est responsable de la mise à jour d’informations sur le fournisseur d’identité approuvé (Azure AD) et les clés utilisées pour valider les jetons émis par eux. La VINR facilite également mettre à jour automatiquement les informations de clé stockées dans un fichier Web.config en téléchargeant le dernier document de métadonnées de fédération associé à votre répertoire, vérifier si la configuration est à jour avec le dernier document et mise à jour de l’application à utiliser la nouvelle clé que nécessaire.

Si vous avez créé votre application à l’aide de la documentation de procédure pas à pas fourni par Microsoft ou les exemples de code, la logique de substitution de la clé est déjà incluse dans votre projet. Vous remarquerez que le code ci-dessous existe déjà dans votre projet. Si votre application n’a pas déjà cette logique, suivez les étapes ci-dessous pour ajouter et vérifier qu’il fonctionne correctement.

1. Dans l' **Explorateur de solutions**, ajoutez une référence à l’assembly **System.IdentityModel** pour le projet approprié.
2. Ouvrez le fichier **Global.asax.cs** et ajoutez les éléments suivants à l’aide de directives :
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Ajoutez la méthode suivante pour le fichier **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Appeler la méthode **RefreshValidationSettings()** dans la méthode **Application_Start()** dans **Global.asax.cs** comme indiqué :
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Une fois que vous avez suivi ces étapes, proposée est mise à jour avec les dernières informations sur le document de métadonnées fédération, y compris les dernières clés. Cette mise à jour se produira chaque fois que votre pool d’applications réutilise dans IIS ; par défaut IIS est défini sur Corbeille applications toutes les heures 29.

Suivez les étapes ci-dessous pour vérifier que la logique de substitution de la clé fonctionne.

1. Après avoir vérifié que votre application utilise le code ci-dessus, ouvrez le fichier **Web.config** et accédez à la **<issuerNameRegistry>** bloc, recherchez spécifiquement les éléments suivants premières lignes :
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. Dans la **<add thumbprint=””>** définition, modifie la valeur de l’empreinte numérique en remplaçant un caractère par une autre. Enregistrez le fichier **Web.config** .

3. Créer l’application, puis exécutez-le. Si vous pouvez terminer le processus de connexion, votre application est mise à jour de la clé de télécharger les informations requises à partir du document de métadonnées de fédération de votre annuaire. Si vous rencontrez des problèmes de connexion, assurez-vous que les modifications dans votre application sont correctes en lisant la rubrique [Ajout d’authentification à votre utilisation d’Application Web Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) , ou en téléchargeant et en inspectant l’exemple de code suivant : [Application en nuage client multiples pour Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Protection des ressources et créé avec Visual Studio 2008 ou 2010 des applications Web et Windows Identity Foundation (WIF) version 1.0 pour .NET 3.5

Si vous avez créé une application sur la version 1.0 WIF, il n’existe aucun mécanisme fournie pour actualiser automatiquement la configuration de votre application pour utiliser une nouvelle clé.

- *En toute simplicité* Utilisez les outils FedUtil inclus dans le SDK WIF, qui peut extraire le dernier document de métadonnées et mettre à jour votre configuration.
- Mettre à jour votre application pour .NET 4.5, qui inclut la version la plus récente de WIF situé dans l’espace de noms système. Vous pouvez ensuite utiliser la [Validation de l’émetteur nom du Registre (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) pour effectuer des mises à jour automatiques de configuration de l’application.
- Effectuer un survol manuel selon les instructions à la fin du présent document d’orientation.

Instructions pour la FedUtil permet de mettre à jour votre configuration :

1. Vérifiez que vous disposez de la version 1.0 WIF SDK installé sur votre ordinateur de développement pour Visual Studio 2008 ou 2010. Vous pouvez [télécharger à partir de là](https://www.microsoft.com/en-us/download/details.aspx?id=4451) si vous n’avez pas encore installé dessus.
2. Dans Visual Studio, ouvrez la solution, puis droit sur le projet applicable et sélectionnez **mettre à jour les métadonnées de fédération**. Si cette option n’est pas disponible, FedUtil et/ou la version 1.0 WIF SDK n'a pas été installé.
3. Dans l’invite, sélectionnez **mise à jour** pour mettre à jour les métadonnées de votre fédération. Si vous avez accès à l’environnement du serveur dans lequel l’application est hébergée, vous pouvez éventuellement utiliser de FedUtil [Planificateur de mise à jour automatique des métadonnées](https://msdn.microsoft.com/library/ee517272.aspx).
4. Cliquez sur **Terminer** pour terminer le processus de mise à jour.

### <a name="other"></a>Applications Web / API de protection des ressources à l’aide de toutes les bibliothèques ou implémenter manuellement les protocoles pris en charge

Si vous utilisez une autre bibliothèque ou implémentée manuellement les protocoles pris en charge, vous devez passer en revue la bibliothèque ou votre implémentation afin de garantir que la clé est en cours récupérée dans le document de découverte OpenID se connecter ou le document de métadonnées de fédération. Vérifier pendant ce consiste à effectuer une recherche dans votre code ou de la bibliothèque pour tous les appels arrière vers le document de découverte OpenID ou le document de métadonnées de fédération.

Si la clé est stockée ailleurs ou codé en dur dans votre application, vous pouvez manuellement récupérer la clé et la mise à jour il en conséquence effectuer un survol manuel selon les instructions à la fin du présent document d’orientation. **Il est vivement recommandé que vous améliorer votre application pour prendre en charge la substitution automatique** à l’aide d’une des approches plans dans cet article pour éviter toute interruption futures et aérienne si Azure AD augmente il est cadence survol ou a un survol de hors d’urgence.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Comment tester votre application pour déterminer si elle est affectée.

Vous pouvez vérifier si votre application prend en charge la substitution automatique en téléchargeant les scripts et en suivant les instructions de [ce référentiel GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Comment effectuer un survol manuel si votre application ne prend pas en charge survol automatique

Si votre application fait **pas** prise en charge survol automatique, vous avez besoin d’établir un processus qui régulièrement surveille clés de signature de Azure AD et exécute un survol manuel en conséquence. [Ce GitHub référentiel](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contient des scripts et obtenir des instructions sur la procédure à suivre.

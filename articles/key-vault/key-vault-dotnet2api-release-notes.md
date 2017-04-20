<properties
   pageTitle="Notes de publication de l’archivage sécurisé .NET 2.x API de clé | Microsoft Azure"
   description="Les développeurs .NET utiliseront cette API au code pour l’archivage sécurisé de clé Azure"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Archivage de sécurisé de clés Azure .NET 2.0 - Guide de Migration et les Notes de publication

Les notes et les conseils suivants sont pour les développeurs qui travaillent avec Azure clé l’archivage sécurisé .NET / bibliothèque c#. Dans la modification de la 1.0 version à la version 2.0, un certain nombre de mises à jour ont été apporté seront nécessitent migration de travail dans votre code afin que vous permettent de tirer parti des améliorations fonctionnelles et ainsi que des ajouts tels que prise en charge de l’archivage sécurisé clé certificats.

Prise en charge de l’archivage sécurisé clé certificats offre pour la gestion de votre x509 certificats et les comportements suivants :  

-   Permet un propriétaire de certificat créer un certificat via un processus de création de l’archivage sécurisé clé ou via l’importation d’un certificat existant. Cela inclut les deux auto-signé et autorité de certification généré certificats.

- Permet un propriétaire de certificat de l’archivage sécurisé clé implémenter un stockage sécurisé et la gestion des X509 certificats sans interaction avec une clé privée.  

-   Permet un propriétaire de certificat créer une stratégie qui achemine l’archivage sécurisé clé pour gérer le cycle de vie d’un certificat.  

-   Permet aux propriétaires de certificat fournir des informations de contact pour la notification sur les événements de cycle de vie d’expiration et de renouvellement de certificat.  

-   Prend en charge le renouvellement automatique avec émetteur sélectionné - l’archivage sécurisé clé partenaire X509 fournisseurs de certificats / Autorités de certification.
    - Remarque - fournisseurs/références Non associés sont également autorisées mais, ne prend pas en charge la fonctionnalité de renouvellement automatique.


## <a name="net-support"></a>Prise en charge .NET
- **.NET 4.0** n’est pas pris en charge par la version 2.0 de l’archivage sécurisé de clé Azure .NET / bibliothèque c#
- **.NET Core** est pris en charge par la version 2.0 de l’archivage sécurisé de clé Azure .NET / bibliothèque c#

## <a name="namespaces"></a>Espaces de noms
- L’espace de noms pour **les modèles** d’est passé de **Microsoft.Azure.KeyVault** à **Microsoft.Azure.KeyVault.Models**.
- L’espace de noms **Microsoft.Azure.KeyVault.Internal** est supprimée.
- L’espace de noms de dépendances Azure SDK sont modifiées à partir de **Hyak.Common** et **Hyak.Common.Internals** **Microsoft.Rest** et **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Modifications de type
- *Code secret* modifié en *SecretBundle*
- *Dictionnaire* modifié à *IDictionary*
- *Liste<T>, string []* modifiées en *IList<T> *
- *NextList* modifiées en *NextPageLink*


## <a name="return-types"></a>Types de retour
- Renvoient une **KeyList** et **SecretList** *IPage<T> * au lieu de *ListKeysResponseMessage*
- La généré **BackupKeyAsync** retournera *BackupKeyResult* qui contient la *valeur* (sauvegarde blob). Avant la méthode encapsulée et que la valeur de renvoi.

## <a name="exceptions"></a>Exceptions
- *KeyVaultClientException* est modifié en *KeyVaultErrorException*
- L’erreur de service est modifié d’exception *. Erreur* à *exception. Body.Error.Message*.
- Supprimer des informations supplémentaires à partir du message d’erreur pour **[JsonExtensionData]**.

## <a name="constructors"></a>Constructeurs
- Au lieu d’accepter une *HttpClient* comme un argument de constructeur, le constructeur accepte uniquement *HttpClientHandler* ou *DelegatingHandler []*.



## <a name="downloaded-packages"></a>Modules téléchargés  
Lorsqu’un client traite une dépendance de clé de l’archivage sécurisé les éléments suivants ont été téléchargés
#### <a name="previous-package-list"></a>Liste de package précédente
- version du package id="Hyak.Common » = « 1.0.2 » targetFramework = « net45 »
- version du package id="Microsoft.Azure.Common » = « 2.0.4 » targetFramework = « net45 »
- version du package id="Microsoft.Azure.Common.Dependencies » = « 1.0.0 » targetFramework = « net45 »
- version du package id="Microsoft.Azure.KeyVault » = « 1.0.0 » targetFramework = « net45 »
- version du package id="Microsoft.Bcl » = « 1.1.9 » targetFramework = « net45 »
- version du package id="Microsoft.Bcl.Async » = « 1.0.168 » targetFramework = « net45 »
- version du package id="Microsoft.Bcl.Build » = « 1.0.14 » targetFramework = « net45 »
- version du package id="Microsoft.Net.Http » = « 2.2.22 » targetFramework = « net45 »

#### <a name="current-package-list"></a>Liste de package actuelle
- version du package id="Microsoft.Azure.KeyVault » = « 2.0.0-preview » targetFramework = « net45 »
- version du package id="Microsoft.Rest.ClientRuntime » = « 2.2.0 » targetFramework = « net45 »
- version du package id="Microsoft.Rest.ClientRuntime.Azure » = « 3.2.0 » targetFramework = « net45 »


## <a name="class-changes"></a>Modification d’une classe

- Classe **UnixEpoch** a été supprimée
- Classe **Base64UrlConverter** est renommé en **Base64UrlJsonConverter**

## <a name="other-changes"></a>Autres modifications

- Prise en charge de la configuration de stratégie de nouvelles tentatives d’opération KV concernant les échecs transitoires a été ajouté à cette version de l’API.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- Pour les opérations qui retourné un *l’archivage sécurisé*, le type de retour a été une classe contenant une propriété de l’archivage sécurisé. Le type de retour est désormais *l’archivage sécurisé*.
- *PermissionsToKeys* et *PermissionsToSecrets* sont à présent *Permissions.Keys* et *Permissions.Secrets*
- Certaines des modifications types de retour s’appliquent au contrôle plan également.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- Le package est incorrecte **Microsoft.Azure.KeyVault.Extensions** et **Microsoft.Azure.KeyVault.Cryptography** pour les opérations de chiffrement.

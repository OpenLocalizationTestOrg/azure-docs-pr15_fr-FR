<properties 
    pageTitle="Service Bus authentification et autorisation | Microsoft Azure"
    description="Vue d’ensemble de l’authentification partagé Access Signature (sa)."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Autorisation et Bus de Service d’authentification

Les applications peuvent s’authentifier à Bus des services Azure à l’aide de l’authentification de Signature accès partagé (sa) ou via Azure Active Directory contrôle d’accès (également appelé Service de contrôle d’accès ou ACS). Partagé Signature Access authentification permet aux applications de s’authentifier sur Bus des services à l’aide d’une touche d’accès configurée sur l’espace de noms, ou sur l’entité auquel des droits spécifiques sont associées. Vous pouvez ensuite utiliser cette clé pour générer un jeton de Signature de Access partagés clients peuvent utiliser pour s’authentifier sur Bus des services.

>AZURE. Associations de sécurité importantes est préférable ACS, car elle fournit un schéma d’authentification simple, flexible et facile à utiliser Bus des services. Les applications peuvent utiliser les associations de sécurité dans les scénarios dans lesquels ils n’avez pas besoin gérer la notion d’un conseillers « utilisateurs ».

## <a name="shared-access-signature-authentication"></a>Authentification de Signature accès partagée

[Authentification associations de sécurité](service-bus-sas-overview.md) vous permet d’accorder un accès utilisateur aux ressources Bus des services avec des droits spécifiques. Authentification de sa dans Service marché implique la configuration d’une clé de chiffrement avec des droits associés sur une ressource Bus des services. Les clients peuvent alors accéder à cette ressource en présentant un jeton de sa qui se compose de la ressource URI en cours d’accès et d’expiration signés avec la touche configurée.

Vous pouvez configurer des clés pour les associations de sécurité dans un espace de noms Bus des services. La clé s’applique à toutes les entités messageries dans cet espace de noms. Vous pouvez également configurer clés sur des sujets et files d’attente Bus des services. Associations de sécurité sont également prise en charge sur Bus de Service de relais.

Pour utiliser les associations de sécurité, vous pouvez configurer un objet [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) dans un espace de noms, la file d’attente ou la rubrique qui comprend les éléments suivants :

- *Nom* qui identifie la règle.

- *PrimaryKey* est une clé de chiffrement permettant de se/valider jetons associations de sécurité.

- *SecondaryKey* est une clé de chiffrement permettant de se/valider jetons associations de sécurité.

- *Droits* représentant la collection d’écouter, envoyer ou gérer droits concédés.

Règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les entités dans un espace de noms pour les clients avec jetons signés à l’aide de la clé correspondante. Jusqu'à 12 autorisation règles peuvent être configurées sur un espace de noms, file d’attente ou rubrique Bus des services. Par défaut, un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) avec tous les droits est configuré pour chaque espace de noms lorsqu’il est tout d’abord mis en service.

Pour accéder à une entité, le client nécessite un jeton de sa généré à l’aide d’un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)spécifique. Le jeton associations de sécurité est généré à l’aide de la HMAC-SHA256 d’une chaîne de ressources qui se compose de l’URI ressource à laquelle l’accès est demandé et d’expiration avec une clé de chiffrement associée à la règle d’autorisation.

Prise en charge de l’authentification sa Bus des services est inclus dans les versions Azure .NET SDK 2.0 et versions ultérieures. Associations de sécurité prend en charge un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Toutes les API acceptant une chaîne de connexion en tant que paramètre incluent prise en charge des chaînes de connexion associations de sécurité.

## <a name="acs-authentication"></a>Authentification ACS

Authentification Bus des services via ACS est gérée tout au long accompagnée d’une «-petite « espace de noms ACS. Si vous souhaitez un espace de noms sont remplis ACS par un espace de noms Bus des services, vous ne pouvez pas créer votre espace de noms Bus des services à l’aide du portail classique Azure ; Vous devez créer l’espace de noms à l’aide de l’applet de commande PowerShell [New-AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) . Par exemple :

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Pour éviter de créer un espace de noms ACS, exécutez la commande suivante :

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Par exemple, si vous créez un espace de noms de Service Bus appelé **contoso.servicebus.windows.net**, un espace de noms sont remplis ACS appelée **contoso-sb.accesscontrol.windows.net** est configuré automatiquement. Pour tous les espaces de noms qui ont été créés avant d’août 2014, un espace de noms ACS accompagnement a été également créé.

Une identité de service par défaut « propriétaire », avec tous les droits, est configurée par défaut dans cet espace de noms sont remplis ACS. Vous pouvez obtenir un contrôle affiné à toute entité Bus des services via ACS en configurant les relations d’approbation approprié. Vous pouvez configurer des identités de service supplémentaire pour gérer l’accès aux entités Bus des services.

Pour accéder à une entité, le client demande un jeton SWT ACS avec les revendications appropriées en présentant ses informations d’identification. Le jeton SWT doit puis envoyé dans le cadre de la demande à Bus des services pour activer l’autorisation du client pour l’accès à l’entité.

Prise en charge de l’authentification ACS Bus des services est inclus dans les versions Azure .NET SDK 2.0 et versions ultérieures. Ce type d’authentification prend en charge un [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Toutes les API acceptant une chaîne de connexion en tant que paramètre incluent prise en charge des chaînes de connexion ACS.

## <a name="next-steps"></a>Étapes suivantes

Poursuivez la lecture de [l’authentification partagé Access Signature à Service](service-bus-shared-access-signature-authentication.md) pour plus d’informations sur les associations de sécurité.

Pour obtenir une vue d’ensemble de sa dans Service marché, voir [Partagés accès Signatures](service-bus-sas-overview.md).

Vous pouvez trouver plus d’informations sur les jetons ACS dans [Comment : demander un jeton ACS via le protocole renvoyer à la ligne OAuth](https://msdn.microsoft.com/library/hh674475.aspx).




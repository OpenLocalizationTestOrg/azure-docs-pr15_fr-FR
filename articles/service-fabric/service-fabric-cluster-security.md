<properties
   pageTitle="Banque d’informations sécurisé un cluster de Service TISSU | Microsoft Azure"
   description="Décrit les scénarios de sécurité pour un cluster de tissu de Service et les différentes technologies utilisées pour implémenter ces scénarios."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Scénarios de sécurité cluster tissu de service

Un cluster de Service tissu est une ressource qui vous appartient. Clusters doivent toujours être sécurisées pour empêcher les utilisateurs non autorisés de se connecter à votre cluster, en particulier lorsqu’il a charges de travail en cours d’exécution dessus. Bien qu’il soit possible de créer un cluster non sécurisé, pratique permettra donc tout utilisateur anonyme vous y connecter s’il expose des points de terminaison de gestion à l’Internet public. 

Cet article fournit une vue d’ensemble des scénarios de sécurité pour les clusters s’exécutant sur Azure ou autonome et les différentes technologies utilisées pour implémenter ces scénarios. Les scénarios de sécurité cluster sont :

- Nœud-à-nœud sécurité
- Sécurité client-à-nœud
- Contrôle d’accès basé sur un rôle (RBAC)

## <a name="node-to-node-security"></a>Nœud-à-nœud sécurité
Permet de sécuriser les communications entre les machines virtuelles ou machines dans le cluster. Cela garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à héberger des applications et services dans le cluster.

![Diagramme de communication nœud à nœud][Node-to-Node]

Clusters s’exécutant sur clusters Azure ou autonome fonctionnant sous Windows peuvent utiliser [Certificat de sécurité](https://msdn.microsoft.com/library/ff649801.aspx) ou [Sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx) pour les ordinateurs Windows Server.
### <a name="node-to-node-certificate-security"></a>Nœud-à-nœud certificats de sécurité
Service TISSU utilise les certificats de serveur X.509 que vous spécifiez dans le cadre de la configuration du type de nœud lorsque vous créez un cluster. Un bref aperçu de ce que sont ces certificats et comment vous pouvez acquérir ou créez-les est fourni à la fin de cet article.

Des certificats de sécurité est configuré lors de la création du cluster via le portail Azure, le Gestionnaire de ressources Azure modèles ou un modèle JSON autonome. Vous pouvez spécifier un certificat principal et un certificat secondaire facultatif qui est utilisé pour le survol certificat. Les certificats principales et secondaires que vous spécifiez doivent être différentes de celle du client d’administration, les certificats client en lecture seule que vous spécifiez pour la [sécurité Client-à-nœud](#client-to-node-security).

Pour Azure lisez [configurer un cluster à l’aide d’un modèle Azure le Gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) pour apprendre à configurer des certificats de sécurité dans un cluster.

Pour autonome Windows Server lire [banque d’informations sécurisé un cluster autonome sur Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Sécurité nœud-à-nœud windows
Pour autonome Windows Server lire [banque d’informations sécurisé un cluster autonome sur Windows à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Sécurité client-à-nœud
Authentifie les clients et protège les communications entre un client et des nœuds individuels dans le cluster. Ce type de sécurité authentifie et sécurise les communications client, qui garantit que seuls les utilisateurs autorisés peuvent accéder au cluster et les applications déployées sur le cluster. Les clients sont identifiées par le biais leurs informations d’identification de sécurité Windows ou leurs informations d’identification de sécurité de certificat.

![Diagramme de communication client-à-nœud][Client-to-Node]

Clusters s’exécutant sur clusters Azure ou autonome fonctionnant sous Windows peuvent utiliser [Certificat de sécurité](https://msdn.microsoft.com/library/ff649801.aspx) ou [La sécurité de Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Sécurité de certificat client-à-nœud
 Certificat client-à-nœud sécurité est configurée lors de la création du cluster soit via le portail Azure, le Gestionnaire de ressources modèles ou un modèle JSON autonome en spécifiant un certificat client administrateur et/ou un certificat client utilisateur.  L’administrateur client et utilisateur client certificats que vous spécifiez doivent être différents de celui les certificats principales et secondaires que vous spécifiez pour la [sécurité de nœud à nœud](#node-to-node-security).

Clients qui se connectent au cluster en utilisant le certificat d’administration ont un accès intégral aux fonctionnalités de gestion des.  Clients qui se connectent au cluster en utilisant le certificat client utilisateur en lecture seule ont accès en lecture seule aux fonctions de gestion. En d’autres termes, ces certificats sont utilisés pour le rôle bases accès contrôle de décrites plus loin dans cet article.

Pour Azure lisez [configurer un cluster à l’aide d’un modèle Azure le Gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) pour apprendre à configurer des certificats de sécurité dans un cluster.

Pour autonome Windows Server lire [banque d’informations sécurisé un cluster autonome sur Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Sécurité Azure Active Directory (DAS) client-à-nœud sur Azure
Clusters s’exécutant sur Azure peuvent également sécuriser l’accès aux points de terminaison gestion à l’aide d’Azure Active Directory (DAS). Pour plus d’informations sur la façon créer les objets AAD nécessaires, comment faire pour remplir les lors de la création de cluster et comment vous connecter à ces clusters par la suite, voir [configurer un cluster à l’aide d’un modèle Azure le Gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) .

## <a name="security-recommendations"></a>Recommandations de sécurité
Pour les clusters Azure, il est recommandé d’utiliser sécurité AAD s’authentifier clients, les certificats pour la sécurité de nœud à nœud.

Pour autonome Windows Server clusters, il est recommandé d’utiliser la sécurité de Windows avec groupe les comptes gérés (GMA) si vous avez Windows Server 2012 R2 et Active Directory. Dans le cas contraire toujours utiliser la sécurité de Windows avec les comptes Windows.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur un rôle (RBAC)
Contrôle d’accès permet à l’administrateur de cluster limiter l’accès à certaines opérations cluster pour différents groupes d’utilisateurs, ce qui rend le cluster plus sécurisée. Deux types de contrôle d’accès différents sont prises en charge pour les clients qui se connectent à un cluster : rôle d’administrateur et le rôle d’utilisateur.

Les administrateurs ont un accès intégral aux fonctionnalités de gestion des (y compris les fonctionnalités en lecture/écriture). Les utilisateurs, par défaut, ont uniquement accès en lecture aux fonctionnalités de gestion des (par exemple, des fonctions de requête) et la possibilité pour résoudre les applications et services.

Spécifier les rôles de client administrateurs et les utilisateurs au moment de la création d’un cluster en fournissant des identités séparées (certificats, AAD etc.) pour chacun. Pour plus d’informations sur les paramètres de contrôle d’accès par défaut et comment modifier les paramètres par défaut, voir [contrôle d’accès pour les clients tissu de Service basé sur un rôle](service-fabric-cluster-security-roles.md).


## <a name="x509-certificates-and-service-fabric"></a>Les certificats X.509 et tissu de Service
Certificats numériques X.509 sont fréquemment utilisées pour authentifier les clients et les serveurs et pour chiffrer et signer numériquement les messages. Pour plus d’informations sur ces certificats, accédez à [l’utilisation des certificats](http://msdn.microsoft.com/library/ms731899.aspx).

Quelques points importants à prendre en compte :

- Certificats utilisés dans les clusters charges de travail en cours d’exécution doivent être créés à l’aide d’un service de certificat Windows Server correctement configuré ou obtenus à partir d’une [Autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority)approuvées.
- Ne jamais utiliser n’importe quel temporaire ou certificats de test de production qui sont créés avec les outils tels que MakeCert.exe.
- Vous pouvez utiliser un certificat auto-signé, mais que vous devez uniquement le faire pour les clusters de test et non dans production.

### <a name="server-x509-certificates"></a>Certificats X.509 serveur

Les certificats de serveur ont la tâche principale d’un serveur (nœud) pour les clients d’authentification ou d’authentifier un serveur (nœud) à un serveur (nœud). Parmi les contrôles initiaux lorsqu’un nœud ou un client s’authentifie un nœud consiste à vérifier la valeur du nom commun dans le champ objet. Ce nom courantes ou des noms de remplacement du sujet du certificat doivent être présent dans la liste des noms communs autorisés.

L’article suivant explique comment générer des certificats des noms de remplacement d’objet (SAN) : [comment ajouter un autre nom du sujet à un certificat LDAP sécurisé](http://support.microsoft.com/kb/931351).

Le champ objet peut contenir plusieurs valeurs, chacune précédé de l’initialisation pour indiquer le type de valeur. En règle générale, l’initialisation est « CN » pour nom commun ; par exemple, « CN = www.contoso.com ». Il est également possible que le champ objet est vide. Si le champ nom de remplacement sujet facultatif est rempli, elle doit contenir le nom du certificat commun et une entrée par un autre nom du sujet. Ils sont entrés en tant que valeurs nom DNS.

La valeur du champ rôles prévus du certificat doit inclure une valeur appropriée, par exemple « Serveur authentification » ou « Client ».

### <a name="client-x509-certificates"></a>Certificats X.509 client

Les certificats clients ne sont pas en général émis par une autorité de certification tierce. En revanche, le magasin personnel de l’emplacement actuel de l’utilisateur contient généralement les certificats client placées à cet endroit par une autorité racine, avec un objectif initial de « Authentification du Client ». Le client peut utiliser ce certificat lors de l’authentification commun est requise.

>[AZURE.NOTE] Toutes les opérations de gestion sur un cluster de Service TISSU requièrent des certificats serveur. Certificats clients ne peuvent pas être utilisés pour la gestion.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Étapes suivantes

Cet article fournit des informations conceptuelles sur la sécurité de cluster. Ensuite, [créez un cluster dans Azure à l’aide d’un modèle de gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) ou via le [portail Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

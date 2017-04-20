<properties
   pageTitle="Scénarios et des exemples de gouvernance de l’abonnement | Microsoft Azure"
   description="Fournit des exemples d’implémentation de gouvernance abonnement Azure pour des scénarios courants."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exemples d’implémentation structure entreprise Azure

Cette rubrique fournit des exemples de comment une entreprise peut implémenter les recommandations pour une [structure entreprise Azure](resource-manager-subscription-governance.md). Il utilise une société fictive nommée Contoso pour illustrer les meilleures pratiques pour les scénarios courants. 

## <a name="background"></a>Arrière-plan

Contoso est une société dans le monde entier qui propose des solutions de chaîne d’approvisionnement des clients de tous les éléments à partir d’un modèle « Logiciel en tant que Service » à un modèle de package déployé en local.  Ils développement des logiciels dans le monde centres de développement significative en Inde, États-Unis et Canada. 

La partie fil de la société est divisée en plusieurs divisions indépendantes qui gèrent les produits dans une entreprise significative. Chaque division possède son propre développeurs, responsables de produits et des architectes. 

La division de Services de technologie d’entreprise (ETS) donne la capacité informatique centralisée et gère plusieurs centres de données où divisions hébergent leurs applications. Tout en gérant les centres de données, l’organisation ETS fournit et gère centraliser la collaboration (par exemple, le courrier électronique et sites Web) et les services de réseau/téléphonie. Ils également gérer les charges de travail côté client pour les unités commerciales plus petites qui n’ont pas personnel. 

Personnage suivantes est utilisées dans cette rubrique :

- Dave est l’administrateur ETS Azure.
- Alice est directeur de développement de Contoso de la division de chaîne d’approvisionnement. 

Contoso doit créer une application métier d’et une application côté client. Il a décidé d’exécuter les applications sur Azure. Dave lit la rubrique [gouvernance abonnement précises](resource-manager-subscription-governance.md) et est maintenant prêt à mettre en œuvre les recommandations. 

## <a name="scenario-1-line-of-business-application"></a>Scénario 1 : métier d’application

Contoso est création d’un système de gestion de code source (BitBucket) devant être utilisé par les développeurs du monde entier.  L’application utilise l’Infrastructure en tant que Service (IaaS) pour héberger et se compose de serveurs web et un serveur de base de données. Les développeurs accéder aux serveurs dans leur environnement de développement, mais ils ne doivent pas accéder aux serveurs dans Azure. Contoso ETS souhaite autoriser le propriétaire de l’application et l’équipe gérer l’application. L’application n’est disponible lors de la connexion réseau d’entreprise de Contoso. Dave doit configurer l’abonnement pour cette application. L’abonnement héberge également d’autres logiciels développeur liés à l’avenir.  

### <a name="naming-standards--resource-groups"></a>Normes de noms et des groupes de ressources

Dave crée un abonnement pour prendre en charge des outils de développement qui sont communes à toutes les divisions. Il a besoin créer des noms parlants pour les groupes d’abonnement et des ressources (pour l’application et les réseaux). Il crée les groupes d’abonnement et des ressources suivantes :

| Élément | Nom | Description |
| ---- | ---- | ----------- |
| Abonnement | Contoso ETS DeveloperTools Production | Prend en charge les outils de développement courants |
| Groupe de ressources | rgBitBucket | Contient le serveur web d’application et le serveur de base de données |
| Groupe de ressources | rgCoreNetworks | Contient des réseaux virtuels et connexion site à passerelle |


### <a name="role-based-access-control"></a>Contrôle d’accès basé sur un rôle

Après la création de son abonnement, Dave souhaite s’assurer que les équipes appropriés et les propriétaires d’applications peuvent accéder à leurs ressources. Dave reconnaît que chaque équipe a des besoins différents. Il utilise les groupes qui ont été synchronisés à Azure Active Directory de Contoso locales Active Directory (AD) et fournit le niveau d’accès aux équipes de droite. 

Dave affecte les rôles suivants pour l’abonnement : 

| Rôle | Affecté à | Description |
| ---- | ----------- | ----------- |
| [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) | Gèrent ID à partir de Contoso AD | Cet identifiant est contrôlé avec juste dans access heure (juste) via l’outil de gestion des identités de Contoso et garantit que l’accès propriétaire de l’abonnement est entièrement audit. |
| [Gestionnaire de sécurité](./active-directory/role-based-access-built-in-roles.md#security-manager) | Service de gestion des risques et | Ce rôle permet aux utilisateurs d’examiner le centre de sécurité Azure et l’état des ressources. |
| [Collaboration de réseau](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Équipe de réseau | Ce rôle permet d’équipe de réseau de Contoso gérer le réseau privé virtuel à un Site et les réseaux virtuels. |
| *Rôle personnalisé* | Propriétaire de l’application | Dave crée un rôle qui accorde la possibilité de modifier des ressources dans le groupe de ressources. Pour plus d’informations, voir [Rôles personnalisée dans Azure RBAC](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Stratégies

Dave comporte les conditions suivantes pour la gestion des ressources de l’abonnement :

- Les outils de développement prenant en charge les développeurs dans le monde entier, il ne souhaite pas empêcher les utilisateurs de création de ressources dans n’importe quelle région. Toutefois, il a besoin de savoir où les ressources sont créées. 
- Il est concerné aux coûts. Par conséquent, il souhaite empêcher les propriétaires de l’application de création de machines virtuelles inutilement coûteux.  
- Étant donné que cette application gère les développeurs de nombreuses divisions, il souhaite ajouter des balises à chaque ressource avec le propriétaire d’entreprise unité et d’application. À l’aide de ces balises, ETS pouvez bill les équipes appropriés.

Il crée [le Gestionnaire de ressources stratégies](resource-manager-policy.md)suivantes : 

| Champ | Effet | Description |
| ----- | ------ | ----------- |
| emplacement | audit | Vérification de la création des ressources dans n’importe quelle région |
| type | refuser | Refuser la création de machines virtuelles série G |
| balises | refuser | Exiger la balise de propriétaire de l’application |
| balises | refuser | Nécessite de balise de centre de coût |
| balises | ajouter | Ajouter le nom de balise **division** et la valeur de balise **ETS** à toutes les ressources |


### <a name="resource-tags"></a>Balises de ressources

Dave prend en charge qu’il doit comporter des informations spécifiques sur la facture pour identifier le centre de coût pour la mise en œuvre BitBucket. En outre, Dave souhaite connaître toutes les ressources qui possède ETS. 

Il ajoute les [balises](resource-group-using-tags.md) suivantes pour les groupes de ressources et les ressources. 
 
| Nom de balise | Valeur de la balise |
| -------- | --------- |
| ApplicationOwner | Le nom de la personne qui gère cette application. |
| Centre de coût | Centre de coût du groupe est un abonnement payant la consommation Azure. |
| Division | **ETS** (la division associée à l’abonnement) |

### <a name="core-network"></a>Réseau principal

L’équipe de gestion Contoso ETS informations sécurité et les risques examine plan proposé de Dave pour déplacer l’application vers Azure. Qu’ils souhaitent pour vous assurer que l’application n’est pas visible sur internet.  Dave est également des applications de développeur à l’avenir seront déplacées vers Azure. Ces applications nécessitent des interfaces publiques.  Pour répondre à ces exigences, il fournit des réseaux virtuels internes et externes et un groupe de sécurité réseau pour restreindre l’accès.

Il crée les ressources suivantes :

| Type de ressource | Nom | Description |
| ------------- | ---- | ----------- |
| Réseau virtuel | vnInternal | Utilisé avec l’application BitBucket et est connecté au réseau d’entreprise de Contoso via ExpressRoute.  Un sous-réseau (sbBitBucket) fournit l’application avec un espace d’adressage IP spécifique. |
| Réseau virtuel | vnExternal | Disponible pour les futures applications nécessitant des points de terminaison au public. |
| Groupe de sécurité réseau | nsgBitBucket | Garantit que les attaques de cette charge de travail sont réduit en autorisant les connexions uniquement sur le port 443 pour le sous-réseau où l’application réside (sbBitBucket). |

### <a name="resource-locks"></a>Verrouillages des ressources 

Dave reconnaît que la connectivité du réseau d’entreprise de Contoso au réseau virtuel interne doit être protégée à partir de n’importe quel script versatiles ou de suppression accidentelle. 

Il crée le [verrouillage de la ressource](resource-group-lock-resources.md)suivante :

| Type de verrou | Ressource | Description |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Empêche les utilisateurs de supprimer le réseau virtuel ou sous-réseaux, mais n’empêche pas l’ajout de nouveaux sous-réseaux. |

### <a name="azure-automation"></a>Automatisation Azure 

Dave ne comporte rien pour automatiser pour cette application. Bien qu’il a créé un compte Azure automatisation, il ne l’utiliser au départ. 

### <a name="azure-security-center"></a>Centre de sécurité Azure 

Gestion des services informatiques de Contoso doit identifier rapidement et de gérer les menaces. Ils veulent également comprendre quels sont les problèmes éventuels.  

Pour répondre à ces exigences, Dave permet le [Centre de sécurité Azure](./security-center/security-center-intro.md)et offre un accès en tant que responsable de sécurité. 

## <a name="scenario-2-customer-facing-app"></a>Scénario 2 : l’application côté client

La direction d’entreprise dans la division de chaîne d’approvisionnement a identifié les différentes possibilités d’améliorer l’engagement avec les clients de Contoso à l’aide d’une carte de fidélité. Équipe Alice doit créer cette application et décide que Azure augmente leur capacité à répondre aux besoins de l’entreprise. Alice fonctionne avec Dave de ETS pour configurer les deux abonnements de développement et d’utilisation de cette application.

### <a name="azure-subscriptions"></a>Abonnements Azure 

Dave ouvre une session le portail d’entreprise Azure et voit que le service de chaîne d’approvisionnement existe déjà.  Toutefois, comme le premier projet de développement de l’équipe de chaîne d’approvisionnement dans Azure ce projet, Dave reconnaît la nécessité d’un nouveau compte pour l’équipe de développement d’Alice.  Il crée le compte « R & D » pour son équipe et attribue un accès à Alice. Alice se connecte via le portail de compte et crée deux abonnements : une pour conserver les serveurs de développement et l’autre pour contenir les serveurs de production.  Elle suit normes de dénomination établies précédemment lorsque vous créez les abonnements suivants : 

| Utilisation de l’abonnement | Nom |
| ---------------- | ---- |
| Développement | Développement de LoyaltyCard SupplyChain ResearchDevelopment |
| Production | SupplyChain opérations LoyaltyCard Production |

### <a name="policies"></a>Stratégies

Dave et Alice discuter l’application et d’identifient que cette application sert uniquement aux clients dans la région Amérique du Nord.  Alice et son équipe planifier l’utilisation de Azure Application Service environnement et SQL Azure pour créer l’application. Il faudra créer des machines virtuelles pendant le développement.  Catherine veut qu’à faire en sorte que son les ressources que dont elles ont besoin pour Explorer et examiner les problèmes sans tirant dans ETS. 

Pour l' **abonnement de développement**, ils créent les stratégies suivantes :

| Champ | Effet | Description |
| ----- | ------ | ----------- |
| emplacement | audit | La création des ressources dans n’importe quelle région l’audit. |

Ils ne pas limitent le type de référence (SKU) un utilisateur peut créer dans le développement, et ils ne nécessitent pas de balises pour les groupes de ressources ou des ressources.

Pour l' **abonnement de production**, ils créent des stratégies suivantes :

| Champ | Effet | Description |
| ----- | ------ | ----------- |
| emplacement | refuser | Refuser la création de toutes les ressources en dehors des centres de données des États-Unis. |
| balises | refuser | Exiger la balise de propriétaire de l’application |
| balises | refuser | Nécessite de balise de service. | 
| balises | ajouter | Ajouter des balises à chaque groupe de ressources qui indique l’environnement de production. |

Ils ne pas limitent le type de référence (SKU) un utilisateur peut créer en production.

### <a name="resource-tags"></a>Balises de ressources 

Dave prend en charge qu’il doit comporter des informations spécifiques pour identifier les groupes d’entreprise correct pour la facturation et la propriété. Il définit les balises de ressources pour les groupes de ressources et des ressources. 
 
Nom de balise | Valeur de la balise |
| -------- | --------- |
| ApplicationOwner | Le nom de la personne qui gère cette application. |
| Département | Centre de coût du groupe est un abonnement payant la consommation Azure. |
| EnvironmentType | **Production** (Même si l’abonnement comprend **Production** dans le nom, y compris cette balise permet de faciliter leur identification lorsque vous examinez les ressources dans le portail ou sur la facture.) |

### <a name="core-networks"></a>Réseaux de base

L’équipe de gestion Contoso ETS informations sécurité et les risques examine plan proposé de Dave pour déplacer l’application vers Azure. Qu’ils souhaitent pour vous assurer que l’application de carte de fidélité est correctement isolée et protégée dans un réseau DMZ.  Pour répondre à cette exigence, Dave et Alice créent un réseau virtuel externe et un groupe de sécurité réseau à isoler l’application de carte de fidélité à partir du réseau d’entreprise Contoso.  

Pour l' **abonnement de développement**, ils créent :

| Type de ressource | Nom | Description |
| ------------- | ---- | ----------- |
| Réseau virtuel | vnInternal | Gère l’environnement de développement de carte de fidélité Contoso et est connecté au réseau d’entreprise de Contoso via ExpressRoute. |

Pour l' **abonnement de production**, ils créent :

| Type de ressource | Nom | Description |
| ------------- | ---- | ----------- |
| Réseau virtuel | vnExternal | Héberge l’application de carte de fidélité et n’est pas connecté directement à ExpressRoute de Contoso. Code est envoyée directement aux services PaaS via leur système de Code Source. |
| Groupe de sécurité réseau | nsgBitBucket | Garantit que les attaques de cette charge de travail sont réduit en autorisant uniquement dans liées aux communications sur TCP 443.  Contoso est également une solution à l’aide d’un pare-feu d’Application Web pour accroître la protection. |  

### <a name="resource-locks"></a>Verrouillages des ressources 

Dave et Alice CONFERANT et décider d’ajouter les verrous des ressources sur certains des ressources clés dans l’environnement pour éviter toute suppression accidentelle pendant un push code errant. 

Ils créent le verrou suivant :

| Type de verrou | Ressource | Description |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | Pour interdire aux utilisateurs de supprimer le réseau virtuel ou sous-réseaux. Le verrou n’empêche pas l’ajout de nouveaux sous-réseaux. |

### <a name="azure-automation"></a>Automatisation Azure 

Alice et son équipe de développement ont des procédures opérationnelles pour gérer l’environnement de cette application. Les procédures opérationnelles permettant l’Ajout/Suppression de nœuds pour l’application et d’autres tâches DevOps. 

Pour utiliser ces procédures opérationnelles, ils activent [Automation](./automation/automation-intro.md).

### <a name="azure-security-center"></a>Centre de sécurité Azure 

Gestion des services informatiques de Contoso doit identifier rapidement et de gérer les menaces. Ils veulent également comprendre quels sont les problèmes éventuels.  

Pour répondre à ces exigences, Dave permet le centre de sécurité Azure. Il garantit que le centre de sécurité Azure surveille les ressources et offre un accès aux équipes DevOps et sécurité. 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles de gestionnaire de ressources, voir [meilleures pratiques pour la création de modèles Azure le Gestionnaire de ressources](resource-manager-template-best-practices.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribué à cette rubrique.*

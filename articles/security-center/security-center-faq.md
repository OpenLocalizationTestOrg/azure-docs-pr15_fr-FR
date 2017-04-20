<properties
   pageTitle="Centre de sécurité Azure Forum aux questions (FAQ) | Microsoft Azure"
   description="Ce forum aux questions répond aux questions sur le centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Centre de sécurité Azure Forum aux questions (FAQ)

Ce forum aux questions répond aux questions sur le centre de sécurité Azure, un service qui vous permet de prévenir, détecter et répondre aux menaces avec une meilleure visibilité et contrôler la sécurité de vos ressources Microsoft Azure.

## <a name="general-questions"></a>Questions d’ordre générales

### <a name="what-is-azure-security-center"></a>Quel est le centre de sécurité Azure ?
Centre de sécurité Azure vous permet d’empêcher, détecter et répondre aux menaces avec une meilleure visibilité et contrôler la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

### <a name="how-do-i-get-azure-security-center"></a>Comment obtenir le centre de sécurité Azure ?
Centre de sécurité Azure est compatible avec votre abonnement Microsoft Azure et accéder à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). ([Se connecter au portail](https://portal.azure.com), sélectionnez **Parcourir**et faites défiler vers **Le centre de sécurité**).  

## <a name="billing"></a>Facturation

### <a name="how-does-billing-work-for-azure-security-center"></a>Fonctionnement de facturation pour le centre de sécurité Azure
Centre de sécurité est proposé en deux versions : gratuites et Standard.

La couche libre permet de vous permettent de définir des stratégies de sécurité et de recevoir des alertes de sécurité, des incidents et des recommandations qui vous guident dans le processus de configuration des contrôles nécessaires. Avec la couche gratuite, vous pouvez également surveiller l’état de sécurité de vos ressources Azure et les solutions des partenaires intégrées à votre abonnement Azure.

La catégorie Standard offre la détection de fonctionnalités plus avancées couche libre : menaces intelligence comportement, analyse des incidents et l’analyse de détection des anomalies. Une évaluation gratuite de 90 jours de la couche Standard est disponible. Pour mettre à niveau, sélectionnez tarifs couche dans la [stratégie de sécurité](security-center-policies.md#setting-security-policies-for-subscriptions). Pour plus d’informations, voir [le centre de sécurité tarifs](security-center-pricing.md) .

## <a name="data-collection"></a>Collecte de données

Centre de sécurité collecte des données à partir de vos machines virtuelles afin d’évaluer leur état de sécurité, fournissent des recommandations de sécurité et signale les menaces. Lorsque vous accédez tout d’abord le centre de sécurité, collecte de données est activée sur tous les ordinateurs virtuels dans votre abonnement. Collecte de données est recommandée, mais vous pouvez annulations en [désactivant la collecte des données](#how-do-i-disable-data-collection) dans la stratégie de centre de sécurité.

### <a name="how-do-i-disable-data-collection"></a>Comment désactiver la collecte de données ?

Vous pouvez désactiver la **collecte de données** pour un abonnement dans la stratégie de sécurité à tout moment. ([Se connecter au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez **Le centre de sécurité**, puis sélectionnez **stratégie**.)  Lorsque vous sélectionnez un abonnement, une nouvelle carte s’ouvre et vous permet de désactiver la **collecte de données** . Sélectionnez l’option **Supprimer les agents** dans le ruban supérieur pour supprimer les agents des machines virtuelles existantes.

> [AZURE.NOTE] Stratégies de sécurité peuvent être définies au niveau de l’abonnement Azure et niveau de groupe de ressources, mais vous devez sélectionner un abonnement à désactiver la collecte de données.

### <a name="how-do-i-enable-data-collection"></a>Comment activer la collecte de données ?
Vous pouvez activer la collecte des données pour vos abonnements Azure dans la stratégie de sécurité. Pour activer la collecte de données, [se connecter au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez **Le centre de sécurité**, puis sélectionnez **stratégie**. **Collecte de données** **on** et configurer les comptes de stockage où vous souhaitez les données à collecter pour (voir question »[où sont stockées mes données ?](#where-is-my-data-stored)»). **Collecte de données** est activée, recueille automatiquement les informations de configuration et événements de sécurité à partir de toutes les machines virtuelles pris en charge dans l’abonnement.

> [AZURE.NOTE] Stratégies de sécurité peuvent être définies au niveau de l’abonnement Azure et niveau de groupe de ressources, mais la configuration de la collecte de données se produit au niveau de l’abonnement uniquement.

### <a name="what-happens-when-data-collection-is-enabled"></a>Que se passe-t-il lors de la collecte de données est activée ?
Collecte de données est activée via l’Agent de surveillance Azure et l’extension du contrôle de la sécurité Azure. L’extension de contrôle de la sécurité Azure analyse pour une configuration pertinents sécurité différents et envoie en traces [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). En outre, le système d’exploitation crée des entrées de journal des événements.  L’Agent de surveillance Azure lit les entrées de journal des événements et ETW trace et les copie à votre compte de stockage pour une analyse.  Il s’agit du compte de stockage que vous avez configuré dans la stratégie de sécurité. Pour plus d’informations sur le compte de stockage, voir question »[où sont stockées mes données ?](#where-is-my-data-stored)»

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>L’extension Agent de surveillance ou de contrôle de la sécurité influe sur les performances de mon serveur (s) ?
L’agent et extension consomme un montant nominal de ressources système et doit avoir peu d’impact sur les performances.

### <a name="where-is-my-data-stored"></a>Où sont stockées mes données ?
Pour chaque région dans lequel vous avez machines virtuelles en cours d’exécution, vous choisissez le compte de stockage où sont stockées les données collectées à partir de ces ordinateurs virtuels. Cela facilite la conserver les données dans la même zone géographique à des fins de souveraineté de données et de confidentialité. Vous choisissez le compte de stockage pour un abonnement dans la stratégie de sécurité. ([Se connecter au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez **Le centre de sécurité**, puis sélectionnez **stratégie**.) Lorsque vous cliquez sur un abonnement, une nouvelle carte s’ouvre. Sélectionnez **comptes de stockage choisir** pour sélectionner une région.

> [AZURE.NOTE] Stratégies de sécurité peuvent être définies au niveau de groupe ressource du niveau d’abonnement Azure et mais sélection d’une région pour votre compte de stockage se produit au niveau de l’abonnement uniquement.

Pour plus d’informations sur le stockage Azure et des comptes de stockage, voir [Documentation du stockage](https://azure.microsoft.com/documentation/services/storage/) et des [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>À l’aide du centre de sécurité Azure

### <a name="what-is-a-security-policy"></a>Qu’est une stratégie de sécurité ?
Une stratégie de sécurité définit le jeu de contrôles qui sont recommandés pour les ressources au sein de l’abonnement spécifié ou le groupe de ressources. Dans le centre de sécurité Azure, vous définissez des stratégies pour vos abonnements Azure et les groupes de ressources en fonction des besoins de sécurité de votre société et le type d’applications ou critère de diffusion des données dans chaque abonnement.

Par exemple, les ressources permettant de développement ou de test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications avec réglementé données telles que des informations d’identification personnelle (informations d’identification personnelle) peuvent nécessiter un niveau plus élevé de sécurité. Les stratégies de sécurité activées dans le centre de sécurité Azure déterminera recommandations de sécurité et de surveillance. Pour en savoir plus sur les stratégies de sécurité, voir [analyse dans le centre de sécurité Azure du fonctionnement de sécurité](security-center-monitoring.md).

> [AZURE.NOTE] En cas de conflit entre la stratégie de l’abonnement et stratégie de niveau de groupe de ressources, la stratégie de niveau de groupe de ressources est prioritaire.

### <a name="who-can-modify-a-security-policy"></a>Personnes autorisées à modifier une stratégie de sécurité ?
Stratégies de sécurité sont configurés pour chaque abonnement ou d’un groupe de ressources. Pour modifier une stratégie de sécurité au niveau de groupe de ressources du niveau d’abonnement ou, vous devez être un propriétaire ou un collaborateur de cet abonnement.

Pour savoir comment configurer une stratégie de sécurité, reportez-vous à [définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Qu’est une recommandation de sécurité ?
Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque les problèmes de sécurité potentielles sont identifiés, recommandations sont créées. Les recommandations vous guident dans le processus de configuration du contrôle nécessaire. Les exemples sont :

- Mise en service de contre les logiciels malveillants pour aider à identifier et supprimer des logiciels malveillants
- Configuration des [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) et des règles pour contrôler le trafic vers des machines virtuelles
- Mise en service d’un pare-feu d’application web à protéger contre les attaques de ciblage de vos applications web
- Déploiement mises à jour système manquants
- Définir les destinataires de configurations système d’exploitation qui ne correspondent pas les plannings de référence recommandés

Uniquement des recommandations qui sont activées dans les stratégies de sécurité sont présentées ici.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Comment puis-je afficher l’état actuel de la sécurité de mes ressources Azure ?
Une vignette **au niveau des ressources** sur la carte du **Centre de sécurité** indique la position de sécurité générale de votre environnement réparti par machines virtuelles, des applications web et d’autres ressources. Chaque ressource a un affichage indicateur si les problèmes de sécurité potentielles ont été identifiées. En cliquant sur la vignette d’intégrité de ressources affiche vos ressources et identifie l’endroit où l’attention est nécessaire ou problèmes peuvent exister.

### <a name="what-triggers-a-security-alert"></a>Ce qui déclenche une alerte de sécurité ?
Centre de sécurité Azure recueille, analyse automatiquement et fusionne les données du journal à partir de vos ressources Azure, le réseau et les solutions des partenaires tels que contre les logiciels malveillants et de pare-feu. Lorsque des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détection de :

- Machines virtuelles compromis communiquer avec des adresses IP malveillants connus
- Logiciels malveillants avancées détectés à l’aide de rapport d’erreurs Windows
- Attaques en force contre machines virtuelles
- Alertes de sécurité à partir de solutions de sécurité intégrée partenaire tels que les logiciels malveillants ou les pare-feu de Application Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Quelle est la différence entre les menaces détectées et averti sur par Microsoft Security Response Center et le centre de sécurité Azure ?
Le centre de réponse sécurité Microsoft (MSRC) effectue une analyse sélectionnez sécurité de l’infrastructure et Azure réseau et reçoit plaintes intelligence et abuse menace provenant de tiers. Lorsque MSRC se transforme en prenant en charge que les données client a été utilisées par une partie illégale ou non autorisée ou que la l’utilisation des client Azure ne respecte pas avec les termes pour utiliser Acceptable, un gestionnaire des incidents de sécurité vous avertit le client. Notification survient généralement en envoyant un courrier électronique aux contacts de sécurité spécifiés dans le centre de sécurité Azure ou le propriétaire de l’abonnement Azure si un contact de sécurité n’est pas spécifié.

Centre de sécurité est un service Azure qui surveille l’environnement du client Azure en permanence et s’applique analytique pour détecter automatiquement un large éventail d’activité potentiellement malveillant. Ces éléments détectés sont exposées sous forme d’alertes de sécurité dans le tableau de bord centre de sécurité.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>La gestion des autorisations dans le centre de sécurité Azure ?
Centre de sécurité Azure prend en charge l’accès basé sur un rôle. Pour en savoir plus sur le contrôle d’accès basé sur un rôle (RBAC) dans Azure, consultez [Contrôle d’accès basé sur Azure Active Directory rôle](../active-directory/role-based-access-control-configure.md).

Lorsqu’un utilisateur s’ouvre centre de sécurité, uniquement des recommandations et alertes liées à l’utilisateur a accès à des ressources seront visible. Cela signifie que les utilisateurs verront uniquement les éléments associés aux ressources où l’utilisateur est le rôle de propriétaire, collaborateur ou lecteur à l’abonnement ou le groupe de ressources appartenant à une ressource.

Si vous avez besoin :

- **Modifier une stratégie de sécurité**, vous devez être un propriétaire ou un collaborateur de l’abonnement.
- **Appliquer une recommandation**, vous devez être un propriétaire ou un collaborateur de l’abonnement.
- **Avoir une visibilité sur l’état de sécurité dans l’ensemble de vos abonnements**, vous devez être un propriétaire, un collaborateur ou un lecteur (administrateur informatique, équipe de sécurité) de chaque abonnement.
- **Avoir une visibilité sur l’état de sécurité de vos ressources**, vous devez être un groupe de ressources propriétaire, collaborateur ou lecteur (DevOps).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Les ressources Azure sont analysés par le centre de sécurité Azure ?
Centre de sécurité Azure surveille les ressources Azure suivantes :

- Machines virtuelles (machines virtuelles) (y compris les [Services Cloud](../cloud-services/cloud-services-choose-me.md))
- Réseaux virtuels Azure
- Service SQL Azure
- Solutions des partenaires intégrées à votre abonnement Azure tel qu’un pare-feu d’application web sur machines virtuelles et [Environnement de Service d’application](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Machines virtuelles

### <a name="what-types-of-virtual-machines-will-be-supported"></a>Quels types de machines virtuelles seront prises en charge ?
Contrôle au niveau de la sécurité et de recommandations sont disponibles pour les machines virtuelles (machines virtuelles) créés à l’aide à la fois [classique et modèles de déploiement Gestionnaire de ressources](../azure-classic-rm.md).

Machines virtuelles Windows pris en charge :

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Machines virtuelles Linux prises en charge :

- Versions Ubuntu 12.04, 14.04, 16.04
- Debian versions 7, 8
- CentOS versions 6. \*, 7.*
- Versions rouge chapeau Enterprise Linux (RHEL) 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versions 11. \*, 12.*
- Versions de Linux Oracle 6. \*, 7.*

Machines virtuelles en cours d’exécution dans un service cloud sont également pris en charge. Rôles web et de travail en cours d’exécution en production emplacements sont analysés des services cloud uniquement. Pour en savoir plus sur le service cloud, voir [vue d’ensemble des Services Cloud](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Pourquoi le centre de sécurité Azure ne reconnaît pas la solution contre les logiciels malveillants en cours d’exécution sur mon ordinateur virtuel Azure ?

Centre de sécurité Azure contient uniquement visibilité contre les logiciels malveillants installés par l’intermédiaire des extensions Azure. Par exemple, centre de sécurité n’est pas en mesure de détecter contre les logiciels malveillants qui était déjà installé sur une image que vous avez fourni ou si vous avez installé contre les logiciels malveillants sur vos machines virtuelles à l’aide de votre propre processus (par exemple, des systèmes de gestion de configuration).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Je reçois le message « Manquants des données d’analyse » pour mon machine virtuelle ?

Il peut prendre un certain temps (généralement moins d’une heure) pour les données d’analyse remplir une fois la collecte de données est activée dans le centre de sécurité Azure. Analyses remplissent pas pour les machines virtuelles dans un état arrêté.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Je reçois le message « Agent machine virtuelle est manquant ? »

L’Agent de machine virtuelle doit être installé sur machines virtuelles afin de permettre la collecte de données. L’Agent de machine virtuelle est installé par défaut pour les machines virtuelles qui sont déployés à partir de la Azure Marketplace. Pour plus d’informations sur l’installation de l’Agent de machine virtuelle sur d’autres ordinateurs virtuels, voir le billet de blog [Agent machine virtuelle et Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).

<properties
   pageTitle="Meilleures pratiques pour les entreprises déplacement vers Azure | Microsoft Azure"
   description="Décrit une structure entreprises peuvent utiliser pour garantir un environnement sécurisé et facile à gérer."
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

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Structure de l’entreprise Azure - gouvernance des abonnement

Les entreprises adoptent plus en plus du cloud public pour sa souplesse et la flexibilité. Ils utilisent résistance du cloud pour générer des revenus ou optimiser les ressources pour les entreprises. Microsoft Azure fournit une multitude de services que les entreprises peuvent assembler comme des blocs de construction pour résoudre une vaste gamme de charges de travail et les applications. 

Mais, savoir où commencer est souvent difficile. Après avoir décidé d’utiliser Azure, quelques questions fréquemment en cas de problème :

- « Comment respecter nos obligations légales de souveraineté de données dans certains pays ? »
- « Comment puis-je assurer qu’une personne ne change pas par inadvertance un système critique ? »
- « Comment savoir ce que toutes les ressources sont prises en charge afin que j’ai puis-je du compte et facture réarchiver précisément ? »

La perspective d’un abonnement avec aucune rails assure votre protection vide est une tâche ardue. Cet espace vide peut en effet diminuer le déplacement vers Azure.

Cet article fournit un point de départ destiné aux techniciens répondent aux besoins de gouvernance et équilibrer avec le besoin en souplesse. Il présente le concept d’une structure d’entreprise qui guide les organisations dans implémentation et gestion de leurs abonnements Azure. 

## <a name="need-for-governance"></a>Vous avez besoin de gouvernance

Lors du déplacement vers Azure, vous devez résoudre le sujet de la gouvernance au plus tôt pour garantir l’application réussie du cloud dans l’entreprise. Malheureusement, l’heure et la bureaucratie de création d’un système de gouvernance complète signifie certaines divisions accéder directement au fournisseurs sans impliquer informatiques de l’entreprise. Cette approche, laissez l’entreprise ouvrir problèmes les ressources ne sont pas gérés correctement. Les caractéristiques du cloud public - souplesse, flexibilité et tarification par consommation - sont importants pour les groupes d’entreprise qui doivent pouvoir rapidement répondre aux exigences de clients (internes et externes). Mais, informatiques de l’entreprise doit s’assurer que les données et les systèmes sont efficacement protégés.

Dans la réalité, la structure est utilisée pour créer la base de la structure. La structure guides le plan général et fournit les points d’ancrage pour les systèmes plus permanents à monter. Une structure d’entreprise est le même : un ensemble de contrôles flexibles et fonctionnalités Azure fournissent une structure à l’environnement et les points d’ancrage pour les services basés sur le nuage public. Il fournit les générateurs (informatique et de groupes) de base pour créer et joindre des nouveaux services.

La structure est basé sur les pratiques que nous avons collectées à partir de nombreux contrats avec des clients de tailles diverses. Plage de ces clients de petites organisations au développement de solutions dans le cloud pour les entreprises Fortune 500 et éditeurs de logiciels chargés de migration et de développement de solutions dans le cloud. La structure d’entreprise est « spécialisé » pour être flexible pour prendre en charge les charges de travail informatiques traditionnels et les charges de travail agiles ; par exemple, les développeurs de créer des applications de logiciel-as-a-service (SaaS) selon les fonctionnalités Azure.

La structure d’entreprise est destiné à être la structure de chaque nouvel abonnement dans Azure. Il permet aux administrateurs d’assurer les charges de travail respecter la gouvernance minimale d’une organisation sans empêchant des groupes et les développeurs de rapidement les objectifs de leur propre.

> [AZURE.IMPORTANT] La gouvernance est cruciale pour le succès de Azure. Cet article cibles l’implémentation technique d’une structure de l’entreprise, mais ne concerne les processus et les relations entre les composants plus large. Gouvernance de stratégie flux à partir du haut vers le bas et est déterminé par ce que l’entreprise souhaite atteindre. Bien entendu, la création d’un modèle de gouvernance pour Azure comprend des représentants de service informatique, mais surtout il doit avoir représentation forte à partir de groupe leaders et gestion des risques et de sécurité. Mais au final, une structure de l’entreprise consiste à réduire les risques d’entreprise pour faciliter la mission et les objectifs d’une organisation.

L’image suivante décrit les composants de la structure. La structure repose sur une planification unie pour les départements, les comptes et abonnements. Les piliers consistant de stratégies du Gestionnaire de ressources et normes d’appellation forts. Le reste de structure core proviennent des fonctionnalités Azure et aux fonctionnalités qui permettent à un environnement sécurisé et facile à gérer.

![composants de base](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure s’est développée rapidement depuis son lancement en 2008. Cette croissance obligatoire équipes d’ingénieurs Microsoft à concevoir de nouveau leur approche permettant de gérer et déployer des services. Le modèle de gestionnaire de ressources Azure a été introduit dans 2014 et remplace le modèle de déploiement classique. Gestionnaire de ressources permet aux organisations de déployer plus facilement, organiser et contrôler les ressources Azure. Gestionnaire de ressources comprend parallélisation lors de la création des ressources pour accélérer le déploiement des solutions complexes, interdépendants. Il inclut également le contrôle d’accès précis et la possibilité de ressources de balise de métadonnées. Microsoft recommande de créer toutes les ressources via le modèle de gestionnaire de ressources. La structure d’entreprise est explicitement conçu pour le modèle de gestionnaire de ressources.

## <a name="define-your-hierarchy"></a>Définir votre hiérarchie

La structure de la structure repose sur l’inscription d’entreprise Azure (et le portail d’entreprise). L’inscription de l’entreprise définit la forme et utiliser des services Azure dans une société et est la structure de gouvernance principale. Dans l’accord entreprise, les clients sont en mesure de diviser plus encore l’environnement dans les services, les comptes et enfin, abonnements. Un abonnement Azure est l’unité de base où toutes les ressources sont contenues. Il définit également plusieurs limites dans Azure, telles que nombre de cœurs, les ressources, etc..

![hiérarchie](./media/resource-manager-subscription-governance/agreement.png)

Chaque entreprise est différente et permet de la hiérarchie dans l’image précédente pour une grande flexibilité dans l’organisation de Azure au sein de la société. Avant d’implémenter les directives contenues dans ce document, vous devez modéliser votre hiérarchie et comprendre l’impact sur la facturation, accès aux ressources et la complexité.

Les trois modèles courants pour Azure inscriptions sont les suivantes :

- Le modèle **fonctionnel**

    ![fonctionnel](./media/resource-manager-subscription-governance/functional.png)

- Le modèle de **division** 

    ![entreprise](./media/resource-manager-subscription-governance/business.png)

- Le modèle **géographique**

    ![géographique](./media/resource-manager-subscription-governance/geographic.png)

Vous appliquez la structure au niveau de l’abonnement pour étendre la gouvernance des besoins de l’entreprise dans l’abonnement.

## <a name="naming-standards"></a>Conventions d’appellation standard

La première piliers de la structure est conventions d’appellation standard. Normes de noms de bien conçues permettent d’identifier les ressources dans le portail, sur une facture et au sein de scripts. Le plus souvent, vous avez déjà conventions d’appellation standard pour une infrastructure sur site. Lorsque vous ajoutez Azure à votre environnement, vous devez étendre ces conventions à vos ressources Azure. Norme faciliter la gestion des plus efficace de l’environnement à tous les niveaux.

> [AZURE.TIP]
>
> - Passez en revue et adopter où possible les [instructions de modèles et pratiques](./guidance/guidance-naming-conventions.md). Ce guide vous aide à choisir une norme d’appellation significative.
> - Utilisez la casse mixte pour les noms des ressources (par exemple, myResourceGroup et vnetNetworkName). Remarque : Il existe certaines ressources, tels que les comptes de stockage, où la seule option consiste à utiliser des minuscules (et aucun autre caractère spécial).
> - Envisagez d’utiliser le Gestionnaire de ressources Azure stratégies (décrites dans la section suivante) d’appliquer des normes d’attribution de noms.
 
## <a name="policies-and-auditing"></a>Stratégies et audit

Le deuxième fondement de la structure consiste à créer des [stratégies de gestionnaire de ressources Azure](resource-manager-policy.md) et [audit du journal d’activité](resource-group-audit.md). Gestionnaire de ressources stratégies vous fournissent la possibilité de gérer le risque dans Azure. Vous pouvez définir des stratégies garantissant souveraineté de données en limitant, appliquer ou audit certaines actions. 

- La stratégie est un système **d’Autoriser** par défaut. Vous contrôlez les actions en définissant et attribution de stratégies vers des ressources qui refuser ou d’audit des actions sur les ressources.
- Les stratégies sont décrites par les définitions de stratégie dans une langue de définition de stratégie (si-alors conditions).
- Vous créez des stratégies avec JSON (Javascript Object Notation) fichiers mis en forme. Après avoir défini une stratégie, vous attribuez à une zone en particulier : abonnement, groupe de ressources. ou une ressource.

Stratégies d’ont plusieurs actions qui permettent d’une approche permissions vos scénarios. Les actions sont :

-   **Refuser**: bloque la demande de ressources
-   **Audit**: permet de la demande, mais inclure une ligne dans le journal d’activité (qui peut être utilisé pour fournir des alertes ou déclencher procédures opérationnelles)
-   **Ajout**: ajoute les informations spécifiées pour la ressource. Par exemple, si une balise « Centre de coût » n'est pas sur une ressource, ajoutez cette balise avec une valeur par défaut.

### <a name="common-uses-of-resource-manager-policies"></a>Utilisations courantes de stratégies du Gestionnaire de ressources

Azure stratégies du Gestionnaire de ressources sont un outil puissant dans la boîte à outils Azure. Ils permettent d’éviter des coûts inattendus, afin d’identifier un centre de coûts des ressources par le biais de marquage et pour vous assurer que les exigences de conformité sont remplies. Lorsque des stratégies sont combinés avec les fonctionnalités d’audit intégrées, vous pouvez mode solutions complexes et flexibles. Les stratégies de sociétés fournir des contrôles pour les charges de travail « Informatique traditionnelle » et « Agile » les charges de travail ; par exemple, développement d’applications client. Les modèles courants que pour les stratégies, nous voyons sont les suivantes :

- **Geo-conformité/données souveraineté** - Azure fournit des régions du monde entier. Les entreprises souhaitent souvent contrôler l’endroit où les ressources sont créées (si vous souhaitez vous assurer souveraineté de données ou pour garantir que les ressources sont créées à proximité les consommateurs fin des ressources).
- **Gestion des coûts** - abonnement un Azure peut contenir des ressources de nombreux types et d’échelle. Les sociétés souhaitent souvent pour vous assurer que les abonnements standards éviter l’utilisation de ressources inutilement volumineux, ce qui peuvent coûter des centaines de dollars un mois ou plus.
- **Par défaut gouvernance via des balises requis** - nécessitant des balises est l’une des fonctionnalités hautement souhaitées et les plus courantes. Les entreprises à l’aide de stratégies de gestionnaire de ressources Azure sont en mesure d’assurer qu’une ressource est correctement balisée. Les balises plus courants sont : type de département, le propriétaire de la ressource et environnement (par exemple - production, test, développement)

**Exemples**

« Traditionnel informatique « abonnement pour les applications de métier

-   Appliquer des balises de service et de propriétaire sur toutes les ressources
-   Restreindre la création de ressources à la région Amérique du Nord
-   Limiter la capacité à créer des machines virtuelles série G et Clusters HDInsight

Environnement de « Agile » d’une division création d’applications cloud

- Pour répondre aux exigences de souveraineté de données, autoriser la création de ressources uniquement dans une zone spécifique.
- Appliquer la balise environnement sur toutes les ressources. Si une ressource est créée sans une balise, ajoutez la **environnement : inconnue** balise pour la ressource.
- Lorsque les ressources sont créées en dehors de l’Amérique du Nord, mais ne pas empêchent l’audit.
- Auditer les ressources de type coût élevé sont créées.

> [AZURE.TIP]
>
> Le plus souvent utilisé de stratégies du Gestionnaire de ressources dans des organisations consiste à contrôle *où* les ressources sont créées et *les* types de ressources peuvent être créées. En plus des contrôles sur *l’emplacement* et *ce à quoi*, de nombreuses entreprises utilisent des stratégies pour s’assurer que ressources les métadonnées appropriées pour facturer revenir à la consommation. Nous vous recommandons d’appliquer des stratégies au niveau de l’abonnement pour :
>
> - Geo-conformité/données souveraineté
> - Gestion des coûts
> - Balises requises (déterminées par les besoins professionnels, tels que de facturation, propriétaire de l’Application)
>
> Vous pouvez appliquer des stratégies supplémentaires aux niveaux inférieurs d’étendue.
 
### <a name="audit---what-happened"></a>Audit - passée ?

Pour afficher le fonctionne de votre environnement, vous devez procéder à un audit de l’activité des utilisateurs. La plupart des types de ressources dans Azure créent les journaux de diagnostic que vous pouvez analyser via un outil de journal ou dans Azure opérations Management Suite. Vous pouvez collecter les journaux d’activité sur plusieurs abonnements à fournir un par département ou vue d’entreprise. Les enregistrements d’audit sont un outil de diagnostic importantes et mécanisme crucial pour déclencher des événements dans l’environnement Azure.

Journaux d’activité à partir du Gestionnaire de ressources déploiements activer vous permet de déterminer les **opérations** ayant Placez et qui a effectué chacune d’elles. Journaux d’activité peuvent être collectées et agrégées à l’aide d’outils tels que journal Analytique.

## <a name="resource-tags"></a>Balises de ressources

Lorsque les utilisateurs de votre organisation ajoutent des ressources à l’abonnement, il se transforme en croissants associer des ressources avec le service approprié, le client et l’environnement. Vous pouvez joindre des métadonnées aux ressources via des [balises](resource-group-using-tags.md). Balises vous permet de fournir des informations sur la ressource ou le propriétaire. Les balises vous permettent non seulement agréger et regrouper des ressources de diverses façons, mais utilisez ces données pour les besoins de refacturation. Vous pouvez ajouter des balises ressources présentant jusqu'à 15 paires clé : valeur. 

Les balises de ressources sont flexibles et doivent être associés à la plupart des ressources. Exemples de balises de ressources courantes sont :

- Facturation
- Service (ou la division)
- Environnement (Production, étape, développement)
- Niveau (niveau Web, couche Application)
- Propriétaire de l’application
- NomProjet

![balises](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Pour plus d’exemples d’indicateurs, voir [recommandé conventions d’appellation des ressources Azure](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Créer une stratégie de liaison qui identifie vos abonnements les métadonnées qui sont nécessaire pour l’entreprise, finances, sécurité, gestion des risques et la gestion globale de l’environnement. Envisagez d’effectuer une stratégie qui impose marquage pour :
>
> - Groupes de ressources
> - Espace de stockage
> - Machines virtuelles
> - Serveurs web/environnements de Service d’application

## <a name="resource-group"></a>Groupe de ressources 

Gestionnaire de ressources permet de placer des ressources dans des groupes significatifs pour affinité de facturation ou Natural Keyboard gestion. Comme mentionné précédemment, Azure comporte deux modèles de déploiement. Dans le modèle classique antérieur, l’unité de base de la gestion d’était l’abonnement. Il a été difficile à décomposer les ressources au sein d’un abonnement, qui conduit à la création d’un grand nombre d’abonnements. Avec le modèle de gestionnaire de ressources, nous l’avons vu l’introduction des groupes de ressources. Groupes de ressources sont des conteneurs de ressources qui ont un cycle de vie courantes ou partagent un attribut telles que « tous les serveurs SQL » ou « Application A ».

Groupes de ressources ne peut pas être contenues dans l’autre et ressources peuvent appartenir qu’à un groupe de ressources. Vous pouvez appliquer certaines actions sur toutes les ressources dans un groupe de ressources. Par exemple, la suppression d’un groupe de ressources supprime toutes les ressources dans le groupe de ressources. En règle générale, vous placez un ensemble de l’application ou un système associés dans le même groupe de ressources. Par exemple, une application à trois niveaux appelée Application Web Contoso contient le serveur web, de serveur d’applications et de SQL server dans le même groupe de ressources.

> [AZURE.TIP]
>
> La façon dont vous organisez vos groupes de ressources peut varier de charges de travail « Informatique traditionnelle » à charges de travail « Informatique Agile »
>
> - « Traditionnel informatique « charges de travail sont généralement regroupés par éléments au sein du même cycle de vie, par exemple une application. Regroupement par application permet de gestion des applications individuels.
> - « Agile informatique « charges de travail tendent à se concentrer sur les applications cloud des clients externes. Les groupes de ressources doivent refléter les couches de déploiement (par exemple, couche Web, couche application) et la gestion.

## <a name="role-based-access-control"></a>Contrôle d’accès basé sur un rôle

Vous êtes vous demandez probablement « qui doivent y accéder aux ressources ? » et « comment contrôler cet accès ? » Autoriser ou interdiction de l’accès au portail Azure et contrôler l’accès aux ressources dans le portail sont crucial. 

Lorsque Azure initialement publiée, des contrôles d’accès à un abonnement ont été base : administrateur ou administrateur de co-création. Accéder à un abonnement dans le niveau d’accès modèle impliqué classique à toutes les ressources dans le portail. Ce manque de contrôle permissions conduit à la prolifération des abonnements pour fournir un niveau de contrôle d’accès raisonnablement pour une inscription Azure.

Cette prolifération des abonnements n’est plus nécessaire. Avec le contrôle d’accès basé sur un rôle, vous pouvez affecter des utilisateurs aux rôles standards (par exemple, « lecteur » et « writer » courants rôles). Vous pouvez également définir des rôles personnalisés.

> [AZURE.TIP]
>  
> - Se connecter à votre banque d’identités d’entreprise (généralement Active Directory) à Azure Active Directory à l’aide de l’outil AD Connect.
> - Contrôle de l’administrateur/Co-création-administrateur d’un abonnement à l’aide d’une identité gérée. **** N’affecter d’administration/co-création-admin à un nouveau propriétaire de l’abonnement. À la place, utilisez rôles RBAC pour fournir les droits de **propriétaire** à un groupe ou individuels.
> - Ajouter des utilisateurs Azure à un groupe (par exemple, les propriétaires de X Application) dans Active Directory. Utiliser le groupe synchronisé pour fournir des membres du groupe les droits appropriés pour gérer le groupe de ressources qui contient l’application.
> - Suivez le principe de l’octroi du **moindre privilège** requis pour faire le travail prévu. Par exemple :
>     - Groupe de déploiement : Un groupe qui n’est pas en mesure de déployer des ressources.
>     - : Machine virtuelle un groupe d’administration Qui est en mesure de redémarrer machines virtuelles (pour les opérations)

## <a name="azure-resource-locks"></a>Verrouillages des ressources Azure

À mesure que votre organisation ajoute les services de base à l’abonnement, il se transforme en plus important de s’assurer que ces services sont disponibles pour éviter d’interruption de service. [Verrouillages des ressources](resource-group-lock-resources.md) permettent de limiter les opérations sur les ressources de grande valeur où suppression ou leur modification aurait un impact sur vos applications ou l’infrastructure cloud. Vous pouvez appliquer des verrous à un abonnement, le groupe de ressources ou la ressource. En règle générale, vous appliquez verrous aux notions de base ressources telles que les réseaux virtuels, des passerelles et des comptes de stockage. 

Verrouillages des ressources pris en charge deux valeurs : CanNotDelete et en lecture seule. CanNotDelete signifie que les utilisateurs (avec les droits appropriés) peuvent toutefois lire ou modifier une ressource, mais ne peut pas le supprimer. En lecture seule signifie que les utilisateurs autorisés ne peut pas supprimer ou modifier une ressource.

Pour créer ou supprimer les verrous gestion, vous devez avoir accès à `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` actions.
Les rôles intégrés uniquement propriétaire et l’administrateur de l’accès utilisateur sont accordées ces actions.

> [AZURE.TIP] Options de réseau Core doivent être protégées avec les verrous. Suppression accidentelle d’une passerelle, VPN de site à serait désastreux à un abonnement Azure. Azure ne vous permet de supprimer un réseau virtuel qui est en cours d’utilisation, mais l’application de restrictions plus est une mesure utile. Les stratégies sont également cruciales pour la gestion des contrôles appropriés. Nous vous conseillons d’appliquer un verrou **CanNotDelete** aux stratégies qui sont en cours d’utilisation.
>
> - Réseau virtuel : CanNotDelete
> - Groupe de sécurité réseau : CanNotDelete
> - Stratégies : CanNotDelete

## <a name="core-networking-resources"></a>Ressources de mise en réseau fondamentales

Accès aux ressources peut être interne (au sein du réseau de l’entreprise) ou externe (via internet). Il est facile pour les utilisateurs de votre organisation placer par inadvertance ressources l’emplacement incorrect et les ouvrir potentiellement malveillants accès. Comme avec des appareils locales, les entreprises doivent ajouter des contrôles appropriés pour vous assurer que les utilisateurs Azure prendre les bonnes décisions. Pour la gouvernance de l’abonnement, nous allons identifier ressources principales qui permettent de contrôler base d’access. Les ressources core comprennent :

- **Réseaux virtuels** sont des objets de conteneur pour sous-réseaux. Bien que pas indispensable, il est souvent utilisé lors de la connexion des applications aux ressources d’entreprise internes.
- **Groupes de sécurité réseau** sont similaires à un pare-feu et fournir des règles de façon dont une ressource peut « parler » sur le réseau. Ils permettent de granulaire contrôler comment / si un sous-réseau (ou machine virtuelle) peut se connecter à Internet ou à d’autres sous-réseaux dans le même réseau virtuel.

![réseau de base](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Créer des réseaux virtuels dédiés à usage interne charges de travail et les charges de travail externe. Cette approche réduit le risque de par inadvertance plaçant machines virtuelles sont destinés internes charges de travail dans un espace en vis-à-vis externes.
> - Groupes de sécurité réseau sont essentiels pour cette configuration. Au minimum, bloquer l’accès à internet à partir de réseaux virtuels internes et bloquer l’accès au réseau d’entreprise à partir de réseaux externes de virtuels.

### <a name="automation"></a>Automatisation

Gestion des ressources individuellement sont à la fois du temps et potentiellement risquée pour certaines opérations. Azure fournit diverses fonctionnalités automation Azure automatisation, applications logique et fonctions Azure. [Automatisation Azure](./automation/automation-intro.md) permet aux administrateurs de créer et définir des procédures opérationnelles pour gérer les tâches courantes dans la gestion des ressources. Créer des procédures opérationnelles à l’aide d’un éditeur de code PowerShell ou un éditeur graphique. Vous pouvez créer des flux de travail multi-phase complexes. Azure Automation est souvent utilisée pour gérer les tâches courantes telles que l’arrêt de ressources inutilisées ou en créant des ressources en réponse à un déclencheur spécifique sans avoir besoin d’intervention humaine.

> [AZURE.TIP]
>
> - Créer un compte Azure Automation et passez en revue les disponible procédures opérationnelles (ligne graphique et commande) disponibles dans la [Galerie de Runbook](./automation/automation-runbook-gallery.md).
> - Importer et personnaliser des procédures opérationnelles clés pour votre utilisation personnelle.
>
> Un scénario courant est la possibilité de début/arrêt des machines virtuelles sur un planning. Il existe exemple procédures opérationnelles qui sont disponibles dans la galerie à la fois gérer ce scénario et apprendre pour le développer.


## <a name="azure-security-center"></a>Centre de sécurité Azure

Parmi les bloqueurs de fenêtres publicitaires plus grands vers le cloud adoption ont été peut-être les problèmes de sécurité. Responsables de risque informatique et les départements de sécurité doivent pour vous assurer que les ressources dans Azure sont sécurisés. 

Le [Centre de sécurité Azure](./security-center/security-center-intro.md) offre une vue centralisée de l’état de sécurité des ressources dans les abonnements et fournit des recommandations permettant d’empêcher les ressources compromis. Il peut activer les stratégies plus granulaires (par exemple, application des stratégies aux groupes de ressources spécifiques qui permettent de l’entreprise d’adapter leur position au risque ils résolvent). Enfin, le centre de sécurité Azure est une plateforme ouverte qui permet aux partenaires Microsoft et éditeurs de logiciels de créer un logiciel qui se branche sur Centre de sécurité Azure pour améliorer ses fonctionnalités. 

> [AZURE.TIP]
>
> Centre de sécurité Azure est activé par défaut dans chaque abonnement. Toutefois, vous devez activer la collecte de données des machines virtuelles pour permettre le centre de sécurité Azure installer son agent et commencer la collecte de données.
>
> ![collecte de données](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous avez appris à gouvernance de l’abonnement, il est temps pour afficher ces recommandations en pratique. Voir des [exemples d’implémentation de gouvernance abonnement Azure](resource-manager-subscription-examples.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribué à cette rubrique.*

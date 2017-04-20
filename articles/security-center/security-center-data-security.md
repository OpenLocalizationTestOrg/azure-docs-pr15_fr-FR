<properties
   pageTitle="Sécurité des données Azure le centre de sécurité | Microsoft Azure"
   description="Ce document explique comment les données sont gérées et sauvegardées dans le centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Sécurité des données Azure le centre de sécurité
Pour aider les clients prévenir, détecter et répondre aux menaces, centre de sécurité Azure recueille et traite les données relatives à vos ressources Azure, y compris les informations de configuration, les métadonnées, les journaux d’événements, blocage des fichiers de vidage et bien plus encore. Nous rendre engagements forts pour protéger la confidentialité et la sécurité de ces données. Microsoft est conforme aux directives de sécurité et conformité strictes — depuis le codage à un service. 

Cet article explique comment les données sont gérées et sauvegardées dans le centre de sécurité Azure.

## <a name="data-sources"></a>Sources de données

Centre de sécurité Azure analyse les données dans les sources suivantes :

- Services Azure : Lit les informations sur la configuration des services Azure que vous avez déployé à communiquer avec le fournisseur de ressources du service.
- Le trafic réseau : Lectures échantillonnées réseau métadonnées le trafic à partir de l’infrastructure de Microsoft, tels que IP/port, taille des paquets source/destination et protocole réseau.
- Solutions des partenaires : Collecte des alertes de sécurité à partir de solutions partenaires intégrées, telles que les pare-feu et les solutions contre les logiciels malveillants. Ces données sont stockées dans le stockage Azure le centre de sécurité, que qui se trouve actuellement aux États-Unis.
- Vos Machines virtuelles : Centre de sécurité Azure peut recueillir les informations de configuration et des informations sur les événements de sécurité, tels que des événements Windows et d’audit journaux, les journaux IIS, messages journal système et blocage des fichiers de vidage à partir de vos machines virtuelles à l’aide d’agents de collecte de données. Consultez la section « Collecte de données de gestion » ci-dessous pour plus d’informations.  

En outre, informations sur les alertes de sécurité, recommandations et état d’intégrité de sécurité sont stockées dans le stockage Azure le centre de sécurité, que qui se trouve actuellement aux États-Unis. Ces informations peuvent inclure des informations de configuration associés et les événements de sécurité collectées à partir de vos machines virtuelles selon vos besoins afin d’améliorer l’alerte de sécurité, recommandation ou état d’intégrité de sécurité.

## <a name="data-protection"></a>Protection des données

**Répartition des données**: les données sont conservées logiquement séparées sur chaque composant dans l’ensemble du service. Toutes les données sont référencées par l’organisation. Ce marquage persiste tout au long du cycle de vie des données, et elle est appliquée à chaque calque du service. En outre, les données collectées à partir de vos machines virtuelles sont stockées dans vos comptes de stockage.

**Accès aux données**: afin de fournir des recommandations de sécurité et recherchez des menaces de sécurité potentielles, personnel de Microsoft peut-être accéder aux informations collectées ou analysées par services Azure, notamment de blocage des fichiers de vidage. Blocage des fichiers de vidage et événements de création de processus peuvent inclure involontairement des données client ou les données personnelles à partir de vos machines virtuelles. Nous respecter les [Termes du contrat Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), état dans lequel que Microsoft ne doit pas utiliser les données client ou obtenir les informations à partir de celui-ci à des fins commerciales publicités ou similaires. Nous utilisons uniquement les données client de selon vos besoins pour vous fournir des services Azure, y compris à des fins compatibles avec assurant ces services. Conservation de tous les droits sur les données client.

**Utilisation des données**: Microsoft utilise des modèles et menaces vu entre plusieurs clients afin d’améliorer nos capacités de détection et de prévention ; nous le faire conformément aux engagements confidentialité décrites dans notre [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Emplacement des données**: un compte de stockage est spécifié pour chaque région où machines virtuelles en cours d’exécution. Ainsi, vous pouvez stocker les données dans la même région que la machine virtuelle à partir de laquelle les données sont collectées. Ces données, y compris les fichiers de vidage de blocage, seront stockées de façon permanente dans votre compte de stockage. Le service stocke également des informations sur les alertes de sécurité, notamment les alertes de solutions des partenaires intégrée, recommandations et l’état de sécurité dans le stockage Azure le centre de sécurité, que qui se trouve actuellement aux États-Unis.

## <a name="managing-data-collection-from-virtual-machines"></a>Gestion de la collecte des données à partir de machines virtuelles

Lorsque vous choisissez d’activer le centre de sécurité Azure, collecte de données est activée pour chacun de vos abonnements. Vous pouvez désactiver la collecte de données dans la section « Stratégie de sécurité » de votre tableau de bord centre de sécurité Azure. Lors de la collecte de données est activée, centre de sécurité Azure dispositions l’Agent de surveillance Azure sur tout prises en charge machines virtuelles et nouveaux créés. Analyse de l’extension de contrôle de la sécurité Azure pour différents liés à la sécurité des configurations et des événements en [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) effectue le suivi. En outre, le système d’exploitation déclenche événements du journal au cours de l’exécution de l’ordinateur. Voici quelques exemples de ce type de données : type de système d’exploitation et la version, d’exploitation journaux système (journaux des événements Windows), en cours d’exécution des processus, nom de l’ordinateur, adresses IP, connecté utilisateur et l’ID de client. L’Agent de surveillance Azure lit les entrées de journal des événements et ETW trace et les copie à votre compte de stockage pour une analyse. 

Un compte de stockage est spécifié pour chaque région dans lequel vous avez machines virtuelles en cours d’exécution, où les données collectées à partir d’ordinateurs virtuels dans ce même région est stockée. Cela facilite la conserver les données dans la même zone géographique à des fins de souveraineté de données et de confidentialité. Vous pouvez configurer des comptes de stockage pour chaque région dans la section « Stratégie de sécurité » de votre tableau de bord centre de sécurité Azure.

L’Agent de surveillance Azure copie également blocage des fichiers de vidage à votre compte de stockage.  Centre de sécurité Azure éphémères copies de vos fichiers de vidage de blocage de collecte et les analyse pour preuve de tentatives d’attaque et compromis réussies.  Centre de sécurité Azure effectue cette analyse au sein de la même région géographique en tant que le compte de stockage et supprime les copies éphémères lors de l’analyse est terminée.

Vous pouvez désactiver la collecte de données des machines virtuelles à tout moment, ce qui entraîne la suppression les Agents surveillance précédemment installés par le centre de sécurité Azure.


## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment les données sont gérées et sauvegardées dans le centre de sécurité Azure. Pour en savoir plus sur le centre de sécurité Azure, voir :

- [Guide d’exploitation et de planification de la sécurité Azure centre](security-center-planning-and-operations-guide.md) , apprenez à planifier et comprendre les considérations de conception à adopter Azure le centre de sécurité.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md) : Découvrez comment contrôler l’état de vos ressources Azure
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité
- [Surveillance solutions des partenaires avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : rechercher des billets de blog sur la sécurité Azure et conformité

<properties
   pageTitle="Présentation de l’intégration de Microsoft Azure journal | Microsoft Azure"
   description="En savoir plus sur l’intégration de journal Azure, ses fonctionnalités clées et son fonctionnement."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Présentation de l’intégration de journal de Microsoft Azure (Preview)

En savoir plus sur l’intégration de journal Azure, ses fonctionnalités clées et son fonctionnement.

## <a name="overview"></a>Vue d’ensemble

Plateforme en tant que Service (PaaS) et Infrastructure en tant que Service (IaaS) hébergé dans Azure générer une grande quantité de données dans les journaux de sécurité. Les journaux ne contiennent des informations critiques fournissant intelligence et les idées puissantes en violations de stratégie, menaces internes et externes, problèmes de conformité réglementaire et anomalies de réseau, hôte et l’activité des utilisateurs.

Journal Azure intégration vous permet d’intégrer des journaux brutes à partir de vos ressources Azure dans vos systèmes locaux les informations de sécurité et de gestion des événements (SIEM). Intégration du journal Azure collecte Azure Diagnostics à partir de votre Windows *(WAD)* machines virtuelles, ainsi que les diagnostics de solutions des partenaires comme un pare-feu d’Application Web (WAF). Cette intégration fournit un tableau de bord unifiée pour tous vos actifs, en local ou dans le cloud, afin que vous pouvez agréger, corrélation, analyser et d’alerte pour les événements de sécurité.

![Intégration du journal d’Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?

Azure génère journalisation complète pour chaque service Azure. Ces fichiers journaux est classés par deux principaux types :

- **Journaux de contrôle/gestion**, qui permettent aux activités Azure Gestionnaire de ressources créer, mettre à jour et supprimer. Journaux d’Audit Azure est un exemple de ce type de journal.
- **Journaux de plan de données**, qui donnent une visibilité sur les événements déclenché dans le cadre de l’utilisation d’une ressource Azure. Exemples de ce type de journal l’événement Windows système, sécurité, et ouvre une Application sur un ordinateur virtuel.

Intégration du journal Azure prend actuellement en charge l’intégration des journaux d’Audit Azure, machine virtuelle journaux et alertes du centre de sécurité Azure.

Si vous avez des questions sur l’intégration de journal Azure, envoyez un message électronique à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez abordé intégration du journal Azure. Pour plus d’informations sur l’intégration de journal Azure et les types de journaux pris en charge, voir les rubriques suivantes :

- [Intégration du journal Microsoft Azure Azure journaux (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) – centre de téléchargement pour plus d’informations, configuration système requise et les instructions d’installation sur l’intégration de journal Azure.
- [Prise en main intégration du journal Azure](security-azure-log-integration-get-started.md) - ce didacticiel, vous guide dans l’installation d’intégration du journal Azure et d’intégration des journaux de stockage Azure Azure les journaux d’Audit et alertes du centre de sécurité.
- [Étapes de configuration partenaire](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ce billet de blog vous montre comment configurer l’intégration de journal Azure pour fonctionner avec les solutions des partenaires Splunk ArcSight HP et IBM QRadar.
- [Journal azure intégration Forum aux questions (FAQ)](security-azure-log-integration-faq.md) - ce forum aux questions répond aux questions sur l’intégration de journal Azure.
- [Alertes du centre de sécurité intégration avec Azure s’intégration](../security-center/security-center-integrating-alerts-with-log-integration.md) – ce document vous montre comment synchroniser les alertes du centre de sécurité, ainsi que des événements de sécurité machine virtuelle collectées par Diagnostics Azure et Azure les journaux d’Audit, avec le journal analytique ou votre solution SIEM.
- [Nouvelles fonctionnalités pour les diagnostics de Windows Azure et Azure les journaux d’Audit](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – ce billet de blog vous présente les journaux d’Audit Azure et d’autres fonctionnalités qui vous aident Familiarisez-vous dans les opérations de vos ressources Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

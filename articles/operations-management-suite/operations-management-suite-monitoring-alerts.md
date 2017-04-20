<properties 
   pageTitle="La gestion des produits de contrôle de Microsoft d’alerte | Microsoft Azure"
   description="Une alerte indique un problème qui doit être examiné par un administrateur.  Cet article décrit les différences de la façon dont les alertes sont créées et gérées dans System Center Operations Manager (SCOM) et journal Analytique et fournit les meilleures pratiques d’exploiter très rapidement les deux catégories de produits pour une stratégie de gestion des alertes hybride." 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Gestion des alertes d’analyse de Microsoft 

Une alerte indique un problème qui doit être examiné par un administrateur.  Il existe des différences distincts entre System Center Operations Manager (SCOM) et journal Analytique dans Suite de gestion des opérations (OMS) en ce qui concerne la création des alertes, comment elles sont gérées et analysées et comment vous êtes averti qu’un problème critique a été détecté.

## <a name="alerts-in-operations-manager"></a>Alertes dans Operations Manager
Alertes dans SCOM sont générées par les règles individuelles ou des moniteurs pour indiquer un problème spécifique.  Un moniteur peut générer une alerte lorsqu’il entre dans un état d’erreur pendant une règle peut générer une alerte pour indiquer un problème critique qui n’est pas directement lié à l’état d’un objet managé.  Packs d’administration incluent divers types de flux de travail créer des alertes pour l’application ou le service qu’ils gèrent.  Partie du processus de configuration d’un pack d’administration est réglage celle-ci pour vous assurer que vous ne recevez des alertes excessives de problèmes que vous ne considérez comme critiques.

![Affichage des alertes SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM offre complète de gestion des alertes alertes ayant un statut peut être modifié par les administrateurs lorsqu’ils travaillent pour résoudre le problème.  Lorsque le problème a été résolu, l’administrateur définit l’alerte vers fermé moment auquel il ne s’affichera plus dans les vues affichant des alertes actives.  Alertes qui sont générés à partir de moniteurs peuvent être résolus automatiquement lorsque le moniteur revient à un état correct.

![Statut d’alerte](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertes dans le journal Analytique
Une alerte dans le journal Analytique est créée à partir d’une requête de journal qui s’exécute automatiquement à intervalles réguliers.  Vous pouvez créer une règle d’alerte à partir de n’importe quelle requête journal.  Si la requête renvoie les résultats qui répondent aux critères que vous spécifiez, une alerte est créée.  Cela peut être une requête spécifique qui crée une alerte si un événement particulier est détecté, ou vous pouvez utiliser une requête plus générale qui recherche pour tous les événements d’erreur relatifs à une application spécifique.

Ouvrez une session Analytique alertes sont écrits sur le référentiel OMS en tant qu’événement et peuvent être récupérés avec une requête de journal.  Ils n’ayant pas un statut comme événements SCOM afin que vous pouvez indiquer lorsque le problème est résolu.

![Alerte OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Lorsque SCOM est utilisé comme source de données pour le journal Analytique, alertes SCOM sont écrits sur le référentiel OMS qu’ils sont créés et modifiés.  

![Alerte SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

La [solution de gestion des alertes](http://technet.microsoft.com/library/mt484092.aspx) fournit un récapitulatif des alertes actives et plusieurs requêtes courantes pour récupérer des différents jeux d’alertes.  Cela vous permet d’analyse plus efficace de vos alertes à un rapport dans SCOM.  Vous pouvez monter dans la hiérarchie sur à partir de résumés aux données détaillées et créer des requêtes ad-hoc pour récupérer différents jeux d’alertes.

![Solution de gestion des alertes](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notifications
Notifications dans SCOM envoient un courrier ou du texte en réponse aux alertes qui correspondent aux critères particuliers.  Vous pouvez créer des abonnements à des notifications différentes ayant différents utilisateurs en fonction de critères tels que l’objet en cours d’analyse, la gravité de l’alerte, le type de problème détecté une notification ou l’heure du jour.

Plusieurs abonnements peuvent être utilisés pour mettre en œuvre une stratégie de notification complète pour un grand nombre de packs d’administration.

![Alertes SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Journal Analytique peut vous informer par courrier qu’une alerte a été créée en définissant une action de notification de messagerie sur chaque [règle d’alerte](http://technet.microsoft.com/library/mt614775.aspx).  Il n’a pas la possibilité de SCOM le s’abonner à plusieurs alertes avec une seule règle.  Vous devez également créer vos propres règles d’alerte dans la mesure où OMS ne fournit aucune préconfiguré.

![Ouvrez une session alertes Analytique](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Vous ne peut pas complètement gérer alertes SCOM dans journal Analytique Cependant dans la mesure où vous pouvez uniquement modifier dans la Console d’opérations.  Journal Analytique est utile en tant que partie d’une gestion des alertes du processus via pour fournir des outils d’analyse que SCOM seul n’a pas.

## <a name="alert-remediation"></a>Alerte mise à jour
[Mise à jour](http://technet.microsoft.com/library/mt614775.aspx) fait référence à une tentative de corriger automatiquement le problème identifié par une alerte.
  
SCOM vous autorise à exécuter Diagnostics et récupérations en réponse à un écran d’un état incorrect.  Cela se produit sonnerie sur l’Analyseur de création de l’alerte.  Diagnostics et récupérations sont généralement implémentées comme un script qui s’exécute sur l’agent.  Un diagnostic tente de rassembler plus d’informations sur le problème détecté pendant une récupération pour tente de résoudre le problème.

Journal Analytique vous permet de démarrer une [procédure opérationnelle Azure Automation](https://azure.microsoft.com/documentation/services/automation/) ou appel un webhook en réponse à une alerte de journal Analytique.  Procédures opérationnelles peuvent contenir une logique complexe implémentée dans PowerShell.  Le script s’exécute dans Azure et accéder aux ressources Azure et ressources externes disponibles à partir du cloud.  Automatisation Azure n’a pas la possibilité d’exécuter des procédures opérationnelles sur un serveur dans votre centre de données locale, mais cette fonctionnalité n’est pas disponible actuellement lors du démarrage de la procédure opérationnelle en réponse aux alertes de journal Analytique.

À la fois des récupérations dans SCOM et procédures opérationnelles dans OMS peuvent contenir des scripts PowerShell, mais récupérations sont plus difficiles à créer et gérer des, car ils doivent être contenues dans un pack d’administration.  Procédures opérationnelles sont stockés dans Automation Azure qui fournit des fonctionnalités de création, de test et de gestion des procédures opérationnelles.

Si vous utilisez SCOM comme source de données pour journal Analytique, vous pouvez créer une alerte de journal Analytique à l’aide d’une requête de journal pour récupérer des alertes SCOM stockés dans le référentiel OMS.  Ainsi, vous permet d’exécuter une procédure opérationnelle Azure Automation en réponse à une alerte SCOM.  Bien entendu, étant donné que la runbook est exécuté en Azure, il s’agit pas une stratégie viable pour les récupérations des problèmes en local.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les détails des [alertes dans System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).
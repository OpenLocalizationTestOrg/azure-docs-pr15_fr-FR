<properties
    pageTitle="Développement et administration de la tâche liste dans les Services BizTalk | Microsoft Azure"
    description="Planification et travail d’aide pour le déploiement d’Azure BizTalk Services."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administration et liste des tâches de développement dans les Services BizTalk  

## <a name="getting-started"></a>Prise en main
Lorsque vous travaillez avec Microsoft Azure BizTalk Services, il existe plusieurs en local et composants, sur le nuage à prendre en compte. Pour commencer, tenez compte des flux de processus suivant :  

|Étape|Qui est responsable|Tâche|Liens connexes|
|----|----|----|----|
|1.|Administrateur|Créer l’abonnement Azure Microsoft à l’aide d’un compte Microsoft ou un compte professionnel|[Portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Administrateur|Créer ou mettre en service un BizTalk Service.|[Créer un BizTalk Service à l’aide du portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Administrateur|Votre enregistrement ou déploiement de Services BizTalk de votre entreprise|[Enregistrement et mise à jour d’un déploiement BizTalk Service sur le portail de Services BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Administrateur|S’applique si l’application utilise BizTalk carte Service pour vous connecter à un système de ligne-de-métier en local ou utilise un file d’attente ou une rubrique Destination.  Créer le Namespace Bus Service Azure. Donnez à cet espace de noms, nom de l’émetteur de Bus de Service et valeurs de clé de l’émetteur de Bus de Service pour les développeurs.|[Comment : créer ou modifier un Namespace de Service Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) et [obtenir un nom de l’émetteur et les valeurs de clé de l’émetteur](biztalk-issuer-name-issuer-key.md)|
|5.|Pour les développeurs|Installez le Kit de développement et créez le projet BizTalk Service dans Visual Studio.|[Installez les Services BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) et [créer des points de terminaison de messagerie enrichis sur Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Pour les développeurs|Déployez votre BizTalk Service project à votre BizTalk Service hébergé sur Azure.|[Déploiement et l’actualisation du projet de Services BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Administrateur|S’appliquent si vous utilisez EDI.  Vous pouvez ajouter des partenaires et créer des accords sur le portail Microsoft Azure BizTalk Services. Lorsque vous créez un contrat, vous pouvez ajouter le pont et/ou transformations créées par le développeur pour les paramètres d’accord.|[Configuration EDI et AS2 EDIFACT sur BizTalk Services portail](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Administrateur|À l’aide du portail classique Azure, surveiller l’intégrité de votre BizTalk Service, y compris des indicateurs de performance.|[BizTalk Services : Onglets de tableau de bord, surveiller et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Administrateur|À l’aide du portail de Services Microsoft Azure BizTalk, gérer utilisé par les Services BizTalk et le suivi des messages qu’ils soient traités par les fichiers pont.|[À l’aide du portail de Services BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Administrateur|Créer un plan de sauvegarde pour sauvegarder le BizTalk Service.|[Continuité des activités et récupération d’urgence dans les Services BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Étapes suivantes
[Exemples et des didacticiels](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Créez le projet dans Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installez les Services BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Concepts
[Créez le projet dans Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 et EDIFACT messagerie (commerciaux)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Autres ressources  
[Ajouter une Source, Destination et pont points de terminaison de la messagerie](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[En savoir plus et créer des tables des messages et des transformations](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Au moyen du Service de carte BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

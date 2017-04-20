<properties
   pageTitle="Comment mettre à l’échelle des fonctions Azure | Microsoft Azure"
   description="Comprendre comment Azure fonctions évoluer pour répondre aux besoins de vos charges de travail piloté par l’événement."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, cluster dynamique, architecture sans serveur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Comment mettre à l’échelle des fonctions Azure

## <a name="introduction"></a>Introduction

Avantage des fonctions Azure est que les ressources de cluster sont consommées uniquement lorsque cela est nécessaire. Cela signifie que vous ne payer pour les machines virtuelles inactives ou de réserver de la capacité pour lorsque vous en aurez besoin. En revanche, la plateforme attribue cluster power lorsque votre code est en cours d’exécution, évolue vers le haut que nécessaire pour gérer le chargement et s’adapte vers le bas lorsque code n’est pas exécuté.

Le mécanisme de cette nouvelle fonctionnalité est le plan de services dynamique.  

Cet article fournit une vue d’ensemble du fonctionne de l’offre de Service dynamique et comment la plateforme s’adapte à la demande pour exécuter votre code.

Si vous n’êtes pas encore familiarisé avec Azure fonctions, veillez à vérifier l’article [vue d’ensemble des fonctions Azure](functions-overview.md) pour mieux comprendre ses fonctions.

## <a name="configure-azure-functions"></a>Configurer les fonctions Azure

Deux paramètres principales sont liées à l’échelle :

* [Plan de services d’application Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou dynamique offre de Service
* Taille de la mémoire pour l’environnement d’exécution

Le coût d’une fonction change selon le plan de services que vous sélectionnez. Plan de Service dynamique, le coût est une fonction de temps d’exécution, la taille de la mémoire et nombre d’exécutions. Méthode d’allocation des frais uniquement lorsque votre code est en cours d’exécution.

Un plan de services d’application héberge vos fonctions sur machines virtuelles existantes, qui peuvent également être utilisés pour exécuter du code. Une fois que vous payez pour ces machines virtuelles chaque mois, il est sans frais supplémentaires pour les fonctions de l’exécution sur les.

## <a name="choose-a-service-plan"></a>Choisir une offre de service

Lorsque vous créez des fonctions, vous pouvez sélectionner pour les exécuter sur un plan de Service dynamique ou un [plan de services d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Dans le plan de Service d’application, vos fonctions seront exécutent sur un ordinateur virtuel dédié, comme travail d’applications web aujourd'hui pour Basic, Standard ou références SKU Premium.
Cette virtuelle dédié est affectée à vos applications et les fonctions et n’est toujours disponible si le code est en cours activement exécuté. Il s’agit d’un bon choix si vous avez machines virtuelles existants, sous utilisé déjà en cours d’exécution du code ou si vous prévoyez d’exécuter des fonctions en continu ou presque en permanence. Une machine virtuelle sépare coût à partir de taille pour l’exécution et la mémoire. Par conséquent, vous pouvez limiter le coût de nombreuses fonctions longue au coût d’ordinateurs virtuels un ou plusieurs fonctionnant sous.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]

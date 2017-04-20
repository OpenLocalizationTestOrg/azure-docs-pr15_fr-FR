<properties
   pageTitle="Création de nom de domaine complet pour une machine virtuelle dans Azure portal | Microsoft Azure"
   description="Apprenez à créer un nom de domaine complet ou nom de domaine complet pour un gestionnaire de ressources en fonction de machine virtuelle dans le portail Azure."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Créer un nom de domaine complet dans le portail Azure
Lorsque vous créez une machine virtuelle () dans le [portail Azure](https://portal.azure.com) à l’aide du modèle de déploiement Gestionnaire de ressources, une ressource IP publique pour la machine virtuelle est automatiquement créée. Cette adresse IP vous permet d’accéder à distance à la machine virtuelle. Bien que le portail ne crée pas un [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)ou le nom de domaine complet, par défaut, vous pouvez créer une fois que la machine virtuelle est créée. Cet article décrit les étapes pour créer un nom DNS ou le nom de domaine complet.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS tel que pour protocole RDP (Remote Desktop).

## <a name="next-steps"></a>Étapes suivantes
À présent que votre ordinateur virtuel a un nom IP et DNS public, vous pouvez déployer des structures d’application courantes ou services tels que IIS, SQL ou SharePoint.

Vous pouvez également en savoir plus sur [l’aide du Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) pour obtenir des conseils sur la création de vos déploiements Azure.
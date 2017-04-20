<properties
   pageTitle="Créer une identité Professionnel ou scolaire dans AAD | Microsoft Azure"
   description="Apprenez à créer une identité Professionnel ou scolaire dans Azure Active Directory à utiliser avec vos machines virtuelles Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Création d’une identité Professionnel ou scolaire dans Azure Active Directory à utiliser avec Linux machines virtuelles

Si vous avez créé un compte personnel Azure ou avez un abonnement MSDN personnel et créé le compte Azure pour tirer parti des crédits Azure MSDN--une identité de *compte Microsoft* vous permet de créer votre document. Nombreuses fonctionnalités d’Azure-- [modèles de groupe de ressources](../azure-resource-manager/resource-group-overview.md) est un exemple--nécessite un compte professionnel ou scolaire (une identité géré par Azure Active Directory) pour l’utiliser. Vous pouvez suivre les instructions ci-dessous pour créer un nouveau compte scolaire ou car peut être effectué en, un des principaux avantages de votre compte Azure personnel est qu’il est fourni avec un domaine Azure Active Directory par défaut que vous pouvez utiliser pour créer un nouveau compte scolaire ou que vous pouvez utiliser avec des fonctionnalités Azure qui en ont besoin.

Toutefois, les modifications récentes rendent possible gérer vos abonnements avec n’importe quel type de compte Azure en utilisant la `azure login` méthode de connexion interactive décrite [ici](../xplat-cli-connect.md). Vous pouvez utiliser ce mécanisme, ou vous pouvez suivre les instructions qui suivent. Vous pouvez également [créer un professionnel ou scolaire identité dans Azure Active Directory à utiliser avec machines virtuelles Windows](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]

<properties
   pageTitle="Conditions préalables pour les techniques de création d’une image de la machine virtuelle de Azure Marketplace | Microsoft Azure"
   description="Comprendre la configuration requise pour créer et déployer une image de machine virtuelle à Azure Marketplace pour d’autres personnes à acheter."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Conditions préalables pour les techniques de création d’une image de la machine virtuelle de Azure Marketplace
Lire le processus de manière approfondie avant de commencer et à comprendre où et pourquoi chaque étape est effectuée. Autant que possible, vous devez préparer les informations de votre société et d’autres données, téléchargez les outils nécessaires, et/ou créer composants techniques avant de commencer le processus de création d’une offre. Ces éléments doivent être claires de la révision de cet article.  

## <a name="download-needed-tools--applications"></a>Outils de téléchargement nécessaire et les applications
Vous devez disposer des éléments suivants prêts avant de commencer le processus :

- En fonction du système d’exploitation vous ciblez, installez [l’interface de ligne de commande](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) [applets de commande PowerShell Azure](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) ou Linux à partir de la page de [Téléchargements Azure](https://azure.microsoft.com/downloads/) .
- Installer Explorateur de stockage Azure CodePlex.
- Téléchargez et installez l’outil de Test de Certification pour Azure certifié :
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Vous avez besoin d’un ordinateur fonctionnant sous Windows pour exécuter l’outil de certification. Si vous ne disposez pas d’un ordinateur fonctionnant sous Windows disponible, vous pouvez exécuter l’outil à l’aide d’un ordinateur fonctionnant sous Windows virtuel dans Azure.

## <a name="platforms-supported"></a>Plates-formes prises en charge
Vous pouvez développer Azure sur des ordinateurs virtuels sous Windows ou Linux. Certains éléments de la publication, telles que la création d’un Azure compatible virtuel dur disque dur--utiliser différents outils et les étapes selon le système d’exploitation que vous utilisez :  

- Si vous utilisez Linux, reportez-vous à la section « Créer un disque dur virtuel Azure compatible (basé sur Linux) » du [guide de publication machine virtuelle image](marketplace-publishing-vm-image-creation.md).
- Si vous utilisez Windows, reportez-vous à la section « Créer un disque dur virtuel Azure compatible (basées sur Windows) » du [guide de publication machine virtuelle image](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Vous devez accéder à un ordinateur fonctionnant sous Windows pour :
- Exécutez l’outil de validation de certification.
- Créer l’URL de signature disque dur virtuel partagé access pour la présentation de certification disque dur virtuel.

## <a name="develop-your-vhd"></a>Développer votre disque dur virtuel
Vous pouvez développer des disques durs virtuels Azure dans le nuage ou sur site :

- Développement nuage signifie que toutes les étapes de développement sont effectuées à distance sur un disque dur virtuel résident sur Azure.
- Développement local nécessite le téléchargement d’un disque dur virtuel et au développement à l’aide d’infrastructure en local. Bien qu’il soit possible, nous ne recommandons pas dessus. Notez que développer pour Windows ou SQL en local, vous devez avoir les clés de licence pertinentes en local. Vous ne pouvez pas inclure ou installer SQL Server après avoir créé une machine virtuelle. Vous devez également baser votre offre sur une image SQL approuvée à partir du portail Azure. Si vous décidez de développer en local, vous devez effectuer certaines étapes différemment si vous développez dans le cloud. Vous pouvez trouver des informations pertinentes dans [créer une image de la machine virtuelle en local](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Étapes suivantes
À présent que vous examiné les conditions préalables et accompli les tâches nécessaires, vous pouvez avancer aide pour créer votre offre image machine virtuelle comme indiqué dans le [guide de publication machine virtuelle image](marketplace-publishing-vm-image-creation.md).

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)
- [Créer une machine virtuelle exécutant Windows dans le portail preview Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md

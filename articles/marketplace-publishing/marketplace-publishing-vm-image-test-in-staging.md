<properties
   pageTitle="Tester votre offre machine virtuelle de Marketplace | Microsoft Azure"
   description="Comprendre comment tester votre image machine virtuelle de Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Tester votre offre machine virtuelle de Azure Marketplace dans mise en attente

Environnement intermédiaire signifie déployer votre SKU privé « sandbox » dans laquelle vous pouvez tester et valider ses fonctionnalités avant de déployer à la place de marché. La référence (SKU) s’affiche dans les intermédiaires comme s’il s’agissait à un client qui a déployé. Votre image machine virtuelle doit être certifié vers mise en attente.

## <a name="step-1-push-your-offer-to-staging"></a>Étape 1 : Distribuer votre offre de mise en attente

1. Sous l’onglet **Publier** , cliquez sur **mise en attente de transmission**.

    ![dessin](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Si le portail de publication vous avertit des erreurs, les corriger.
3.  Dans la **qui peut accéder à votre offre intermédiaire ?** boîte de dialogue, entrez la liste des abonnements Azure que vous utiliserez pour afficher un aperçu de votre offre dans le [portail Azure preview](https://portal.azure.com).

    >[AZURE.NOTE] En cas de Machines virtuelles et Solution modèles, veuillez **ne pas** d’autorisation abonnements de type fournisseur, DreamSpark ou Azure dans la zone Ouvrir.


    > En cas de Machines virtuelles, lorsque vous cliquez sur le bouton **De transmission pour la mise en attente**, les étapes suivantes sont exécutées derrière la scène. Vous ne pourrez pas visualiser l’avancement de chaque étape sous l’onglet Publier dans la publication portail. Vous devez archiver cette page à intervalles réguliers (jusqu'à ce que la colonne Statut indique intermédiaire) pour toutes les informations de panne dont vous avez besoin de correction de votre fin.

    > - Tout d’abord votre demande intermédiaire accède à l’équipe de certification qui valider le disque dur virtuel. Toutefois, si votre demande a est uniquement marketing modification, l’étape de certification est ignorée.
    > - Une fois que la certification est terminée, la réplication de l’offre démarrer sur Azure centres de données. En règle générale, il vous permet d’accéder 24-48hours pour la réplication terminer mais peut prendre jusqu'à une semaine selon la taille de ce dernier. Toutefois, si votre demande a est uniquement marketing modification, la réplication est plus rapide.
    > - Lorsque la réplication est terminée, l’offre sera disponible dans le [portail Azure](http:/portal.azure.com). À ce moment, l’état sont placées dans la publication portail. Une offre d’intermédiaire est visible dans le [portail Azure](http:/portal.azure.com) avec uniquement les identificateurs de messagerie associées à l’abonnement avec lequel l’offre est mis en place.

4. Connectez-vous à [Azure afficher un aperçu de portail](https://portal.azure.com) en utilisant l’un des abonnements Azure répertoriées dans l’étape précédente.
5. Rechercher votre offre et valider vos points image machine virtuelle :
  - Assurez-vous que contenu marketing s’affiche correctement sur le marché.
  - Déploiement de bout en bout de l’image de la machine virtuelle.

      ![portail de carte img](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] Votre offre restera dans les intermédiaires jusqu'à ce que vous avertir Microsoft via le portail de publication [onglet**Publier** > cliquez sur le bouton **« demander l’approbation pour Push pour Production »**] que vous êtes prêt à transmission en production. Ceci est le moment idéal pour que tous les membres de votre équipe chèque sur tous les éléments de préparation de votre offre accédant répertoriés.

> La plateforme intermédiaire est conçue pour tester l’offre en mode Aperçu par l’éditeur. Nous vous déconseillons de cette platofrm à des fins de commerce.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre offre est « intermédiaire » et que vous avez testé ses fonctionnalités et contenu marketing, vous pouvez passer à la phase de publication finale, **étape 4**: [déploiement de votre offre à la place de marché](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)

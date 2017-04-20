<properties
   pageTitle="Déployez votre offre à la place de marché Azure | Microsoft Azure"
   description="En savoir plus sur et passez en revue les instructions pour déployer votre offre--image machine virtuelle, service développeur, un service de données, etc., à la Azure Marketplace."
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
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Déployez votre offre la Azure Marketplace
Lorsque vous êtes satisfait de votre offre (autrement dit, vous avez testé scénarios clients, marketing contenu, etc.) et vous êtes prêt à lancer, demandez à **transmission en production** sous l’onglet **Publier** .  

1. Les quatre étapes sous la procédure pas à pas de page dans la publication portail doit être terminées et vert. Pour les offres Machine virtuelle, assurez-vous que les instructions suivantes sont suivies.

    ![dessin][img-pubportal-walkthru-checked]

2. Sélectionnez l’onglet **Publier** dans la liste sur le côté gauche.

    ![dessin][img-pubportal-menu-publish]

3. Cliquez sur le bouton **demander une approbation pour diffuser en production**. Une fois que la demande est faite, l’équipe approbation exécute une révision finale et puis votre offre est disponible dans les Azure Marketplace.

    ![dessin][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] En cas de Machines virtuelles, lorsque vous cliquez sur le bouton approbation de la demande pour diffuser en production, les étapes suivantes sont exécutées derrière la scène. Vous ne pourrez pas visualiser l’avancement de chaque étape sous l’onglet Publier dans la publication portail. Vous devez vérifier cette page à intervalles réguliers (jusqu'à ce que la colonne Statut indique « Adresse ») pour toutes les informations de panne dont vous avez besoin de correction de votre fin.

> - Tout d’abord votre demande de production accède à l’équipe de certification qui valider le disque dur virtuel. Toutefois, si vous mettez à jour votre offre déjà répertorié, et la demande a uniquement marketing modification, l’étape de certification est ignorée.
> - À l’étape suivante, la demande entrée à l’équipe de validation du contenu qui vérifier le contenu marketing de l’offre.
> - Si les étapes ci-dessus sont réussies, l’offre est approuvée en production. Pour l’instant, le statut devient « répertorié » dans le portail de publication. Toutefois, ce statut « Adresse » n’implique pas que le processus est terminé. Les étapes suivantes doivent être complet avant l’offre n’est disponible dans les Azure Marketplace.
> - Une fois que l’offre est approuvée en production dans l’étape précédente, la réplication de l’offre démarrer sur Azure centres de données. En règle générale, il vous permet d’accéder 24-48hours pour la réplication terminer mais peut prendre jusqu'à une semaine selon la taille de ce dernier. Toutefois, si vous mettez à jour votre offre déjà répertorié et il a uniquement marketing modification, la réplication est plus rapide.
> - Lorsque la réplication est terminée, puis l’offre seront disponible dans la Azure Marketplace.

> Vous pouvez toujours supprimer l’offre lorsqu’il est dans un état **brouillon** (c'est-à-dire, jamais **transmission mis en œuvre** ou **Push en production**). Sous l’onglet **historique** , cliquez sur le bouton **Annuler le brouillon** en bas de la page pour supprimer un brouillon.


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Liste de vérification de production pour toutes les offres Machine virtuelle

- Vérifiez que vous êtes un partenaire agréé de Microsoft Azure
- Sous l’onglet références SKU, l’option « Masquer cette référence (SKU) à partir du marché, car elle doit toujours être acheté via un modèle de solution » doit être marqués comme Oui uniquement si la référence (SKU) fait partie d’un modèle de Solution. Dans tous les autres cas, cette option doit toujours être marquée comme non.
- N’oubliez pas : Ne modifiez pas le paramètre de visibilité de référence (SKU) une fois que la référence (SKU) est répertorié. Nous ne prennent pas en charge cette fonctionnalité.
- Assurez-vous que les logos respectent les instructions de logo Azure Marketplace indiquées ci-dessous.
- Description de l’offre et de référence (SKU) ne doivent pas être identiques.
- De référence (SKU) titre et proposer Long synthèse ne doivent pas être identiques.
- Titre de référence (SKU) et récapitulatif des offres ne doivent pas être identiques.
- Titres de référence (SKU) ne doivent pas être identiques pour une offre avec plusieurs références SKU.

**Instructions de logo Azure Marketplace**

- La conception Azure comporte une palette de couleurs simple. Limiter le nombre de principal et secondaire couleurs sur votre logo.
- Les couleurs du thème du portail Azure sont blanches et noire. Par conséquent, évitez d’utiliser ces couleurs comme couleur d’arrière-plan de vos logos. Utiliser une couleur qui rendre vos logos mis en évidence dans le portail Azure. Nous vous recommandons de couleurs primaires simples. Si vous utilisez un arrière-plan transparent, vérifiez que le logo/texte n’est pas blanc ou noir.
- N’utilisez pas un arrière-plan dégradé sur le logo.
- Évitez de placer du texte, même votre société ou le nom de la marque, sur le logo.
- L’apparence de votre logo doit être « plat » et évitez de dégradés.
- Le logo ne doit pas être étiré.

**Conseils supplémentaires pour le logo Hero :**

- Le logo Hero est facultatif. L’éditeur peut choisir de ne pas télécharger un logo Hero. **Toutefois une fois téléchargé l’icône hero ne peuvent pas être supprimé à partir de la publication portail. À ce stade, le partenaire devez suivre les indications Azure Marketplace pour les icônes Hero dois-je que l’offre n’est pas approuvée en production.**
- Le nom d’affichage de Publisher, titre de référence (SKU) et l’offre long résumé s’affichent dans la couleur de police blanc. Par conséquent, vous devez éviter tout en conservant de n’importe quelle couleur de lumière à l’arrière-plan de l’icône Hero. Noir, arrière-plan blanc et transparent n’est pas autorisée pour afficher les icônes Hero.
- L’éditeur nom complet, titre de référence (SKU), l’offre long résumé et le bouton Créer sont incorporées par programme à l’intérieur du logo Hero une fois que l’offre est répertorié. Si vous devez entrer pas du texte lors de la création du logo Hero. Laissez vide sur la droite, car le texte (c'est-à-dire Publisher nom complet, titre de référence (SKU), l’offre long synthèse) sont incluses par programme en nous sur cet emplacement. L’espace vide pour le texte doit être 415 x 100 droite (et il est décalé en 370px à partir de la gauche).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Liste de vérification de production supplémentaires déjà répertoriés Machine virtuelle offre

- Vérifier s’il existe déjà une offre portant le même nom offre de votre entreprise. Si Oui, vous devez ajouter une nouvelle version de la référence (SKU) dans l’offre existante au lieu de créer une nouvelle offre en double.
- Ne modifiez pas disque de données entre deux versions du même SKU.
- La Azure Marketplace ne reconnaît pas tarification modification des listés références SKU car il influence la facturation des clients existants. Assurez-vous de ne pas modifier le prix de gestion sont-elles répertoriées dans les régions dans lesquels la référence (SKU) est disponible. Toutefois, vous pouvez ajouter de nouvelles références SKU ou ajouter de nouvelles zones à une référence (SKU) existant.


## <a name="next-steps"></a>Étapes suivantes
Une fois que l’offre effectif, tester les scénarios client pour vérifier que tous les contrats fonctionnent correctement dans l’environnement de production comme testé et validées dans l’environnement intermédiaire.

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png

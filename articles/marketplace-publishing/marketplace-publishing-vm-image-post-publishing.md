<properties
   pageTitle="Gestion de votre image machine virtuelle sur Azure Marketplace | Microsoft Azure"
   description="Guide détaillé sur la gestion de votre image de machine virtuelle sur Azure Marketplace après la publication initiale."
   services="Azure Marketplace"
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
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guide de leur production pour les offres machine virtuelle dans la Azure Marketplace

Cet article explique comment vous pouvez mettre à jour une offre Machine virtuelle live dans le Azure Marketplace. Également, il vous guide dans la procédure d’ajout d’une ou plusieurs nouvelles références SKU à une offre existante et que vous supprimez une Machine virtuelle offre ou une référence (SKU) live dans le Azure Marketplace.

Une fois qu’un article offre/stock est enregistré dans le [Portail Azure](http://portal.azure.com), vous ne pouvez pas modifier les champs indiquées ci-dessous :

- **Offrent identificateur :** [Portail de publication -> Machines virtuelles -> sélectionner votre offre -> onglet Images machine virtuelle -> identificateur offrent]
- **Identificateur de référence (SKU) :** [Portail de publication -> Machines virtuelles -> sélectionner votre offre -> références SKU onglet -> Ajouter une référence SKU]
- **Publisher Namespace :** [Portail de publication -> Machines virtuelles -> onglet -> communiquer contactez-nous sur votre société (figurant sous « Étape 2 inscrire votre société ») -> procédure pas à pas Publisher Namespace -> Namespace]

Une fois que l’offre/référence (SKU) est répertorié dans la [Azure Marketplace](http://azure.microsoft.com/marketplace), vous ne pouvez pas modifier les champs indiquées ci-dessous :

- **Offrent identificateur :** [Portail de publication -> Machines virtuelles -> sélectionner votre offre -> onglet Images machine virtuelle -> identificateur offrent]
- **Identificateur de référence (SKU) :** [Portail de publication -> Machines virtuelles -> sélectionner votre offre -> références SKU onglet -> Ajouter une référence SKU]
- **Publisher Namespace :** [Portail de publication -> Machines virtuelles -> onglet -> procédure pas à pas dire contactez-nous sur votre société (trouvé sous étape 2 inscrire) Publisher Namespace -> Namespace]
- **Ports** [Portail de publication -> Machines virtuelles -> sélectionner votre offre -> onglet Images machine virtuelle -> Ouvrir les Ports]
- **Tarifs changement de SKU(s) répertoriés**
- **Modification du modèle de liste SKU(s) de facturation**
- **Suppression de la liste régions du SKU(s) répertoriés**
- **Modifier le nombre de disque de données de liste SKU(s)**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. comment mettre à jour les détails techniques d’une référence SKU

Vous pouvez ajouter une nouvelle version à la référence (SKU) répertorié et publier à nouveau votre offre en suivant les étapes indiquées ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **IMAGES machine virtuelle** .
4. À partir de la section **références SKU** de l’onglet **IMAGES machine virtuelle** , recherchez la référence (SKU) que vous souhaitez mettre à jour.
5. Ensuite, ajoutez un nouveau numéro de version de la référence (SKU) et cliquez sur le bouton **« + »** . La nouvelle version doit être au format X.Y.Z où X, Y, Z sont des entiers. Modifications de version ne doivent être incrémentielles.
6. Dans la zone **URL du disque dur virtuel du système d’exploitation** , ajoutez la signature d’un accès partagé QU'URI créée pour le système d’exploitation disque dur virtuel et enregistrer les modifications.

    >[AZURE.IMPORTANT] Vous ne pouvez pas incrément/incrémentation le nombre de disque de données d’une référence SKU répertorié. Vous devez créer une nouvelle référence SKU dans ce cas. Reportez-vous à la section [3. Comment ajouter une nouvelle référence SKU sous une offre répertoriée](#3-how-to-add-a-new-sku-under-a-live-offer) pour obtenir des instructions détaillées.

7. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. comment mettre à jour les détails techniques d’une offre ou d’une référence SKU

Vous pouvez mettre à jour la vont (marketing, juridique, prend en charge, catégories) détails de votre offre direct ou de référence (SKU) dans la Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 mise à jour la description de l’offre et logos

Vous pouvez mettre à jour les détails de l’offre et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **Anglais (États-Unis)** .
5. Dans la barre latérale gauche, cliquez sur l’onglet **Détails** . Sous la section *DESCRIPTION* de l’onglet **Détails** vous pouvez mettre à jour le titre de l’offre, proposer résumé, proposez résumé long et enregistrer les modifications.

    >[AZURE.NOTE] Veuillez tenir compte des opérations suivantes pendant que vous mettez à jour les détails de la référence (SKU).
    **Ne saisissez du texte en double sous la description de l’offre et la description de la référence (SKU). Ne saisissez du texte en double sous le titre de référence (SKU) et l’offre long résumé. Ne saisissez du texte en double sous le titre de référence (SKU) et le résumé offre.**

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. Sous la section *LOGOS* de l’onglet **Détails** , vous pouvez mettre à jour les logos. Toutefois, vous assurer que les logos suivent les [instructions Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : contenu marketing Marketplace fournir -> Détails -> Azure Marketplace Logo instructions).

    >[AZURE.NOTE] Icône Hero est facultative. Vous pouvez choisir de ne pas télécharger une icône Hero. Toutefois, une fois que l’icône Hero est téléchargé, puis rien n’est prévu pour le supprimer de la publication portail. Dans ce cas, vous devez suivre les [instructions d’icône Hero](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : contenu marketing Marketplace fournir -> Détails -> conseils supplémentaires pour la bannière logo Hero).

7. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
8. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Mettre à jour la description de la référence (SKU)

Vous pouvez mettre à jour les détails de la référence (SKU) et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connecter le [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **Anglais (États-Unis)** .
5. Dans la barre latérale gauche, cliquez sur l’onglet **PLANS** . Sous la section *références SKU* de l’onglet **PLANS** , vous pouvez mettre à jour les titre de référence (SKU), les références SKU résumé et les informations de référence (SKU) description et enregistrer les modifications.

    >[AZURE.NOTE] Veuillez tenir compte des opérations suivantes pendant que vous mettez à jour les détails de la référence (SKU). **Ne saisissez du texte en double sous la description de l’offre et la description de la référence (SKU). Ne saisissez du texte en double sous titre de référence (SKU) et l’offre long résumé. Ne saisissez du texte en double sous le titre de référence (SKU) et le résumé offre.**

6. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce lien
7. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 modifier les liens existants ou ajouter de nouveaux liens

Vous pouvez modifier les liens existants ou ajouter de nouveaux liens et puis publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connecter le [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **Anglais (États-Unis)** .
5. Dans la barre latérale gauche, cliquez sur l’onglet **liens** .
6. Si vous voulez ajouter un nouveau lien, puis sous les *liens* de la section cliquez sur le bouton **Ajouter un lien** . La boîte de dialogue *« Ajouter un lien »* s’ouvre. Dans cette boîte de dialogue, vous pouvez ajouter le lien titre et URL champs et enregistrer les modifications. Vous pouvez entrer un lien qui contient des informations qui peuvent aider les clients.
7. Si vous voulez mettre à jour ou supprimer un lien existant, puis sélectionnez le lien approprié et cliquez sur le bouton Modifier ou le bouton Supprimer en conséquence.

    >[AZURE.NOTE] Vérifiez que les liens qui vous sont entrés dans cette section fonctionnent correctement, comme ces liens sont-elles validées pendant le processus de demande de production.

8. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 modifier un exemple d’image existantes ou ajouter une nouvelle image d’exemple

Vous pouvez modifier un exemples d’images existantes ou ajouter de nouvelles images exemple et puis publier à nouveau votre offre en suivant les étapes ci-dessous :

>[AZURE.NOTE] Exemple qu’une seule image est affichée dans la [https://portal.azure.com](https://portal.azure.com).

1. Connecter le [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **Anglais (États-Unis)** .
5. Dans la barre latérale gauche, cliquez sur l’onglet **Exemples d’IMAGES** .
6. Si vous souhaitez ajouter une nouvelle image d’exemple, puis sous la section *Exemples d’Images* cliquez sur le bouton **Télécharger une IMAGE** et puis enregistrez les modifications.

    >[AZURE.NOTE] Y compris un exemple d’image est une étape facultative.

7. Si vous voulez mettre à jour ou supprimer une image d’exemple existante, puis recherchez l’image de l’échantillon approprié et puis cliquez sur le bouton **Remplacer l’IMAGE** ou le bouton Supprimer en conséquence.

8. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2,5 mise à jour de contenu légal

Vous pouvez mettre à jour le contenu légal et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connecter le [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **Anglais (États-Unis)** .
5. Dans la barre latérale gauche, cliquez sur l’onglet informations **juridiques** . Sous la section *juridique* , vous pouvez mettre à jour vos stratégies/conditions d’utilisation. Entrez ou collez les stratégies/termes dans la zone de texte *Conditions d’utilisation* et enregistrer les modifications.
6. La limite de caractères pour les conditions juridiques d’utilisation est de 1 000 000 caractères.
7. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 mettre à jour les informations de support

Vous pouvez mettre à jour les informations de support et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connecter le [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **prise en charge** .
4. Sous la section *Contact ingénierie* de l’onglet **prise en charge** , vous pouvez mettre à jour les détails du contact. Ces informations sont utilisées pour les communications internes entre le partenaire et Microsoft.
5. Sous la section de *Support client* de l’onglet **prise en charge** , vous pouvez mettre à jour les détails du contact prise en charge, tels que **nom, messagerie téléphone** et **URL du support technique**. Ces informations sont utilisées pour les communications internes entre le partenaire et Microsoft.

    >[AZURE.NOTE] Si vous souhaitez fournir uniquement le support par courrier électronique, fournissent un numéro de téléphone factice sous la section **Service clientèle** . Dans ce cas, votre messagerie fournie est utilisée à la place.

6. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
7. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 mettre à jour les catégories

Vous pouvez mettre à jour de la section catégories pour votre offre et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connecter le [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **catégories** .
4. Dans la section *catégories* , vous pouvez mettre à jour les catégories pour votre offre et enregistrer les modifications. Vous pouvez sélectionner un maximum de cinq catégories de la galerie Azure Marketplace.
5. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
6. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. comment ajouter une nouvelle référence SKU sous une offre répertoriée

Vous pouvez ajouter une nouvelle référence SKU sous votre offre live en suivant les étapes indiquées ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **références SKU** . Après avoir cliqué sur ce sur le bouton **Ajouter une référence (SKU)**.  Nouvelle boîte de dialogue s’ouvre. Entrez un identificateur de référence (SKU) en minuscules. Cochez la case à cocher pour afficher vos propres facturation model(BYOL) si vous souhaitez publier le nouvelles références SKU modèle de facturation BYOL. Dans le cas contraire, désactivez la case à cocher pour BYOL. Après avoir cliqué sur ce sur la marque de graduation dans la boîte de dialogue Créer une nouvelle référence SKU. Si vous ne pas optez pour le modèle de facturation BYOL pour le nouvelles références SKU, puis le modèle de facturation est automatiquement fixé à toutes les heures pour le nouvelles références SKU. Si vous souhaitez activer la version d’évaluation gratuite de 30 jours pour toutes les heures modèle de facturation, puis cliquez sur l’option « Un mois » pour « est une version d’évaluation gratuite disponible ? ». Sinon, sélectionnez « Aucune période d’évaluation ». [Remarque : l’option « une version d’évaluation gratuite disponible est ? » apparaît uniquement si vous n'avez pas sélectionné BYOL dans la boîte de dialogue lors de la création le nouvelles références SKU.]

    >[AZURE.IMPORTANT] L’option « Masquer cette référence (SKU) à partir du marché, car elle doit toujours être acheté via un modèle de solution » doit être marqués comme « Oui » uniquement si vous êtes accepté pour la publication d’une offre de modèle de solution sur le marché Azure. Dans le cas contraire, cette option doit toujours être marquée comme « Non ».

4. Maintenant dans la barre latérale gauche, cliquez sur l’onglet **IMAGES machine virtuelle** et découvrez le nouvelles références SKU que vous avez créée.
5. Pour configurer le nouvelles références SKU, reportez-vous à l’étape 5 de ce [lien](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) pour obtenir des instructions.
6. Pour ajouter les documents marketing pour le nouvelles références SKU, reportez-vous à la section Étape 1 : contenu marketing Marketplace fournir -> Détails -> numéros de point 2 à 5 de ce [lien](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Pour ajouter les informations de tarification pour le nouvelles références SKU, reportez-vous à la section 2.1. Définir vos prix machine virtuelle de ce [lien](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Après avoir apporté les modifications, accédez à l’onglet **Publier** , puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
9. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. comment modifier le nombre de disque de données pour une référence SKU répertorié

Vous ne pouvez pas incrément/incrémentation le nombre de disque de données d’une référence SKU répertorié. Vous devez créer une nouvelle référence SKU dans ce cas. Reportez-vous à la section [3. Comment ajouter une nouvelle référence SKU sous une offre live](#3-how-to-add-a-new-sku-under-a-live-offer) pour obtenir des instructions détaillées.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. comment supprimer une offre répertoriée dans le Azure Marketplace

Il existe différents aspects qui doivent être prises en charge dans le cas d’une requête pour supprimer une offre live. Suivez les étapes ci-dessous pour obtenir des conseils de l’équipe de support pour supprimer une offre d’apparaître le Azure Marketplace :

1.  Déclencher un tickets de support à l’aide de ce [lien](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Sélectionnez le type de problème comme **« Gestion des offres »** et sélectionnez la catégorie comme **« Modification une offre et/ou la référence (SKU) déjà en production »**
3.  Soumettre la demande

L’équipe de support vous guidera tout au long du processus de suppression offre/référence (SKU).

>[AZURE.NOTE] Vous pouvez toujours supprimer l’offre lorsqu’il est dans un état Brouillon (c'est-à-dire, pas dans intermédiaire ou PRODUCTION) en cliquant sur le bouton **Annuler le brouillon** sous l’onglet **historique** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. comment supprimer une référence SKU répertoriée dans le Azure Marketplace

Vous pouvez supprimer une référence SKU répertoriée de la Azure Marketplace en suivant les étapes indiquées ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans le volet gauche, cliquez sur l’onglet **références SKU** .
4. Sélectionnez la référence (SKU) que vous voulez supprimer, cliquez sur le bouton supprimer par rapport à cette version de Windows.
5. Une fois terminé, accédez à l’onglet Publier dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau l’offre sur le marché Azure.
6. Une fois que l’offre obtient republier sur le marché Azure, la référence (SKU) est supprimé à partir de la Azure Marketplace et le portail Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. comment supprimer la version actuelle d’une référence SKU répertoriée dans le Azure Marketplace

Vous pouvez supprimer la version actuelle d’une référence SKU répertoriée de la Azure Marketplace en suivant les étapes indiquées ci-dessous. Une fois le processus terminé, la référence (SKU) sera restaurée à sa version précédente.

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2.  Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3.  Dans le volet gauche, cliquez sur l’onglet **IMAGES machine virtuelle** .
4.  Sélectionnez la référence (SKU) dont vous voulez supprimer, cliquez sur le bouton Supprimer cette version la version actuelle.
5.  Une fois terminé, accédez à l’onglet **Publier** dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau l’offre sur le marché Azure.
6.  Une fois que l’offre obtient republier sur le marché Azure, la version actuelle de la référence (SKU) répertorié est supprimée à partir de la Azure Marketplace et le portail Azure. La référence (SKU) est annulée à sa version précédente.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. comment rétablir le prix de vente aux valeurs de production
J’ai changé le prix d’une référence SKU répertoriée (ou que j’ai supprimé les zones facturation d’une référence SKU répertoriée). Dans la mesure où il n’est pas pris en charge sur le marché Azure, je veux rétablir mes modifications pour les valeurs de production. Comment puis-je obtenir ?

Suivez les étapes indiquées ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **tarification** .
4. Sous l’onglet tarification, sélectionnez une région dont vous voulez rétablir la tarification.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. En cas de références SKU horaire modèle de facturation, réinitialisez les prix de tous les cœurs comme elles se trouvent dans la production pour la zone sélectionnée. Références SKU modèle de facturation BYOL, vérifiez la référence SKU disponibles dans la région en cochant la case à cocher par rapport à la référence (SKU) sous la section disponibilité de référence (SKU) EXTERNALLY-LICENSED (BYOL) (voir la capture d’écran ci-dessous).

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Cliquez sur le bouton **AUTOPRICE autres marchés basé sur prix IN États-Unis**.

    >[AZURE.NOTE] L’étiquette du bouton peut être différent en fonction de la région que vous avez sélectionnés. Étant donné que nous avons sélectionné États-Unis lors de la création de ce document, donc le bouton est intitulé « Autres marchés basées sur les prix aux États-Unis de prix automatique » dans la capture d’écran ci-dessous.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. L’Assistant de prix automatique s’ouvre. La première page affiche la sélection de marché base. Vérifiez votre section et revenir à la page suivante en cliquant sur le bouton **« -> »** .

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Option permettant de sélectionner les cœurs et les plans s’affichera dans la page 2. Sélectionnez les plans de votre choix et les cœurs et cliquez sur « -> » bouton.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Page 3 affiche les marchés et régions. Cliquez sur le bouton bascule tout pour sélectionner toutes les régions ou vérifier manuellement les zones à votre région. Cliquez sur le bouton « -> » pour atteindre la page suivante.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Page 4 affiche le taux de change. Cliquez sur le bouton fin aux étapes. L’Assistant va réinitialiser les tarifs en fonction de votre sélection.

11. Ensuite, accédez à l’onglet tarification et cliquez sur le bouton « Affichage résumé et modifications ».
Sélectionnez « Brouillon » dans la section « Afficher la Version » et « Production » dans la section « Comparer avec » (voir la capture d’écran ci-dessous). Si vous ne voyez aucune différence tarification, elle implique tarifs a été restaurée les valeurs de production avec succès.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Après avoir apporté les modifications, accédez à l’onglet Publier, puis cliquez sur le bouton **De transmission pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
13. Une fois que vous avez testé votre offre de reclassement, accédez à l’onglet Publier dans la publication de portail et cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. comment rétablir le modèle de facturation pour les valeurs de production
J’ai modifié le modèle de facturation d’une référence SKU répertorié. Dans la mesure où il n’est pas pris en charge sur le marché Azure, je veux rétablir mes modifications pour les valeurs de production. Comment puis-je obtenir ?

Suivez les étapes suivantes :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **références SKU** .
4. Cliquez sur le bouton Modifier pour rétablir le modèle de facturation. Une fenêtre s’ouvre. Activez ou désactivez la case à cocher **« facturation et licences est effectuée avec l’extérieur à partir d’Azure (aka mettre votre propre licence) »** en conséquence.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Une fois, reportez-vous à la réponse de la question 8 dans ce document pour revenir en arrière les tarifs.
6. Une fois qu’atteindre l’onglet **Publier** dans la publication de portail et push l’offre de mise en attente pour le tester. Une fois vous avez fini avec l’offre d’un test, puis cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. comment restaurer le paramètre de visibilité d’une référence SKU répertoriée à la valeur de production

Suivez les étapes suivantes :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES virtuelles** et sélectionnez votre offre.
3. Dans la barre latérale gauche, cliquez sur l’onglet **références SKU** .
4. Sélectionnez votre SKU et rétablir le paramètre de visibilité de la référence (SKU) à la valeur de production.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Une fois vous en avez terminé avec les modifications, puis cliquez sur le bouton **Demander l’approbation pour transmission à PRODUCTION** à publier à nouveau votre offre sur le marché Azure.

## <a name="see-also"></a>Voir aussi
- [Mise en route : Comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)
- [Présentation des perspectives vendeur création de rapports](marketplace-publishing-report-seller-insights.md)
- [Présentation des rapports de paiement](marketplace-publishing-report-payout.md)
- [Comment modifier votre inciter revendeur Cloud Solution fournisseur](marketplace-publishing-csp-incentive.md)
- [Résolution des problèmes de publication courantes sur le marché](marketplace-publishing-support-common-issues.md)
- [Obtenir une assistance technique en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)
- [Création d’une image de la machine virtuelle en local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Créer une machine virtuelle exécutant Windows dans le portail preview Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

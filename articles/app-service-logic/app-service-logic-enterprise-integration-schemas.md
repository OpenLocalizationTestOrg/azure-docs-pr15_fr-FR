<properties
    pageTitle="Vue d’ensemble des schémas et de l’entreprise Integration Pack | Microsoft Azure"
    description="Découvrez comment utiliser des schémas avec les applications Enterprise Integration Pack et logique"
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Découvrez les schémas et le Pack de l’intégration d’entreprise  

## <a name="why-use-a-schema"></a>Pourquoi utiliser un schéma ?
Utiliser des schémas pour confirmer que vous recevez des documents XML sont valides, avec les données attendues dans un format prédéfini. Schémas sont utilisés pour valider les messages échangés dans un scénario B2B.

## <a name="add-a-schema"></a>Ajouter un schéma
À partir du portail Azure :  

1. Sélectionnez **plusieurs services**.  
![Portail de capture d’écran de Azure, avec « Services plus » mis en surbrillance](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. Dans la zone de recherche filtre, entrez **l’intégration**, puis sélectionnez **Comptes l’intégration** de la liste des résultats.     
![Capture d’écran de la zone de recherche de filtre](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Sélectionnez le **compte de l’intégration** à laquelle vous ajoutez le schéma.    
![Capture d’écran de la liste des comptes d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Sélectionnez la vignette **schémas** .  
![Capture d’écran de IEP intégration compte « Schémas » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Ajouter un fichier de schéma est inférieure à 2 Mo  

1. Dans la carte de **schémas** qui s’ouvre (à partir de la procédure précédente), sélectionnez **Ajouter**.  
![Carte de capture d’écran des schémas, avec le bouton « Ajouter » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Entrez un nom pour votre schéma. Ensuite, pour télécharger le fichier de schéma, sélectionnez l’icône de dossier en regard de la zone de texte **schéma** . Une fois que le processus de téléchargement est terminé, sélectionnez **OK**.    
![Capture d’écran de « Ajouter un schéma », « Fichier petite » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Ajouter un fichier de schéma supérieur à 2 Mo (avec un maximum de 8 Mo)  

Ce processus varie selon le niveau d’accès conteneur blob : **Public** ou **pas de l’accès anonyme**. Pour déterminer ce niveau d’accès, dans l' **Explorateur de stockage Azure**, sous **Conteneurs Blob**, sélectionnez le conteneur blob souhaité. Sélectionnez **sécurité**, puis sélectionnez l’onglet **Niveau d’accès** .

1. Si le niveau d’accès de sécurité blob est **Public**, procédez comme suit.  
  ![Capture d’écran de Azure Explorateur de stockage, avec « Conteneurs Blob », « Sécurité » et « Public » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    un. Télécharger le schéma de stockage, puis copiez l’URI.  
    ![Capture d’écran du compte de stockage, avec URI mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. Dans **Ajouter un schéma**, sélectionnez **fichier volumineux**et fournir le URI dans la zone de texte **URI contenu** .  
    ![Capture d’écran des schémas, avec le bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Si le niveau d’accès de sécurité blob n’est **pas l’accès anonyme**, procédez comme suit.  
  ![Capture d’écran de Azure Explorateur de stockage, avec « Conteneurs Blob », « Sécurité » et « Aucun accès anonyme » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    un. Télécharger le schéma de stockage.  
    ![Capture d’écran du compte de stockage](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Générer une signature d’un accès partagé pour le schéma.  
    ![Capture d’écran de sccount de stockage, avec un accès partagé signatures onglet mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. Dans **Ajouter un schéma**, sélectionnez **fichier volumineux**et fournir la signature d’un accès partagé URI dans la zone de texte **URI contenu** .  
    ![Capture d’écran des schémas, avec le bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. Dans la cuillère **schémas** du compte de l’intégration EIP, normalement, le schéma nouvellement ajouté.  
![Capture d’écran de EIP intégration compte, avec « Schémas » et le nouveau schéma mis en évidence](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Modifier des schémas
1. Sélectionnez la vignette **schémas** .  
2. Sélectionnez le schéma que vous souhaitez modifier dans la carte de **schémas** qui s’ouvre.
3. Dans la carte **schémas** , sélectionnez **Modifier**.  
![Carte de capture d’écran des schémas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Sélectionnez le fichier de schéma que vous voulez modifier à l’aide de la boîte de dialogue de sélecteur de fichier qui s’ouvre.
5. Sélectionnez **Ouvrir** dans le sélecteur de fichier.  
![Capture d’écran du sélecteur de fichier](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Vous recevez une notification indiquant que le téléchargement a réussi.  

## <a name="delete-schemas"></a>Supprimer des schémas
1. Sélectionnez la vignette **schémas** .  
2. Sélectionnez le schéma que vous voulez supprimer de la carte de **schémas** qui s’ouvre.  
3. Dans la carte de **schémas** , cliquez sur **Supprimer**.
![Carte de capture d’écran des schémas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Pour confirmer votre choix, sélectionnez **Oui**.  
![Capture d’écran de message de confirmation « Supprimer schéma »](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Enfin, vous pouvez remarquer que la liste des schémas dans la carte de **schémas** actualise, le schéma que vous avez supprimée n’apparaît plus.  
![Capture d’écran de EIP intégration compte « Schémas » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le Pack de l’intégration d’entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le pack de l’intégration d’entreprise").  

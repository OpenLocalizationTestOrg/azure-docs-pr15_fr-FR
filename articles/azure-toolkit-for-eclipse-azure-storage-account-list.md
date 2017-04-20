<properties
    pageTitle="Liste des comptes de stockage Azure"
    description="Gérer les paramètres de votre compte de stockage à l’aide de la boîte à outils Azure pour Éclipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Liste des comptes de stockage Azure #

Comptes de stockage Azure activer des emplacements de téléchargement à utiliser pour votre JDK, serveur d’applications et composants arbitraires, ainsi que pour le stockage d’état lorsque vous utilisez la mise en cache. Éclipse conserve une liste des comptes de stockage connus qui sont disponibles pour vos projets dans votre espace de travail Eclipse. Pour ouvrir la boîte de dialogue **Comptes de stockage** , qui est utilisé pour gérer cette liste, au sein de Eclipse, cliquez sur **fenêtre**, cliquez sur **Préférences**, développez **Azure**, puis cliquez sur **Comptes de stockage**.

La figure suivante montre la boîte de dialogue **Comptes de stockage** .

![][ic719496]

Vous pouvez également ouvrir cette boîte de dialogue à partir d’un lien de **comptes** dans les boîtes de dialogue qui utilisent des comptes de stockage, tels que les éléments suivants :

* L’onglet **JDK** de la boîte de dialogue **Configuration du serveur** .
* L’onglet **serveur** de la boîte de dialogue **Configuration du serveur** .
* La boîte de dialogue **Ajouter un composant** .
* La boîte de dialogue Propriétés de **la mise en cache** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Pour importer vos comptes de stockage à l’aide d’un fichier de paramètres de publication ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur **Importer depuis un fichier de paramètres de publication**.
2. (Ignorer cette étape si vous avez déjà enregistré un fichier de paramètres de publication sur votre ordinateur local). Dans la boîte de dialogue **Importer des informations abonnement** , cliquez sur **Télécharger le fichier paramètres de publication**. Si vous n’êtes pas encore connecté à votre compte Azure, vous invitera à se connecter. Puis vous serez invité à enregistrer une Azure publier le fichier de configuration. (Vous pouvez ignorer les instructions qui en résulte indiquées dans les pages d’ouverture de session - ils sont fournis par le portail Azure et sont destinées aux utilisateurs de Visual Studio.) L’enregistrer sur votre ordinateur local.
3. Toujours dans la boîte de dialogue **Importer des informations abonnement** , cliquez sur le bouton **Parcourir** , sélectionnez le fichier de paramètres de publication que vous avez enregistré localement précédemment, puis cliquez sur **Ouvrir**.
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Importer des informations abonnement** .

## <a name="to-create-a-new-storage-account"></a>Pour créer un nouveau compte de stockage ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur **Ajouter**.
2. Dans la boîte de dialogue **Ajouter un compte de stockage** , cliquez sur **Nouveau**.
3. Dans la boîte de dialogue **Nouveau compte de stockage** , spécifier des valeurs pour les éléments suivants :
    * Nom du compte de stockage.
    * Emplacement du compte de stockage.
    * Description du compte de stockage.
    * L’abonnement auquel appartient le compte de stockage.
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Nouveau compte de stockage** .

Il peut prendre plusieurs minutes pour votre compte de stockage à créer. Après sa création, cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un compte de stockage** , et votre nouveau compte de stockage est ajouté à la liste des comptes de stockage disponible.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Pour ajouter un compte de stockage existant dans la liste ##

1. Si vous n’avez pas déjà un compte de stockage Azure, créez-le en suivant la procédure décrite dans la section **pour créer une nouvelle section de compte de stockage** ci-dessus. (Vous pouvez également créer un nouveau compte de stockage sur le [Portail de gestion Azure][].)
2. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur **Ajouter**.
3. Dans la boîte de dialogue **Ajouter un compte de stockage** , entrez des valeurs pour **nom** et **Touche d’accès rapide**. La touche d’accès et le nom de compte doit être d’un compte de stockage Azure existant. Utilisez la section de **stockage** du [Portail de gestion Azure][] pour afficher votre nom de compte de stockage et les clés. Votre boîte de dialogue **Ajouter un compte de stockage** est semblable à ce qui suit.

    ![][ic719497]

4. Cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un compte de stockage** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Pour modifier un compte de stockage pour utiliser une nouvelle touche d’accès rapide ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur le compte de stockage que vous souhaitez modifier, puis cliquez sur **Modifier**.
2. Dans la boîte de dialogue **Modifier stockage compte touche d’accès rapide** , modifiez la valeur de **Touche d’accès rapide** .
3. Cliquez sur **OK** pour fermer la boîte de dialogue **Modifier stockage compte touche d’accès rapide** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Pour supprimer un compte de stockage de la liste conservée dans Éclipse ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur le compte de stockage que vous souhaitez modifier, puis cliquez sur **Supprimer**.
2. Cliquez sur **OK** lorsque vous êtes invité à supprimer le compte de stockage.

>[AZURE.NOTE] Suppression du compte de stockage via la boîte de dialogue **Comptes de stockage** supprime uniquement dans la liste des comptes de stockage affichables dans Eclipse. Il ne supprime pas le compte de stockage de votre abonnement Azure. En outre, le compte de stockage peut apparaître à nouveau dans votre liste après que Eclipse recharge les détails de votre abonnement.

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

[Création d’une Application World Hello pour Azure dans Éclipse][]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<properties
    pageTitle="Affichage du contenu Javadoc dans Eclipse pour le Package de bibliothèques Azure pour Java"
    description="Comment afficher le contenu Javadoc pour les bibliothèques Azure dans Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Affichage du contenu Javadoc dans Eclipse pour le Package de bibliothèques Azure pour Java #

Le contenu Javadoc pour les bibliothèques Azure pour Java peut être affiché au sein de votre environnement Eclipse en associant le contenu Javadoc aux bibliothèques Azure pour Java. Les étapes suivantes vous montrent comment utiliser cette fonctionnalité dans Eclipse.

Cette procédure suppose que vous avez déjà ajouté la bibliothèque Azure pour Java à votre chemin d’accès de génération.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Pour afficher le contenu Javadoc dans Eclipse pour les bibliothèques Azure pour Java ##

* Au sein de l’Explorateur de projets de Eclipse, dans la section **Bibliothèques référencé** de votre projet, ouvrez le menu contextuel pour la bibliothèque Azure pour Java JAR. Par exemple, **microsoft-windowsazure-api-0.1.0.jar** (le numéro de version peut être différent et dépendant de la version que vous avez installé).
* Cliquez sur **Propriétés**.
* Dans la boîte de dialogue **Propriétés** , dans le volet gauche, cliquez sur **Emplacement Javadoc**. La boîte de dialogue **Emplacement Javadoc** s’affiche.
* Vous pouvez spécifier une **URL Javadoc**ou un **Javadoc dans archive**.
    * Si vous choisissez de spécifier une **URL Javadoc**, utilisez les URL tel que **http://dl.windowsazure.com/javadoc** ou **http://dl.windowsazure.com/storage/javadoc**.
    * Si vous choisissez d’utiliser **Javadoc dans archive**, vous pouvez spécifier un fichier externe ou à un fichier d’espace de travail.
    Faites votre choix et parcourir/valider selon vos besoins. L’exemple suivant associe les bibliothèques Azure pour Java à la JAR de Javadoc correspondantes qui a été téléchargé localement dans un dossier nommé **c:\MyAzureJARs**.
    ![][ic553487]
* *Facultatif*: cliquez sur **Valider**. Problèmes potentiels liés à la Javadoc JAR peuvent être affichés ici.
* Cliquez sur **OK**.

Une fois associé à la bibliothèque, le contenu Javadoc doit afficher votre IDE Eclipse. Par exemple, si `blob` est défini de type `CloudBlockBlob` au sein de votre code, Voici un exemple de contenu Javadoc qui s’affiche lorsque vous tapez `blob.acquireLease` dans le code :

![][ic553488]

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Création d’une Application World Hello pour Azure dans Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png
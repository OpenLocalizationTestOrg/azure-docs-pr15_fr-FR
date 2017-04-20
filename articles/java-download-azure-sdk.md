<properties 
    pageTitle="Téléchargez le Kit de développement Azure pour Java" 
    description="Découvrez comment télécharger le Kit de développement Azure pour Java, par exemple de code fourni pour Maven projets et des étapes d’installation de base pour le Kit de ressources Azure pour Eclipse." 
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

# <a name="download-the-azure-sdk-for-java"></a>Téléchargez le Kit de développement Azure pour Java

Cet article contient des instructions pour télécharger et installer les bibliothèques Azure pour Java.

**Remarque :** Les bibliothèques Azure pour Java sont distribuées sous [licence Apache, Version 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Bibliothèques Azure Java - téléchargement manuel

Pour installer manuellement les bibliothèques Azure pour Java, cliquez sur <http://go.microsoft.com/fwlink/?LinkId=690320> pour télécharger un fichier ZIP qui contient toutes les bibliothèques et toutes les dépendances.

Une fois que vous avez téléchargé le fichier zip sur votre ordinateur, extraire le contenu et utilisez une des options suivantes pour ajouter les fichiers JAR à votre projet :

* Importer les fichiers JAR dans votre projet Java dans Eclipse.

* Configurer le **Chemin d’accès créer** pour votre projet Java dans Eclipse pour inclure le chemin d’accès aux fichiers JAR.

Pour plus d’informations sur la configuration des chemins d’accès de build dans Eclipse, voir l’article [Le chemin d’accès de Java créer] sur le site Web Éclipse.

**Remarque :** Voir les License.txt et le fichier ThirdPartyNotices.txt à l’intérieur de la ZIP de licence et d’autres informations.

## <a name="azure-libraries-for-java---building-with-maven"></a>Bibliothèques Azure Java - création avec Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Étape 1 : configurer votre projet à utiliser Maven pour génération

Pour créer des projets de Maven dans Eclipse qui utilisent les bibliothèques Azure pour Java, suivant les instructions de la [Prise en main Azure Gestion des bibliothèques pour Java] [ maven-getting-started] article. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Étape 2 : configurer les paramètres Maven avec les dépendances requises

Une fois que votre projet a été configuré pour utiliser Maven pour générer, vous pouvez ajouter la les dépendances requises pour votre fichier pom.xml à l’aide de syntaxe comme l’exemple suivant. Notez que vous n’avez pas besoin d’ajouter chaque dépendance est répertorié dans l’exemple suivant ; Vous devez uniquement ajouter les dépendances spécifiques qui nécessite de votre projet.

> [AZURE.NOTE] Au sein de chaque `<version>` élément dans l’exemple suivant, remplacez les espaces réservés « n.n.n » dans cet exemple de numéros de version valides, qui peuvent être obtenus à partir du [Référentiel de bibliothèques Azure Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Installez le Kit de ressources de Azure pour Éclipse

Cette section contient des instructions de base pour l’installation de la boîte à outils Azure pour Eclipse ; Pour obtenir des instructions détaillées, voir [la boîte à outils Azure pour Eclipse].

### <a name="prerequisites"></a>Conditions préalables

1. Systèmes d’exploitation Windows répertoriés dans l’article [What's New dans la boîte à outils Azure pour Eclipse] .
1. Systèmes d’exploitation des Macintosh ou Linux répertoriées dans l’article [What's New dans la boîte à outils Azure pour Eclipse] .
1. Eclipse IDE pour les développeurs EE Java, Indigo ou version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Procédure d’Installation

1. Dans Eclipse, dans le menu **aide** , sélectionnez **Installer de nouveaux logiciels**.
1. Entrez l' emplacement de site <http://dl.microsoft.com/eclipse> et appuyez sur **entrée**.
1. Sélectionnez les éléments à installer et cliquez sur **Terminer**.

La boîte à outils Azure pour Eclipse utilise la dernière version du Kit de développement Azure. Cela peut être téléchargé à l’aide de Web Platform Installer (WebPI) à <http://go.microsoft.com/fwlink/?LinkID=252838>. Toutefois, si vous n’avez pas installé, lorsque vous créez votre premier projet de déploiement d’Azure, le Kit de ressources Azure pour Eclipse installe automatiquement la version appropriée du Kit de développement Azure.

## <a name="see-also"></a>Voir aussi

[Kit de ressources Azure pour Éclipse]

[Installez le Kit de ressources de Azure pour Éclipse] 

[Création d’une Application World Hello pour Azure dans Éclipse]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Référentiel de bibliothèques Azure Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Chemin de génération Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=690333

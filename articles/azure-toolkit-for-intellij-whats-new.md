<properties
    pageTitle="Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ | Microsoft Azure"
    description="En savoir plus sur les dernières fonctionnalités dans la boîte à outils Azure pour IntelliJ."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Azure Kit de ressources pour les versions IntelliJ

Cet article contient des informations sur les différentes versions et les dernières mises à jour à la boîte à outils Azure pour IntelliJ.

> [AZURE.NOTE] Vous trouverez également un jeu d’outils Azure pour l’Eclipse de macros. Pour plus d’informations, voir le [Kit de ressources Azure pour Eclipse].

### <a name="august-26-2016"></a>26 août 2016

La boîte à outils Azure pour IntelliJ - release août 2016 inclut les améliorations suivantes :

* **Répartitions JDK personnalisé**. La boîte à outils Azure pour IntelliJ prend désormais en charge spécifiant et déployer une version JDK arbitraire votre conteneur WebApp Azure :
  - Outre les kits JDK fournies par Azure, vous pouvez également choisir parmi une large sélection de versions OpenJDK Zoulou mis à disposition sur Azure par Azul systèmes.
  - Vous pouvez également spécifier vos propres distribution JDK si vous téléchargez un fichier ZIP à votre compte de stockage.
* **Améliorations apportées à l’affichage de l’Explorateur Azure**:
  - Prise en charge de la gestion des machines virtuelles à l’aide du nouveau modèle de gestionnaire de ressources de Azure : vous pouvez répertorier, créer et supprimer des machines virtuelles ressource Gestionnaire sans quitter l’IDE.
  - Prise en charge pour la gestion des objets blob compte de stockage à l’aide du Gestionnaire de ressources de Azure qui complète les fonctionnalités existantes pour la gestion des comptes de stockage « classique ».
* **Pilote de Microsoft JDBC 6.0 pour SQL Server**. Cette mise à jour inclut le pilote JDBC plus récent pour Microsoft SQL Server (version 6.0), qui est à présent inclus dans une bibliothèque que vous pouvez ajouter facilement à vos projets Java, et remplacement de l’ancienne version.

### <a name="june-29-2016"></a>29 juin 2016

La boîte à outils Azure pour IntelliJ - release juin 2016 inclut les améliorations suivantes :

* **Configuration minimale requise Java 8**. La boîte à outils Azure pour IntelliJ nécessite maintenant Java 8, même si cette condition est uniquement pour le jeu d’outils - vos applications peuvent continuer à utiliser toutes les versions de Java sont pris en charge par Azure.
* **Prise en charge pour les kits JDK Java plus récente**. Les dernières versions de la kits JDK Java sont désormais pris en charge par le Kit de ressources de Azure pour IntelliJ.
* **Prise en charge pour Azure SDK v2.9.1**. La dernière version du Kit de développement Azure devient la configuration minimale requise pour le Kit de ressources Azure pour IntelliJ.
* **Exemples intégrées**. La boîte à outils Azure pour IntelliJ comporte désormais plusieurs exemples d’applications pour aider les développeurs prise en main.
* **Intégration d’outils HDInsight**. HDInsight outils d’Azure sont désormais fournis avec le Kit de ressources de Azure pour IntelliJ. Pour plus d’informations, voir [HDInsight IntelliJ plug-in outils].
* **Débogage des applications Web de Java à distance**. La boîte à outils Azure pour IntelliJ prend désormais en charge le débogage distant de Java web apps sur Azure Application Service.

### <a name="april-12-2016"></a>12 avril 2016

La boîte à outils Azure pour IntelliJ - release 2016 d’avril inclut les améliorations suivantes :

* **Prise en charge pour Azure SDK v2.9.0**. La dernière version du Kit de développement Azure devient la configuration minimale requise pour le Kit de ressources Azure pour IntelliJ.
* **Facilité d’utilisation divers, des performances et la réactivité améliorations relatives au support Azure Web App**. Un nombre de l’optimisation des performances dans la façon dont la boîte à outils communique avec résultat Azure dans une interface utilisateur plus réactive.
* **Possibilité de supprimer un conteneur Application Web existant dans Azure à partir de IntelliJ**. La boîte à outils Azure pour IntelliJ maintenant vous permet de supprimer un conteneur Azure Web existant sans quitter IntelliJ.

## <a name="see-also"></a>Voir aussi ##

Pour plus d’informations sur les boîtes à outils Azure pour Java IDEs, consultez les liens suivants :

- [Kit de ressources Azure pour Éclipse]
  - [Installez le Kit de ressources de Azure pour Éclipse]
  - [Créer une application Hello World Web pour Azure dans Éclipse]
  - [Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]
- [Kit de ressources Azure pour IntelliJ]
  - [Installez le Kit de ressources de Azure pour IntelliJ]
  - [Créer une application Hello World Web pour Azure dans IntelliJ]
  - *Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ (Cet Article)*

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

<!-- URL List -->

[Kit de ressources Azure pour Éclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application Hello World Web pour Azure dans Éclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application Hello World Web pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installez le Kit de ressources de Azure pour Éclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installez le Kit de ressources de Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in des outils HDInsight pour IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md

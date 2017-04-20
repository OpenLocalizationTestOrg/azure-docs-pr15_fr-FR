<properties
    pageTitle="Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse"
    description="En savoir plus sur les dernières fonctionnalités dans la boîte à outils Azure pour Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse

## <a name="azure-toolkit-for-eclipse-releases"></a>Azure Kit de ressources pour les versions Éclipse

Cet article contient des informations sur les différentes versions et les dernières mises à jour à la boîte à outils Azure pour Eclipse.

> [AZURE.NOTE] Vous trouverez également un jeu d’outils Azure pour IntelliJ IDE. Pour plus d’informations, voir [Le Kit de ressources de Azure pour IntelliJ].

### <a name="august-26-2016"></a>26 août 2016

La boîte à outils Azure pour Eclipse - release août 2016 inclut les améliorations suivantes :

* **Répartitions JDK personnalisé**. La boîte à outils Azure pour Eclipse prend désormais en charge spécifiant et déployer une version JDK arbitraire votre conteneur WebApp Azure :
  - Outre les kits JDK fournies par Azure, vous pouvez également choisir parmi une large sélection de versions OpenJDK Zoulou mis à disposition sur Azure par Azul systèmes.
  - Vous pouvez également spécifier vos propres distribution JDK si vous téléchargez un fichier ZIP à votre compte de stockage.
* **Améliorations apportées à l’affichage de l’Explorateur Azure**:
  - Prise en charge de la gestion des machines virtuelles à l’aide du nouveau modèle de gestionnaire de ressources de Azure : vous pouvez répertorier, créer et supprimer des machines virtuelles ressource Gestionnaire sans quitter l’IDE.
  - Prise en charge pour la gestion des objets blob compte de stockage à l’aide du Gestionnaire de ressources de Azure qui complète les fonctionnalités existantes pour la gestion des comptes de stockage « classique ».
* **Pilote de Microsoft JDBC 6.0 pour SQL Server**. Cette mise à jour inclut le pilote JDBC plus récent pour Microsoft SQL Server (version 6.0), qui est à présent inclus dans une bibliothèque que vous pouvez ajouter facilement à vos projets Java, et remplacement de l’ancienne version.

### <a name="june-29-2016"></a>29 juin 2016

La boîte à outils Azure pour Eclipse - release juin 2016 inclut les améliorations suivantes :

* **Configuration minimale requise Java 8**. La boîte à outils Azure pour Eclipse requiert désormais Java 8, bien que cette exigence est uniquement pour le jeu d’outils - vos applications peuvent continuer à utiliser toutes les versions de Java sont pris en charge par Azure.
* **Prise en charge pour les kits JDK Java plus récente**. Les dernières versions de la kits JDK Java sont désormais pris en charge par le Kit de ressources Azure pour Eclipse.
* **Prise en charge pour Azure SDK v2.9.1**. La dernière version du Kit de développement Azure devient la condition minimale requise pour le Kit de ressources Azure pour Eclipse.
* **Exemples intégrées**. La boîte à outils Azure pour Eclipse comporte désormais plusieurs exemples d’applications pour aider les développeurs prise en main.
* **Intégration d’outils HDInsight**. HDInsight outils d’Azure sont désormais fournis avec le Kit de ressources Azure pour Eclipse. Pour plus d’informations, voir [HDInsight Eclipse plug-in outils].
* **Débogage des applications Web de Java à distance**. La boîte à outils Azure pour Eclipse prend désormais en charge le débogage distant de Java web apps sur Azure Application Service.
* **Prise en charge pour la version Luna Eclipse.** La nouvelle version minimale requise Eclipse IDE est Luna.

### <a name="april-12-2016"></a>12 avril 2016

La boîte à outils Azure pour Eclipse - release 2016 d’avril inclut les améliorations suivantes :

* **Prise en charge pour Azure SDK v2.9.0**. La dernière version du Kit de développement Azure devient la condition minimale requise pour le Kit de ressources Azure pour Eclipse.
* **Facilité d’utilisation divers, des performances et la réactivité améliorations relatives au support Azure Web App**. Un nombre de l’optimisation des performances dans la façon dont la boîte à outils communique avec résultat Azure dans une interface utilisateur plus réactive.
* **Possibilité de supprimer un conteneur Application Web existant dans Azure à partir d’Eclipse**. Le Kit de ressources Azure pour Eclipse maintenant vous permet de supprimer un conteneur Azure Web existant sans quitter Eclipse.

### <a name="march-7-2016"></a>7 mars 2016

La boîte à outils Azure pour Eclipse - release mars 2016 inclut les améliorations suivantes :

* **Prise en charge pour le déploiement rapide des applications Java légères**. La boîte à outils Azure pour Eclipse prend désormais en charge le déploiement rapide des applications Java légères dans Azure des conteneurs de l’application Web, afin de déploiement d’applications Java maintenant prend secondes place des minutes.
* **Prise en charge pour la gestion Web App à l’aide de l’affichage de l’Explorateur Azure**. L’affichage de l’Explorateur Azure dans la boîte à outils prend désormais en charge de liste, de démarrage et d’arrêt Azure Web Apps.
* **Répartitions Tomcat mis à jour, jetée et Zoulou OpenJDK**. La boîte à outils Azure pour Eclipse prend en charge les versions mises à jour de Tomcat, jetée et OpenJDK Zoulou pour les déploiements Java dans les services de cloud Azure.

### <a name="january-4-2016"></a>4 janvier 2016

La boîte à outils Azure pour Eclipse - release janvier 2016 inclut les améliorations suivantes :

* **Prise en charge pour les mises à jour Zoulou OpenJDK**. Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Tomcat mis à jour et répartitions jetée**. Les répartitions jetée et Tomcat qui sont disponibles sur Microsoft Azure à utiliser avec la boîte à outils Azure pour Eclipse ont été mis à jour.
* **Fonctionnalité disparités qui existent entre Eclipse et d’outils IntelliJ pour Azure**. La boîte à outils Azure pour Eclipse et le [Jeu d’outils Azure pour IntelliJ] prennent désormais en charge le même ensemble de fonctionnalités.

### <a name="september-1-2015"></a>1er septembre 2015.

La boîte à outils Azure pour Eclipse - release septembre 2015 inclut les améliorations suivantes :

* **Prise en charge pour les mises à jour Zoulou OpenJDK**. Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Tomcat mis à jour et répartitions jetée**. Les répartitions jetée et Tomcat qui sont disponibles sur Microsoft Azure à utiliser avec la boîte à outils Azure pour Eclipse ont été mis à jour. (Ces répartitions permet aux développeurs de créer développement rapide et tester des projets avec la boîte à outils Azure pour Eclipse.
* **Prise en charge des références de Tomcat et jetée automatiquement mises à jour**. Outre les versions spécifiques de Tomcat et jetée qui sont disponibles sur Azure, les développeurs peuvent maintenant référencer une distribution appelée la « dernière (mise à jour automatique) », qui met automatiquement à jour la dernière distribution de chaque version majeure du jetée ou Tomcat la prochaine fois que vos instances de rôles sont suppression initiale. (Recyclage s’effectue automatiquement, mais les développeurs peuvent déclencher manuellement une Corbeille à partir du portail Azure). Cette nouvelle fonctionnalité signifie que les développeurs n’ont pas à redéployez son application pour permettre aux leur logiciel de serveur de mise à jour. (
*  Cette fonctionnalité est actuellement destinée uniquement à des fins de test et de développement ou applications non critiques et n’est pas recommandée de production.)
* **Mode Explorateur azure BLOB, les files d’attente et les tables dans le stockage Azure**. Cela permet aux développeurs d’effectuer un ensemble de tâches courantes avec leurs objets de stockage directement à partir de l’IDE Eclipse. Par exemple : suppression, chargement ou le téléchargement des objets BLOB.

### <a name="august-1-2015"></a>1er août 2015.

La boîte à outils Azure pour Eclipse - août 2015 version inclut les améliorations suivantes :

* **Gestion des applications Insights Instrumentation clés**. Cette mise à jour vous permet de trouver, créer et gérer vos clés d’instrumentation Insights Application directement à partir de l’IDE Eclipse.
* **Pilote de Microsoft JDBC 4.1 pour SQL Server**. Cette mise à jour prend en charge le pilote JDBC plus récent pour Microsoft SQL Server.
* **Version2.7 du Kit de développement Azure**. Cette dernière mise à jour pour le Kit de développement Azure est un nouveau préalable pour le Kit de ressources installé sur Windows. (Remarque : si que ce n’est pas nécessaire sur les systèmes d’exploitation non Windows).
* **Prise en charge pour les versions 7 Zoulou OpenJDK mettre à jour**. Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].

### <a name="may-1-2015"></a>1er mai 2015.

La boîte à outils Azure pour Eclipse - mai 2015 version inclut les améliorations suivantes :

* **Meilleure interface utilisateur de sélection de serveur**. Cette version simplifie l’utilisation du Kit de ressources sur les systèmes d’exploitation non Windows.
* **Prise en charge pour les projets Maven**. Cette version prend en charge les projets Maven en tant qu’applications, la boîte à outils pouvez déployer sur Azure et configurer l’analyse de l’Application.
* **Version 2.6 du Kit de développement Azure**. Cette dernière mise à jour pour le Kit de développement Azure est un nouveau préalable pour le Kit de ressources installé sur Windows. (Remarque : si que ce n’est pas nécessaire sur les systèmes d’exploitation non Windows).
* **Mise à niveau du déploiement au lieu de republier**. Si vous republiez un projet de déploiement lors de la version précédente est déjà publiée, le Kit de ressources utilise désormais fonctionnalité de mise à niveau de déploiement d’Azure au lieu de devoir arrêter le déploiement précédent et republiez à partir de zéro, comme dans le passé. Cela permet à votre service cloud de s’exécuter sans interruption dès que possible, aider à disponibilité élevée même pendant une mise à jour et accélérer le processus de nouvelle publication.
* **Prise en charge pour la dernière version de 8 Zoulou OpenJDK - 40 mise à jour**. Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].

### <a name="march-9-2015"></a>9 mars 2015

La boîte à outils Azure pour Eclipse - mars 2015 version inclut les améliorations suivantes :

* **Prise en charge pour Mac, Ubuntu et peut être Linux supplémentaires**. Cette version du Kit de ressources Azure pour Eclipse prend en charge Mac OS et sur plusieurs plateformes Unix, afin que les développeurs peuvent installer le Kit de ressources pour créer, configurer et publier des projets Java aux Services Cloud Azure (PaaS) à partir d’Eclipse en cours d’exécution sur les systèmes d’exploitation différent de Windows.

>[AZURE.NOTE] Cette fonctionnalité est dans l’aperçu, et il n’est pas recommandé d’utiliser dans les environnements de production. Aucune prise en charge du client (contrat de niveau Service), mais tous les commentaires sont Merci et invités.

* **Plug-in de nouvelles perspectives d’Application**. Les développeurs sont désormais en mesure de configurer télémétrie serveur automatique à l’aide d’analyse de l’Application sur Azure.
* **Automatisation du déploiement ant basée sur une ligne de commande**. Cette fonctionnalité permet aux développeurs d’automatiser la publication des versions plus récentes de leurs déploiements à l’aide de Ant en dehors d’Eclipse. Un script déjà généré est configuré automatiquement pour un projet une fois que la première fois qu’il est déployé à partir de Eclipse et les déploiements suivants peuvent utiliser le script pour automatiser entièrement les déploiements via la ligne de commande uniquement.
* **Disponibilité tomcat et jetée sur Azure pour le déploiement plus simple et plus rapide**. Les développeurs peuvent maintenant référencer différentes versions Tomcat et jetée qui sont disponibles sur Azure directement à la place de devoir télécharger un serveur Java à leurs comptes (ou via le Kit de ressources), donc n’est pas nécessaire pour télécharger un serveur Java pour les scénarios de mise en route rapides.
* **Méthode rapide pour les applications web Java publication services cloud Azure**. Pour réduire la durée d’apprentissage pour les scénarios de développement et de test simples, les développeurs peuvent maintenant publier des applications Java plus directement Azure. Au lieu d’avoir à passer par le processus de création et de configuration d’un projet de déploiement d’Azure, applications seront déployées avec une instance par défaut de versions 8 Tomcat et Zoulou machine virtuelle Java (OpenJDK).

### <a name="january-30-2015"></a>30 janvier 2015

La boîte à outils Azure pour Eclipse - release janvier 2015 inclut les améliorations suivantes :

* **Prise en charge pour IBM® WebSphere® Application Server liberté Core**. Cette version ajoute IBM WebSphere Application Server liberté Core à la liste des serveurs d’applications pris en charge à partir de laquelle le Kit de ressources est en mesure de déployer vers Azure. Ce dernier ajout développe la liste actuelle des serveurs d’applications pris en charge &quot;de prédéfinies&quot; par le Kit de ressources, ce qui déjà inclus différentes versions de Tomcat, jetée, JBoss et GlassFish.
* **Inclusion d’Application Insights SDK**. Cette bibliothèque client nouvellement publiés API (v0.9.0) fait désormais partie du Package pour les bibliothèques Azure pour Java.
* **Mise à jour de Package pour les bibliothèques Azure pour Java**. Cette mise à jour inclut des bibliothèques Azure pour Java v0.7.0 et v2.0.0 de stockage Client API, ainsi que la v0.9.0 Application Insights SDK nouvellement publiés.

### <a name="november-12-2014"></a>12 novembre 2014

La boîte à outils Azure pour Eclipse - release novembre 2014 inclut les améliorations suivantes :

* **Prise en charge pour Azure SDK 2,5**. Cette dernière mise à jour pour le Kit de développement Azure est un nouveau préalable pour le Kit de ressources.
* **Prend en charge la version mise à jour des OpenJDK Zoulou 1.8, 1.7 et v1.6 packages**. Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Prise en charge pour les nouvelles tailles D Standard pour les services en nuage**, qui offrent est augmenté performances et les ressources de mémoire supplémentaire. Pour plus d’informations, voir [Machine virtuelle et les tailles de Service Cloud pour Azure].

### <a name="october-17-2014"></a>17 octobre 2014

La boîte à outils Azure pour Eclipse - release octobre 2014 inclut les améliorations suivantes :

* **Améliorations des performances de la publier sur des scénarios Cloud**. Chargement des informations sur l’abonnement est beaucoup plus rapide lorsque les utilisateurs disposent de plusieurs abonnements et les comptes de stockage.
* **Prise en charge de la version mise à jour du package v1.8 Zoulou OpenJDK**. Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Prise en charge de déconseiller les anciennes versions de 3e partie kits JDK**. Packages JDK déconseillées n’est plus apparaîtra dans le menu déroulant pour les nouveaux projets de déploiement. Projets existants références packages JDK déconseillées continuent à être en mesure de le faire pendant la période en cours, mais il est recommandé pour mettre à niveau ces projets pour s’appuient sur les dernières actualités.
* **Version mise à jour du Package pour bibliothèques Azure pour la bibliothèque d’API Java client**. Pour plus d’informations, voir l' [API de Client Microsoft Azure].
* **Correctifs.** Cette version contient un nombre de divers correctifs fondés sur les rapports sur les utilisateurs et le test.

### <a name="august-5-2014"></a>5 août 2014

La boîte à outils Azure pour Eclipse - août 2014 version inclut les améliorations suivantes

* **Prise en charge pour Azure SDK 2.4.** Les versions antérieures de la boîte à outils Eclipse ne fonctionnera pas avec ce SDK récents.
* **Versions de la v1.6 Zoulou OpenJDK, mises à jour des packages 1.7 et 1.8.** Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Version mise à jour du Package pour les bibliothèques Azure bibliothèque d’API Java client.** Pour plus d’informations, voir l' [API de Client Microsoft Azure].
* **Prise en charge de plus tard publier le Format de fichier de paramètres.** Prise en charge a été ajoutée pour la version 2.0 du format de fichier de paramètres de publication.
* **Modifications de l’architecture derrière la publier sur fonctionnalité Cloud.** La boîte à outils utilise désormais l’API de Client Microsoft Azure nouvellement finale pour Java pour la prise en charge publier-nuage.
* **Correctifs.** Cette version contient un certain nombre de correctifs demandées par l’utilisateur.

### <a name="june-12-2014"></a>12 juin 2014

La boîte à outils Azure pour Eclipse - juin 2014 release est une mise à jour maintenance mineure qui fournit les améliorations suivantes :

* **Prise en charge de la v1.8 package Zoulou OpenJDK.** Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Versions mises à jour de la v1.6 Zoulou OpenJDK et 1,7 packages.** Pour plus d’informations, consultez la [page web de systèmes Azul pour la OpenJDK Zoulou].
* **Version mise à jour du Package pour les bibliothèques Azure bibliothèque d’API Java client.** Pour plus d’informations, voir l' [API de Client Microsoft Azure].
* **Correctifs.** Cette version contient un certain nombre de correctifs demandées par l’utilisateur.

### <a name="april-4-2014"></a>4 avril 2014

Le plug-in Azure pour Eclipse - release avril 2014 a publié. Il s’agit d’une mise à jour qui accompagnent la version de 2.3 SDK Azure, qui est un préalable et sont téléchargés automatiquement lors de l’installation du plug-in. Cette mise à jour inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort dans la mesure où afficher un aperçu de la février 2014 :

* **Prise en charge pour la version Azure SDK 2.3.** Le plug-in Azure pour Eclipse - release avril 2014 nécessite Azure SDK 2.3. Lorsque vous utilisez le nouveau plug-in, si vous n’avez pas déjà Azure SDK 2.3, vous devrez autoriser son installation. N’utilisez pas Azure SDK 2.3 d’anciennes versions du plug-in.
* **La mise à niveau des applications sans déploiement du package terminée.** Lorsque vous déployez les applications Java qui font partie de votre projet, le plug-in maintenant télécharge automatiquement les dans votre compte de stockage sélectionné afin que vous pouvez mettre à jour et Corbeille instances de rôle pour déployer les dernières bits application sans avoir à recréer et redéployez la totalité du package.
* **Tomcat 8 est maintenant un serveur d’applications reconnu.** Si vous sélectionnez un répertoire d’installation Tomcat 8 sur votre ordinateur dans l’onglet **serveur** de la boîte de dialogue **Projet de déploiement d’Azure** , le plug-in sera maintenant automatiquement détecter et pouvoir déployer Tomcat 8 de manière automatique, même les anciennes versions de Tomcat déjà dans la liste.
* **Mises à jour du package azul Zoulou OpenJDK : 47 de mise à jour de la mise à jour de 1.7 51 et v1.6.** Efficaces avec cette version, mise à jour du système Azul JDK ouvrir Zoulou versions 7 package 51 est disponible. En outre, des packages JDK ouvrir Zoulou v6 commençaient à être disponibles, en commençant par la mise à jour 47. Ces mises à jour sont en plus du package versions 7 JDK ouvrir Zoulou était auparavant disponible 45 mettre à jour, mettre à jour de 40 et mettre à jour de 25.
* **Prise en charge de taille A8 et Machine virtuelle de A9 Microsoft Azure.** Vous pouvez désormais déployer un service cloud pour la mémoire haute A8 et les tailles de Machine virtuelle A9. Pour plus d’informations sur ces tailles machine virtuelle, voir [Machine virtuelle et les tailles de Service Cloud pour Azure].
* **Redirection automatique HTTP en HTTPS pour les rôles SSL activé.** Lorsque votre service cloud contient les rôles HTTPS uniquement, si la demande utilisateur spécifie HTTP, il vous redirige automatiquement vers HTTPS. Il n’est pas nécessaire pour créer un rôle distinct pour gérer les demandes HTTP.
* **Express émulateur utilisé pour émulation locale.** L’émulateur Express Azure est désormais utilisé comme émulateur lors du débogage de vos applications localement.
* **Azure a été renommé en tant que Microsoft Azure.** Écrans de l’interface utilisateur reflètent maintenant que Azure a été renommé et n’est plus appelé Azure.

### <a name="february-6-2014"></a>6 février 2014

Le plug-in Azure pour Eclipse - février 2014 Preview a publié. Cette mise à jour inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort dans la mesure où les octobre 2013 Preview :

* **Prise en charge pour décharger SSL.** Secure Sockets Layer (SSL) décharger a été ajouté en tant que fonctionnalité, ce qui vous permet d’activer facilement le support Hypertext Transfer Protocol sécurisé (HTTPS) dans votre déploiement Java sur Azure, sans avoir à configurer SSL dans votre serveur d’applications Java. Cela s’avère particulièrement utile dans Session affinité et/ou authentifiés scénarios de communication. Par exemple, lorsque vous utilisez le filtre de Service de contrôle d’accès (ACS), qui est déjà pris en charge par le Kit de ressources. Pour plus d’informations, voir [SSL décharger] et [comment utiliser SSL décharger].
* **GlassFish 4 est maintenant un serveur d’applications reconnu.** Si vous sélectionnez un répertoire d’installation GlassFish 4 sur votre ordinateur dans l’onglet **serveur** de la boîte de dialogue **Projet de déploiement d’Azure** , le plug-in sera désormais automatiquement détecter et pourrez déployer GlassFish extensions serveur Office 4 de manière automatique, similaire à la version GlassFish extensions serveur Office 3 déjà dans la liste.
* **Mise à jour de package azul Zoulou OpenJDK 45.** Efficaces avec cette version, mise à jour Zoulou (package de versions 7 JDK ouvrir) du système Azul 45 est désormais disponible ; Il s’agit en plus de la mise à jour était auparavant disponible 40 et mise à jour de 25.
* **Prend en charge pour « auto » privé ports de point de terminaison.** Vous pouvez définir un port privé sur automatique pour les points de terminaison d’entrée et points de terminaison internes pour permettre aux Azure attribuer automatiquement un port à ce point de terminaison. Précédemment que vous pouviez uniquement affecter un numéro de port spécifique.
* **Prise en charge pour personnaliser le nom du certificat (NC) dans l’interface utilisateur de création de certificat auto-signé.** Auparavant, le même nom codé en dur a été utilisé pour tous les nouveaux certificats ; Vous pouvez maintenant spécifier votre propre nom de certificat afin de faire la distinction entre plusieurs certificats dans le portail Azure utilisés à des fins différentes.
* **Azure barre d’outils :** La barre d’outils Azure comporte une mise à jour avec les modifications suivantes : 
    * ![][ic710876]Cette icône a été ajoutée pour le **Nouveau projet de déploiement d’Azure**.
    * ![][ic710877]Cette icône a été ajoutée comme un raccourci à la boîte de dialogue de création de certificat auto-signé.
* **Prise en charge de la taille de la Machine virtuelle de Azure A5.** Vous pouvez désormais déployer un service cloud vers la mémoire grande taille Machine virtuelle A5. Pour plus d’informations sur cette taille de la mémoire virtuelle, voir [Machine virtuelle et les tailles de Service Cloud pour Azure].
* **Prise en charge de Microsoft Windows Server 2012 R2.** Vous pouvez désormais sélectionner Windows Server 2012 R2 en tant que le système d’exploitation cloud.

### <a name="october-22-2013"></a>22 octobre 2013

Le plug-in Azure pour Eclipse - octobre 2013 Preview a publié. Cette mise à jour inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort dans la mesure où les septembre 2013 Preview :

* **Prise en charge pour la version Azure SDK 2.2.** Le plug-in Azure pour Eclipse - octobre 2013 Preview prend en charge Azure SDK 2.2. Le plug-in fonctionnera toujours avec Azure SDK 2.1 et installe automatiquement Azure SDK 2.2 si vous n’avez pas déjà au moins Azure SDK 2.1 est installé.
* **Mise à jour de package azul Zoulou OpenJDK 40.** Comme annoncé pour le septembre 2013 afficher un aperçu, plug-in maintenant permet à l’aide d’un JDK tiers communiquée directement sur Azure, sans avoir à télécharger vos propres JDK. Dans la version d’octobre 2013, Zoulou (package de versions 7 JDK ouvrir) mise à jour du système Azul 40 est désormais disponible ; Il s’agit en plus de la mise à jour initialement publié 25.
* **Lien vers le déploiement cloud dans le journal d’activité.** Dans le journal d’activité Azure lorsque votre déploiement a un statut **publié**, vous pouvez cliquer sur **Published** car il est désormais un lien vers votre déploiement ; votre déploiement puis s’ouvre dans votre navigateur. (L’état **publié** a été indiqué précédemment **en cours d’exécution**.)
* **Sélection de système d’exploitation cible disponible à publier heure.** La boîte de dialogue **publier sur Azure** contient un nouveau champ, **Cible du système d’exploitation**, qui offre un moyen plus détectable pour vous permet de définir votre système d’exploitation cible.
* **Remplacer automatique déploiement précédent.** La boîte de dialogue **publier sur Azure** contient une nouvelle case à cocher **Remplacer précédent déploiement**. Si cette option est activée, lorsque votre nouveau déploiement est publié elle remplacera automatiquement le déploiement précédent ; Vous ne voulez pas rencontrer &quot;409 conflit&quot; problèmes lors de la publication au même emplacement sans première l’annulation le déploiement précédent.
* **Jetée 9 est maintenant un serveur d’applications reconnu.** Si vous sélectionnez un répertoire d’installation jetée 9 sur votre ordinateur dans l’onglet **serveur** de la boîte de dialogue **Projet de déploiement d’Azure** , le plug-in sera maintenant automatiquement détecter et pouvoir déployer jetée 9 de manière automatique, même les anciennes versions de jetée déjà dans la liste.
* **Ajouter un rôle à partir du menu contextuel du projet.** Le menu contextuel du projet **Azure** contient à présent un nouvel élément de menu, **Ajouter un rôle**, qui fournit un moyen plus rapide et plus moyen détectable pour vous permet d’ajouter un nouveau rôle à votre projet Azure.
* **Une mise à jour vers le Package pour les bibliothèques Azure bibliothèque Java.** Ceci est basé sur la version 0.4.6 de l' [API de Client Microsoft Azure].

### <a name="september-25-2013"></a>25 septembre 2013

Le plug-in Azure pour Eclipse - septembre 2013 Preview a publié. Cette mise à jour inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort dans la mesure où l’août 2013 Preview :

* **Capacité à déployer le package Azul Zoulou OpenJDK disponible sur Azure.** Une nouvelle option a été ajoutée lors de la spécification JDK à utiliser avec votre déploiement d’Azure. Utilisez cette option, vous pouvez déployer un package JDK tiers directement sur le nuage Azure, sans avoir à télécharger vos propres. Systèmes azul fournit la première tel package Zoulou appelée, en fonction de la OpenJDK, qui peut désormais être déployé à l’aide de cette option.
* **Une mise à jour vers le Package pour les bibliothèques Azure bibliothèque Java.** Ceci est basé sur la version 0.4.5 de l' [API de Client Microsoft Azure].

### <a name="august-1-2013"></a>1er août 2013

Le plug-in Azure pour Eclipse - août 2013 Preview a publié. Il s’agit d’une mise à jour qui accompagnent la version de la 2.1 SDK Azure, qui est un préalable et sont téléchargés automatiquement lors de l’installation du plug-in. Cette mise à jour inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort dans la mesure où le juillet 2013 Preview :

* **Suppression des options pour inclure le JDK local et le serveur d’applications local dans le cadre du package de déploiement.** Le téléchargement JDK et serveur d’application à partir du stockage cloud au cours du déploiement est préférable pour l’incorporation de ces composants dans le package, depuis le téléchargement de la plus petite taille de package de déploiement, temps de déploiement plus rapides et faciliter la maintenance des résultats éléments. Par conséquent, les options à inclure JDK et serveur d’applications dans le package de déploiement ont été supprimés. Projets existants qui ont été configurés pour inclure les JDK local et le serveur d’applications local comme partie du package de déploiement est automatiquement convertie en téléchargement automatique JDK et serveur d’applications vers le stockage cloud.
* **Prise en charge pour la version 2.1 du Kit de développement logiciel Azure.** Le plug-in Azure pour Eclipse - août 2013 Preview nécessite Azure SDK 2.1. N’utilisez pas l’aperçu août 2013 avec des versions antérieures du Kit de développement Azure et n’utilisez pas Azure SDK 2.1 d’anciennes versions du plug-in Azure pour Eclipse.
* **Prise en charge pour la mise à jour Éclipse Kepler.** Liés à cette, la nouvelle version minimale requise Eclipse IDE Indigo. Le plug-in Azure pour Eclipse est testé n’est plus embauche sur Helios.

### <a name="july-3-2013"></a>3 juillet 2013

Le plug-in Azure pour Eclipse - juillet 2013 Preview a publié. Cette mise à jour inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort dans la mesure où les mai 2013 Preview :

* **Possibilité de créer un nouveau compte de stockage.** Un bouton **Nouveau** a été ajouté à la boîte de dialogue **Ajouter un compte de stockage** . Cela vous permet de créer un compte de stockage dans le plug-in Eclipse, sans avoir à se connecter au portail de gestion Azure. (Vous devez déjà posséder un abonnement Azure pour utiliser cette fonctionnalité). Pour plus d’informations sur la création d’un nouveau compte de stockage, voir [créer un nouveau compte de stockage].
* **Nouvelle &quot;(automatique)&quot; option compte de stockage utilisé pour le déploiement automatique du JDK et du serveur et de mise en cache.** Lorsque vous utilisez l’option **charger automatiquement** pour JDK et serveur d’applications, vous pouvez à présent spécifier **(automatique)** pour le compte de stockage et URL à utiliser lors du chargement de JDK et serveur d’applications, ou lorsque vous utilisez la mise en cache Azure. Ensuite, ces fonctionnalités utilise automatiquement le même compte de stockage que celui que vous sélectionnez dans la boîte de dialogue **publier sur Azure** . Le didacticiel de [Création d’une Application Hello World pour Azure dans Eclipse] a été mis à jour pour utiliser la nouvelle option **(automatique)** .
* **Possibilité de définir vos points de terminaison du service Azure.** Spécifier les points de terminaison de service qui déterminent que si votre application est déployée sur et gérée par la plateforme Azure globale, Azure géré par 21Vianet en Chine, ou un privé plateforme Azure. Pour plus d’informations, voir [Points de terminaison de Service Azure].
* **Des déploiements peuvent spécifier une ressource de stockage local.** Dans le cas où votre déploiement est trop volumineux pour être inclus dans le dossier approot par défaut, vous pouvez à présent spécifier une ressource de stockage local en tant que la destination du déploiement pour votre JDK et serveur d’applications. Pour plus d’informations, voir [Déployer des déploiements].
* **Prise en charge de tailles A6 et A7 Azure Virtual Machine.** Vous pouvez désormais déployer un service cloud à la mémoire haute A6 et A7 Virtual Machine tailles. Pour plus d’informations sur ces tailles, voir [Machine virtuelle et les tailles de Service Cloud pour Azure].
* **Une mise à jour vers le Package pour les bibliothèques Azure bibliothèque Java.** Ceci est basé sur la version 0.4.4 de l' [API de Client Microsoft Azure].

### <a name="may-1-2013"></a>1er mai 2013

Le plug-in Azure pour Eclipse - peut 2013 Preview a publié. Il s’agit d’une mise à jour principal qui accompagnent la version de Azure SDK 2.0, qui est un préalable et sont téléchargés automatiquement lors de l’installation du plug-in. Cette version inclut les nouvelles fonctionnalités, correctifs et des améliorations ainsi que de facilité d’utilisation des commentaires par l’effort depuis la février 2013 Preview :

* **Téléchargement automatique des JDK et serveur d’applications et déploiement du stockage Azure.** Une nouvelle option qui télécharge le JDK sélectionné et le serveur d’applications, si nécessaire, à un compte de stockage Azure spécifié automatiquement et déploie ces composants à partir de là, au lieu de l’incorporation dans le package de déploiement ou ayant le téléchargement d’utilisateur puis manuellement. Cette fonctionnalité couramment demandée peut améliorer considérablement la facilité de déploiement des composants JDK et le serveur, en particulier pour les utilisateurs débutants. Pour une vue d’ensemble qui utilise ces options, voir [Création d’une Application Hello World pour Azure dans Eclipse].
* **Possibilité de comptes de stockage référence plus facilement (via un contrôle de liste déroulante) et le suivi du compte de stockage centralisé.** Cela s’applique à plusieurs fonctionnalités qui s’appuient sur le stockage, tels que JDK et déploiement d’un composant serveur et la mise en cache. Pour plus d’informations, voir la [Liste compte de stockage Azure].
* **Installation d’accès à distance simplifiée dans l’Assistant Cloud publier sur.** Il vous souhaitez est tapez un nom d’utilisateur et mot de passe pour activer l’accès à distance ou laissez ce champ vide pour conserver l’accès à distance est désactivé.
* **Une mise à jour vers le Package pour les bibliothèques Azure bibliothèque Java.** Ceci est basé sur la version 0.4.2 de l' [API de Client Microsoft Azure].
* **Prise en charge pour les sessions pense-bête sur Windows Server 2012.** Sessions permanentes travaillait précédemment, uniquement sur Windows Server 2008 R2, à présent deux cloud affinité de session du système d’exploitation cibles prise en charge.
* **Amélioration des performances package téléchargement.** Même lorsque le JDK et serveur d’applications sont incorporées dans le package de déploiement, la partie de téléchargement du processus de déploiement peut être environ deux fois plus rapide par rapport aux versions précédentes.

### <a name="february-8-2013"></a>8 février 2013

Le plug-in Azure pour Eclipse - février 2013 Preview a publié. Il s’agit d’une mise à jour mineur qui inclut les correctifs et améliorations commentaires par l’effort quelques-unes des nouvelles fonctionnalités dans la mesure où afficher un aperçu de novembre 2012 :

* Prise en charge pour le déploiement kits JDK, serveurs d’applications et arbitraire autres composants public ou privé Azure blob téléchargements de stockage au lieu de les inclure dans le package de déploiement lorsque vous déployez dans le cloud.
* Possibilité de modifier l’ordre dans lequel les composants définies par l’utilisateur d’un rôle sont traités, grâce à l’ajout de boutons **Monter** et **Descendre** dans la section **composants** des **Propriétés du rôle Azure**.
* Une mise à jour à la bibliothèque de **Package pour les bibliothèques Azure pour Java** , en fonction de la version 0.4.0 de l' [API de Client Microsoft Azure].

### <a name="november-5-2012"></a>5 novembre 2012

Le plug-in Azure pour Eclipse - novembre 2012 Preview a publié. Il s’agit d’une mise à jour principal qui inclut un nombre de nouvelles fonctionnalités, ainsi que les autres correctifs et améliorations pilotée par les commentaires dans la mesure où afficher un aperçu de la septembre 2012 :

* Prise en charge de Microsoft Windows Server 2012 en tant que le système d’exploitation cloud.
* Prise en charge situé Azure prise en charge de la mise en cache pour les clients memcached.
* Inclusion des bibliothèques du client Apache Qpid JMS permettant de tirer parti de la messagerie AMQP Azure.
* Un Assistant **Nouveau projet** amélioré, avec une nouvelle page à la fin qui fournit aux utilisateurs la possibilité d’activer rapidement plusieurs fonctionnalités clés courantes dans leur projet : sessions permanentes, la mise en cache et le débogage distant.
* Réduction automatique des instances de rôle 1 lors de l’exécution dans l’émulateur cluster, afin d’éviter les conflits de liaison de port entre instances de serveur.

### <a name="september-28-2012"></a>Septembre 28 décembre 2012

Le plug-in Azure pour Eclipse - septembre 2012 Preview a publié. Cette mise à jour de service inclut un certain nombre de correctifs supplémentaires depuis l’août 2012 afficher un aperçu, ainsi que des améliorations ainsi que de facilité d’utilisation par l’effort commentaires dans les fonctionnalités existantes :

* Prise en charge de Microsoft Windows 8 et Microsoft Windows Server 2012 en tant que le système d’exploitation de développement, résolution des problèmes précédemment empêché le plug-in de fonctionner correctement sur les systèmes d’exploitation.
* Prise en charge améliorée pour la spécification des plages de port de point de terminaison.
* Correctifs liés à chemins d’accès contenant des espaces.
* Rôle contexte menu les améliorations pour accélérer l’accès aux paramètres de configuration spécifiques au rôle.
* Affinements mineures dans l’Assistant **publication vers le cloud** et un certain nombre de correctifs supplémentaires.

### <a name="august-28-2012"></a>28 août 2012

Le plug-in Azure pour Eclipse - août 2012 Preview a publié. Cette mise à jour de service inclut correctifs supplémentaires depuis le juillet 2012 afficher un aperçu, ainsi que plusieurs commentaires par l’effort améliorations des fonctionnalités existantes :

* Dans la boîte de dialogue filtre de Services contrôle Azure Access :
    * **Option permettant d’incorporer le certificat de signature** dans un fichier de votre application guerre, pour simplifier le déploiement de cloud.
    * **Option permettant de créer un certificat auto-signé** au sein de l’interface utilisateur du filtre ACS. Pour plus d’informations sur le filtre de Services Azure accès contrôle, voir [comment authentifier des utilisateurs Web avec Azure accès contrôle Service à l’aide de Eclipse].
* Dans l’Assistant de projet de déploiement d’Azure (s’applique également à la page de propriétés de Configuration du serveur du rôle) :
    * **La détection automatique de l’emplacement JDK** sur votre ordinateur (que vous pouvez remplacer si vous le souhaitez).
    * **Détection automatique du type de serveur** lorsque vous sélectionnez le répertoire d’installation application server.

### <a name="july-15-2012"></a>15 juillet 2012

Le plug-in Azure pour Eclipse - juillet 2012 aperçu, qui répond à un certain nombre des bogues priorité plus élevées trouvé et/ou signalés par les utilisateurs après la version de juin 2012, a publié. Il s’agit d’une mise à jour du service, aucuns nouvelles fonctionnalités n’incluses.

### <a name="june-7-2012"></a>7 juin 2012

Plug-in Azure pour Eclipse - juin 2012 CTP a publié. Nouvelles fonctionnalités :

* **Assistant Nouveau projet de déploiement d’Azure :** Vous pouvez sélectionner votre JDK, serveur d’applications Java, applications et Java directement dans l’Assistant amélioré l’interface utilisateur. Inclus dans la liste des configurations de serveur de prédéfinies pour choisir parmi sont Tomcat 6, 7 Tomcat, GlassFish extensions serveur Office 3, jetée 7, 8 jetée, 6 JBoss et JBoss 7 (autonome). En outre, vous pouvez personnaliser la liste des configurations de serveur. Cette amélioration de l’interface utilisateur est une alternative à faire glisser et déplacer des fichiers compressés et écrasant les scripts de démarrage, qui était auparavant l’approche principale. Cette méthode fonctionne toujours, mais sera probablement utilisée uniquement pour des scénarios plus avancés.
* **Page de propriétés de rôle de Configuration du serveur :** Vous permet de basculer aisément les kits JDK, serveurs d’applications Java et applications associées à votre déploiement après avoir créé le projet. Pour plus d’informations, voir [Propriétés de configuration de serveur].
* **&quot;Publier dans le cloud&quot; Assistant :** offre un moyen facile à déployer votre projet Azure directement à partir de Eclipse, automatiser la précédemment manuelle lourds-levée de l’extraction des informations d’identification, se connecter au portail de gestion Azure, télécharger un package, etc.. Pour obtenir un exemple de comment déployer directement votre projet sur Azure, voir [Création d’une Application Hello World pour Azure dans Eclipse].
* **Azure barre d’outils :** Une barre d’outils Azure est désormais disponible dans Eclipse qui contient des boutons qui appellent les fonctionnalités suivantes :
    * ![][ic710879]**Exécuter dans Azure émulateur**: s’exécute votre projet dans l’émulateur.
    * ![][ic710880]**Réinitialiser Azure émulateur**: réinitialise l’émulateur.
    * ![][ic710881]**Créer de Package Cloud pour Azure**: Compile votre package pour le déploiement.
    * ![][ic710876]**Nouveau projet de déploiement d’Azure**: crée un nouveau projet de déploiement d’Azure.
    * ![][ic710882]**Publier sur Azure Cloud**: publie votre projet sur Azure.
    * ![][ic710883]**Annuler la publication**: supprime votre déploiement.
    * La plupart de ces boutons de barre d’outils Azure sont utilisées pour [créer une Application Hello World pour Azure dans Eclipse].
* **Bibliothèques azure pour Java :** Désormais disponible dans le cadre du Package unique pour les bibliothèques Azure bibliothèque Java dans Eclipse, qui accompagnent l’installation du plug-in et contenant tous les interdépendances de nécessaires. Ajoutez simplement une référence à la bibliothèque dans votre projet Java et vous n’avez pas besoin de télécharger tout ce que séparément. Pour plus d’informations, voir [la boîte à outils Azure pour Eclipse].
* **Microsoft JDBC pilote 4.0 pour SQL Server disponibles pendant l’installation du plug-in :** Pendant l’installation de la nouvelle plug-in, la version la plus récente du pilote JDBC Microsoft pour SQL Server peut être installée.
* **Filtre de Service de contrôle d’un accès de azure disponibles pendant l’installation du plug-in :** Ce nouveau composant, inclus dans une bibliothèque Eclipse dans la boîte à outils, permet à votre application web Java en toute transparence tirer parti d’authentification Azure Access Control Service (ACS) à l’aide de fournisseurs d’identité différents, tels que Yahoo!, Google et Live.com. Vous n’aurez pas besoin écrire la logique d’authentification vous-même, simplement configurer plusieurs options et laisser le filtre faire l’essentiel de permettre aux utilisateurs de se connecter à l’aide de ACS. Vous pouvez simplement vous concentrer sur l’écriture de code qui permet aux utilisateurs l’accès aux ressources en fonction de son identité, telle que retournée à votre application par le filtre à l’intérieur de l’objet de requête. Pour un didacticiel sur l’utilisation du filtre ACS, voir [comment authentifier des utilisateurs Web avec Azure accès contrôle Service à l’aide de Eclipse].
* **La détection automatique du logiciel Azure SDK 1.7 requis :** Lorsque vous créez un nouveau projet de déploiement d’Azure, Azure SDK 1.7 sont automatiquement téléchargés s’il n’est pas déjà installé.
* **Points de terminaison d’instance :** Autorise l’accès de point de terminaison de port directe pour communiquer avec les instances de rôles d’équilibrage de charge. Points de terminaison instance peuvent être ajoutés via l’interface utilisateur, disponible via la page [Propriétés de points de terminaison] de points de terminaison. Cela permet d’activer le débogage distant et diagnostics JMX spécifique de calcul instances qui s’exécutent dans le cloud dans les scénarios par multi-déploiements d’instance. 
* **Composants de l’interface utilisateur :** Simplifie pour les utilisateurs expérimentés configurer des dépendances de projet entre les rôles d’Azure individuels dans le projet et d’autres ressources externes telles que les projets d’application Java ; facilite également décrire la logique de déploiement. Pour plus d’informations, voir [Propriétés de composants].
* **Mise à niveau automatique des versions précédentes de projets :** Lorsque vous ouvrez un espace de travail qui a Azure projet créé avec une version antérieure du plug-in, les projets anciens seront affichés dans Eclipse comme fermé, car les versions précédentes de projets ne sont pas compatibles avec la nouvelle version. Si vous essayez d’ouvrir un de ces projets anciens, un Assistant Mise à niveau démarre. Si vous acceptez la mise à niveau, un nouveau projet, avec **_Upgraded** ajouté sur le nom de sera créé et automatiquement mises à jour pour fonctionner avec la nouvelle version. Vous pouvez renommer le nouveau projet selon vos besoins. Dans le cadre de la mise à niveau, votre projet d’origine n’est pas modifiée (et reste fermé).

### <a name="december-10-2011"></a>10 décembre 2011

Plug-in Azure pour Eclipse - décembre 2011 CTP a publié. Nouvelles fonctionnalités :

* **Affinité session (&quot;sessions permanentes&quot;) prend en charge :** Aider à activer avec état, groupé applications Java avec une seule case à cocher. Pour plus d’informations, voir [Affinité Session].
* **Apportées des exemples de scripts démarrage :** Pour les plus populaires Java serveurs (Tomcat, jetée, JBoss, GlassFish), vous pouvez seulement copier/coller à partir du répertoire d’exemples de votre projet dans votre script de démarrage.
* **Sortie de démarrage émulateur en temps réel :** Vous pouvez désormais regarder l’exécution de toutes les étapes à partir de votre script de démarrage dans une fenêtre de console dédiée, en vous montrant la progression et les erreurs dans votre script lorsqu’elle est exécutée par Azure.
* **Surveillance java.exe automatique et légère :** Qui va forcer une Corbeille rôle lorsque java.exe s’arrête en cours d’exécution, en utilisant un script légère, fabriquée automatiquement inclus dans votre déploiement.
* **Débogage interface utilisateur de configuration de l’application Java distant :** Permet d’activer facilement débogueur distant de Eclipse accéder à votre application Java en cours d’exécution dans l’émulateur ou dans le nuage Azure, afin de pouvoir lancer et déboguer votre code Java en temps réel. Pour plus d’informations, consultez [Débogage des Applications Azure dans Eclipse].
* **Interface utilisateur de configuration de ressources stockage local :** Si vous n’avez plus configurer les ressources locales en manipulant directement le code XML. Cette fonctionnalité permet d’accéder au chemin de fichier efficace de vos ressources locales après que qu’il est déployé via une variable d’environnement que vous pouvez faire référence directement à partir de votre script de démarrage. Pour plus d’informations, voir [Propriétés de stockage Local].
* **Configuration variable d’environnement interface utilisateur :** Si vous n’avez plus à définir des variables d’environnement via une modification manuelle de la configuration XML. Pour plus d’informations, voir [Propriétés de variables d’environnement].
* **Pilote JDBC pour SQL Azure :** Obtient installé via le plug-in dans une bibliothèque Eclipse intégrée, l’activation de programmation plus simple par rapport à SQL Azure. 
* **Menu contextuel rapide l’accès à la configuration du rôle de l’interface utilisateur**: simplement avec le bouton droit sur le dossier de rôle, puis cliquez sur **Propriétés**.
* **Icônes du dossier de projet et rôle Azure personnalisé :** Pour optimiser la visibilité et faciliter la navigation dans votre espace de travail et un projet.

## <a name="see-also"></a>Voir aussi ##

Pour plus d’informations sur les boîtes à outils Azure pour Java IDEs, consultez les liens suivants :

- [Kit de ressources Azure pour Éclipse]
  - [Installez le Kit de ressources de Azure pour Éclipse]
  - [Créer une application Hello World Web pour Azure dans Éclipse]
  - *Quelles sont les nouveautés dans la boîte à outils Azure pour Éclipse (Cet Article)*
- [Kit de ressources Azure pour IntelliJ]
  - [Installez le Kit de ressources de Azure pour IntelliJ]
  - [Créer une application Hello World Web pour Azure dans IntelliJ]
  - [Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

<!-- URL List -->

[Kit de ressources Azure pour Éclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application Hello World Web pour Azure dans Éclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application Hello World Web pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installez le Kit de ressources de Azure pour Éclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installez le Kit de ressources de Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Quelles sont les nouveautés dans la boîte à outils Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Page web de systèmes azul pour la OpenJDK Zoulou]: http://go.microsoft.com/fwlink/?LinkId=402457
[Points de terminaison de Service Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Liste des comptes de stockage Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Propriétés de composants]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Le débogage des Applications Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Déploiement des déploiements]: http://go.microsoft.com/fwlink/?LinkID=699536
[Propriétés de points de terminaison]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Propriétés de variables d’environnement]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Plug-in des outils HDInsight pour Éclipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[L’authentification des utilisateurs Web avec le Service de contrôle d’accès Azure à l’aide d’Éclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Comment utiliser SSL décharger]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés du stockage local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API de Client Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Propriétés de configuration de serveur]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Session affinité]: http://go.microsoft.com/fwlink/?LinkID=699548
[Décharger SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Pour créer un nouveau compte de stockage]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Machine virtuelle et les tailles de Service Cloud pour Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png

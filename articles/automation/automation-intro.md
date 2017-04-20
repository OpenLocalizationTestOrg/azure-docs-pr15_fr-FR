<properties
    pageTitle="Qu’est Automation Azure | Microsoft Azure"
    description="Découvrez quelle valeur Azure Automation fournit et obtenez des réponses aux questions les plus fréquentes afin que vous puissiez commencer en créant, à l’aide des procédures opérationnelles et Azure Automation DSC."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="qu’est automation, automation azure, exemples automation azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Vue d’ensemble de Automation Azure

Microsoft Azure Automation permet aux utilisateurs d’automatiser les tâches manuelles, longue, des erreurs et si vous effectuez souvent qui sont couramment exécutés dans un environnement cloud et d’entreprise. Il fait gagner du temps et accroît la fiabilité des tâches d’administration normales et même les planifie à mettre en place automatiquement à intervalles réguliers. Vous pouvez automatiser des processus à l’aide de procédures opérationnelles ou automatiser la gestion de la configuration à l’aide de la Configuration de l’état souhaité. Cet article fournit brève présentation d’automatisation Azure et fournit des réponses à certaines questions fréquemment posées. Vous pouvez faire référence à d’autres articles dans cette bibliothèque pour plus d’informations sur les différents thèmes.


## <a name="automating-processes-with-runbooks"></a>Automatisation des processus avec des procédures opérationnelles

Un runbook est un ensemble de tâches qui exécutent des processus automatisé dans Azure Automation. Il peut être un processus simple tel que démarrer une machine virtuelle et de créer une entrée de journal, ou vous devrez peut-être une runbook complexe qui combine les autres procédures opérationnelles plus petits pour effectuer un processus complexe sur plusieurs ressources ou même plusieurs nuages et sur les environnements de site.  

Par exemple, vous pourriez avoir un processus manuel existant de la troncation une base de données SQL si elle est approche taille maximale qui comprend plusieurs étapes telles que la connexion au serveur, connexion à la base de données, obtenir la taille actuelle de base de données, vérifier si seuil a dépassé puis tronqué et avertir l’utilisateur. Au lieu d’exécuter manuellement chacune de ces étapes, vous pouviez créer un runbook qui aurait toutes ces tâches fonctionnent comme un processus unique. Vous commencez le runbook, fournissez les informations requises tels que le nom du serveur SQL, nom de la base de données et la messagerie du destinataire et puis réunir lors de la fin du processus. 


## <a name="what-can-runbooks-automate"></a>Qu’automatiser les procédures opérationnelles ?

Procédures opérationnelles dans Azure Automation basés sur Windows PowerShell ou de flux de travail Windows PowerShell, afin qu’ils faire tout ce que PowerShell possibles. Si une application ou un service a une API, un runbook pouvez travailler avec lui. Si vous avez un module PowerShell pour l’application, vous pouvez charger ce module Azure Automation et inclure ces applets de commande dans votre runbook. Procédures opérationnelles Automation Azure s’exécutent dans le cloud Azure et accéder aux ressources de cloud et ressources externes sont accessibles à partir du cloud. Procédures opérationnelles à l’aide de [Hybride Runbook collaborateur](automation-hybrid-runbook-worker.md), pouvez exécuter dans votre centre de données locales pour gérer les ressources locales. 


## <a name="getting-runbooks-from-the-community"></a>Obtention des procédures opérationnelles à partir de la Communauté

La [Galerie Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contient les procédures opérationnelles de Microsoft et la Communauté que vous pouvez utiliser inchangé dans votre environnement ou les personnaliser selon vos besoins. Ils sont également utiles pour en tant que références pour apprendre à créer votre propre procédures opérationnelles. Vous pouvez collaborer de vos propres procédures opérationnelles dans la galerie que vous pensez que d’autres utilisateurs peuvent être utile. 


## <a name="creating-runbooks-with-azure-automation"></a>Création de procédures opérationnelles avec Automation Azure 

Vous pouvez [créer vos propres procédures opérationnelles](automation-creating-importing-runbook.md) à partir de zéro ou de modifier les procédures opérationnelles à partir de la [Galerie Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) de vos besoins. Il existe trois [types de runbook](automation-runbook-types.md) que vous avez le choix entre selon vos besoins et expérience PowerShell. Si vous préférez travailler directement avec le code de PowerShell, vous pouvez utiliser un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) ou [runbook de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que vous modifiez en mode hors connexion ou avec l' [éditeur de texte](http://msdn.microsoft.com/library/azure/dn879137.aspx) dans le portail Azure. Si vous souhaitez modifier un runbook sans l’exposition au code sous-jacent, vous pouvez créer un [graphique runbook](automation-runbook-types.md#graphical-runbooks) à l’aide de l' [éditeur graphique](automation-graphical-authoring-intro.md) dans le portail Azure. 

Vous préférez regardant à lecture ? Regardez l’en dessous de la vidéo à partir de Microsoft Ignite session dans mai 2015. Remarque : Si les concepts et les fonctionnalités décrites dans cette vidéo sont correctes, Qu'azure Automation de progression souvent dans la mesure où cette vidéo a été enregistrée, il maintenant comporte une interface utilisateur plus détaillée dans le portail Azure et prend en charge des fonctionnalités supplémentaires.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatisation de gestion de la configuration avec la Configuration de l’état souhaité 

[DSC PowerShell](https://technet.microsoft.com/library/dn249912.aspx) est une plateforme de gestion qui vous permet de gérer, déployer et appliquer la configuration des hôtes physiques et des machines virtuelles à l’aide d’une syntaxe déclarative PowerShell. Vous pouvez définir des configurations sur un DSC extraire serveur central que les ordinateurs cibles peuvent automatiquement récupérer et appliquer. DSC propose un ensemble d’applets de commande PowerShell que vous pouvez utiliser pour gérer les configurations et des ressources.  

[Azure Automation DSC](automation-dsc-overview.md) est une solution basée sur le nuage pour DSC PowerShell qui fournit des services requis pour les environnements d’entreprise.  Vous pouvez gérer vos ressources DSC dans Azure Automation et appliquer des configurations virtuels ou physiques aux ordinateurs sur lesquels les récupérer à partir d’un serveur DSC extraire dans le cloud Azure.  Il fournit également des services de création de rapports qui informent que vous signaler les événements importants tels que lorsque les nœuds sont sorties à partir de leur configuration affectée et lorsqu’une nouvelle configuration a été appliquée. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Créer vos propres configurations DSC avec Azure Automation

[Configurations DSC](automation-dsc-overview.md#azure-automation-dsc-terms) spécifier l’état d’un nœud souhaité.  Plusieurs nœuds peuvent appliquer la même configuration pour vous assurer qu’ils conservent un état identique.  Vous pouvez créer une configuration à l’aide de n’importe quel texte éditeur sur votre ordinateur local et puis importez-les dans Automation Azure où vous pouvez le compiler et l’appliquer nœuds.


## <a name="getting-modules-and-configurations"></a>Mise en route des modules et des configurations 

Vous pouvez obtenir des [modules PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) contenant des applets de commande que vous pouvez utiliser dans les procédures opérationnelles et les configurations DSC à partir de la [Galerie de PowerShell](http://www.powershellgallery.com/). Vous pouvez lancer cette galerie à partir du portail Azure et importer les modules directement dans Azure Automation, ou vous pouvez télécharger et les importer manuellement. Vous ne pouvez pas installer les modules directement à partir du portail Azure, mais vous pouvez les télécharger afin de les installer comme vous le feriez pour tout autre module. 


## <a name="example-practical-applications-of-azure-automation"></a>Exemple d’applications pratiques d’automatisation Azure 

Voici quelques exemples de ce que sont les types de scénarios d’automatisation avec Azure Automation. 

* Créer et copier des machines virtuelles dans différents abonnements Azure. 
* Planifier des copies de fichiers à partir d’un ordinateur local à un conteneur de stockage d’objets Blob Azure. 
* Automatiser les fonctions de sécurité tels que refuser les demandes d’un client lorsqu’un refus de service est détectée. 
* Vérifiez que machines alignent en permanence avec la stratégie de sécurité configurée.
* Gérer les continue déploiement de code de l’application sur le nuage et sur infrastructure locaux. 
* Créer une forêt Active Directory dans Azure pour votre environnement de test. 
* Tronquer une table dans une base de données SQL si DB approche taille maximale. 
* Mettre à jour les paramètres d’environnement pour un site Web Azure à distance. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Comment sont Azure Automation liés aux autres outils d’automatisation ?

[Service gestion Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) est conçu pour automatiser les tâches de gestion dans le cloud privé. Il est installé localement dans votre centre de données en tant que composant de [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). SMA et Azure automatisation utilisent le même format runbook en fonction de Windows PowerShell et flux de travail Windows PowerShell, mais SMA ne prend pas en charge les [procédures opérationnelles graphique](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) est destiné automatisation des ressources locales. Il utilise un format runbook autre que celui de Automation Azure et automatisation de la gestion Service et dispose d’une interface graphique pour créer des procédures opérationnelles sans exiger toute l’écriture de script. Ses procédures opérationnelles se composent des activités de Packs d’intégration qui sont créés spécialement pour Orchestrator. 


## <a name="where-can-i-get-more-information"></a>Où puis-je obtenir plus d’informations ? 

Diverses ressources sont disponibles pour vous en savoir plus sur Azure Automation et la création de votre propre procédures opérationnelles. 

* **Bibliothèque d’automatisation Azure** est où que vous soyez immédiatement. Les articles dans cette bibliothèque fournissent une documentation complète sur la configuration et l’administration d’automatisation Azure et pour la création de votre propre procédures opérationnelles. 
* [Applets de commande PowerShell Azure](http://msdn.microsoft.com/library/jj156055.aspx) fournit des informations d’automatiser les opérations Azure à l’aide de Windows PowerShell. Procédures opérationnelles utilisent ces applets de commande pour travailler avec des ressources Azure. 
* [Blog sur la gestion](https://azure.microsoft.com/blog/tag/azure-automation/) fournit les dernières informations sur l’Azure Automation et autres technologies de gestion de Microsoft. Vous devez vous abonner à ce billet de blog afin de rester à jour avec les dernières actualités de l’équipe Automation Azure. 
* [Forum Automation](http://go.microsoft.com/fwlink/p/?LinkId=390561) permet de publier des questions sur automatisation Azure à traiter par Microsoft et de la Communauté Automation. 
* [Applets de commande Azure Automation](https://msdn.microsoft.com/library/mt244122.aspx) fournit des informations d’automatiser les tâches d’administration. Il contient des applets de commande pour gérer les comptes automatisation, les biens, les procédures opérationnelles, DSC.


## <a name="can-i-provide-feedback"></a>Puis-je fournir des commentaires ? 

**Veuillez donnez vos commentaires !** Si vous recherchez une solution runbook Azure Automation ou un module d’intégration, publiez une demande de Script sur Centre de scripts. Si vous avez des demandes de fonctionnalité de commentaires ou d’automatisation Azure, les publier sur [Voix utilisateur](http://feedback.windowsazure.com/forums/34192--general-feedback). Merci ! 



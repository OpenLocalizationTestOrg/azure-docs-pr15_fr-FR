<properties 
    pageTitle="Journalisation pour les services web apprentissage automatique | Microsoft Azure" 
    description="Découvrez comment activer la journalisation pour les services web apprentissage automatique. Journalisation fournit des informations supplémentaires pour aider à résoudre les problèmes de l’API." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Activer la journalisation pour les services web apprentissage automatique  

Ce document fournit des informations sur la fonctionnalité de journalisation de services Web classique. L’activation de l’enregistrement dans les services Web fournit des informations supplémentaires, au-delà d’un numéro d’erreur et un message, qui peut vous aider à résoudre les problèmes de vos appels à l’API de formation Machine.  

Pour activer l’enregistrement dans les Services Web dans le portail classique Azure :   

1.  Se connecter au [portail classique Azure](https://manage.windowsazure.com/)
2.  Dans la colonne fonctionnalités gauche, cliquez sur **Apprentissage automatique**.
3.  Cliquez sur votre espace de travail, puis les **SERVICES WEB**.
4.  Dans la liste des services Web, cliquez sur le nom du service Web.
5.  Dans la liste des points de terminaison, cliquez sur le nom du point de terminaison.
6.  Cliquez sur **configurer**.
7.  Définissez **Niveau de suivi des DIAGNOSTICS** en *erreur* ou *toutes les*, puis cliquez sur **Enregistrer**.

Pour activer la journalisation dans le portail Azure Machine Learning Web Services.

1. Se connecter au portail [Azure Machine Learning Web Services](https://services.azureml.net) .
2. Cliquez sur Services Web classique.
3.  Dans la liste des services Web, cliquez sur le nom du service Web.
4.  Dans la liste des points de terminaison, cliquez sur le nom du point de terminaison.
5.  Cliquez sur **configurer**.
6.  Définissez la **journalisation** à *l’erreur* ou *toutes les*, puis cliquez sur **Enregistrer**.

## <a name="the-effects-of-enabling-logging"></a>Les effets d’activation de l’enregistrement

Lorsque la journalisation est activée, toutes les erreurs du point de terminaison sélectionné et les diagnostics sont enregistrés sur le compte de stockage Azure lié à l’espace de travail de l’utilisateur. Vous pouvez voir ce compte de stockage sur le portail classique Azure affichage tableau de bord (en bas de la section Aperçu rapide) de son espace de travail.  

Les journaux peuvent être affichés à l’aide de plusieurs outils disponibles pour un compte de stockage Azure « Explorer ». Le plus simple est simplement naviguez vers le compte de stockage dans le portail classique Azure, puis cliquez sur **conteneurs**. Un conteneur nommé **ml diagnostics**s’affichent. Ce conteneur conserve toutes les informations de diagnostic pour toutes les extrémités de service web pour les espaces de travail associées à ce compte de stockage. 
 
## <a name="log-blob-detail-information"></a>Journal des informations détaillées sur les objets blob

Chaque blob dans le conteneur conserve les informations de diagnostic pour exactement une des opérations suivantes :

-   L’exécution de la méthode de l’exécution par lots  
-   L’exécution de la méthode de requête-réponse  
-   Échec de l’initialisation d’un conteneur de requête-réponse
  
Le nom de chaque blob comporte un préfixe sous la forme suivante : 

{Id de l’espace de travail}-{service Id Web}-{Id point de terminaison} / {type journal}  

Où type journal est une des valeurs suivantes :  

- traitement par lots  
- score/demandes  
- score/initialisation  
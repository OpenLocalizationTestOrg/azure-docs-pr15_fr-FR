<properties
    pageTitle="Web application clonant à l’aide du portail Azure"
    description="Découvrez comment cloner vos applications Web nouvelles applications Web à l’aide du portail Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Application de Service application Azure clonant à l’aide de portail Azure#

La fonctionnalité clonage dans [Azure Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de cloner facilement des applications web existantes vers une application nouvellement créée dans une zone différente ou dans la même région. Cela va permettre aux clients à déployer un nombre d’applications dans différentes régions rapidement et facilement.

Application clonant est actuellement pris en charge uniquement pour les offres de service premium couche application. La nouvelle fonctionnalité utilise les limitations mêmes en tant que fonction de sauvegarde des applications Web, voir [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>Clonant une application existante ##

L’application web doit s’exécuter dans le mode **Premium** afin que vous permettent de créer une copie pour l’application web.

1. Dans le [Portail Azure](https://portal.azure.com/), ouvrez la carte de votre application web.
2. Cliquez sur **Outils**. Puis, dans la carte **Outils** , cliquez sur **Application cloner**.

    ![][1]

    > [AZURE.NOTE]
    > Si l’application web n’est pas déjà en mode **Premium** , vous recevrez un message indiquant que les modes pris en charge pour l’application clonant. À ce stade, vous avez la possibilité de sélectionner la **mise à niveau**.
    
3. Dans la carte **Cloner application** attribuez un nom de la nouvelle application web, le groupe de ressources et le Plan de Service d’application. Également l’utilisateur sera en mesure de choisir si vous voulez dupliquer un nombre de paramètres de l’application web source ou non.

    ![][2]

4. Après avoir cliqué sur **créer** la plateforme commence à travailler sur la création d’un cloner de l’application web source.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonant une application existante dans un environnement de Service d’application##

Dans la carte **Application cloner** le client aura l’option à choisir un pool d’applications dans un environnement de Service d’application existant.

## <a name="current-restrictions"></a>Restrictions actuelles ##

Cette fonctionnalité n’est actuellement en mode Aperçu avant, nous travaillons pour ajouter de nouvelles fonctions au fil du temps, la liste suivante sont les restrictions connues sur la prise en charge en cours d’application clonant Azure portail :

- Paramètres du Gestionnaire de trafic Azure ne sont pas cloner
- Paramètres d’échelle automatique ne sont pas cloner
- Paramètres de planification de la sauvegarde ne sont pas cloner
- Paramètres de VNET ne sont pas cloner
- Application perspectives ne sont pas automatiquement définies sur l’application web de destination
- Facile Auth paramètres ne sont pas cloner
- Extension kudu ne sont pas cloner
- Conseil règles ne sont pas cloner
- Contenu de la base de données ne sont pas cloner


### <a name="references"></a>Références ###
- [Web application clonant à l’aide de PowerShell](app-service-web-app-cloning.md)
- [Sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md)
- [Comment créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md)
- [Créer une application web dans un environnement de Service d’application](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png
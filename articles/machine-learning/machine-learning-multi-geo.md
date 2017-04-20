<properties
   pageTitle="La documentation d’aide multi-Geo | Microsoft Azure"
   description="Apprenez à créer un espace de travail et publier un service web dans un autre à partir du Sud Central aux États-Unis (SCUS) une région Azure Azure région."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Documentation d’aide multi-Geo

Cet article décrit comment vous pouvez créer un espace de travail et publier un service web dans d’autres régions Azure.

## <a name="create-a-workspace"></a>Créer un espace de travail

1. Connectez-vous au portail classique Azure.

2.  Cliquez sur **+ Nouveau** > **SERVICES Business DATA** > **apprentissage automatique** > **rapide Créer**.  Sous **emplacement** , sélectionnez un autre région, par exemple des **pays d’Asie du Sud-est**.
![Image multi-Geo aide 1][1]
3. Sélectionnez l’espace de travail, puis cliquez sur **se connecter au Studio ML**.
![Image multi-Geo aide 2][2]

4. Vous avez maintenant un espace de travail dans une autre zone que vous pouvez utiliser comme n’importe quel autre espace de travail. Pour basculer entre vos espaces de travail, recherchez le coin supérieur droit de votre écran. Cliquez sur la liste déroulante, sélectionnez la région, puis l’espace de travail. Tout est local dans la zone d’espace de travail ; par exemple, tous les services web créés à partir d’un espace de travail seront trouvent dans la même région que se trouve à l’espace de travail.
![Image de vous aider à plusieurs Geo 3][3]

## <a name="open-an-experiment-from-gallery"></a>Ouvrir une expérience de galerie

Si vous ouvrez une expérience à partir de la galerie, vous pouvez également sélectionner quelle région que vous voulez copier l’expérience à.

![Image de vous aider à plusieurs Geo 4][4a]

## <a name="web-service-management"></a>Gestion du service Web

Pour gérer par programme les services web, tels que de reconversion, utilisez l’adresse régionale :
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Éléments à noter

1.  Vous pouvez copier uniquement l’expériences entre les espaces de travail qui appartiennent à la même région de cette manière. Si vous avez besoin copier expérience au sein des espaces de travail dans différentes régions, vous pouvez utiliser l’applet de commande [PowerShell](http://aka.ms/amlps) [*Copie AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) pour ce faire. Une autre solution de contournement consiste à publier l’expérience dans la galerie en mode non répertorié, puis ouvrez-le dans l’espace de travail à partir de l’autre région.
2.  Le sélecteur de région ne montrera que les espaces de travail pour une région à la fois. À l’avenir, vous ne pourrez pas obtenir la liste complète des espaces de travail que vous avez accès dans toutes les régions en même temps.  
3.  Un espace de travail gratuit ou un espace de travail (anonyme) accès invité est créé et hébergé dans sud américain À l’avenir, vous ne pourrez pas créer des espaces de travail Access Free/invité dans la région que vous choisissez.  
4.  Services Web déployés à partir d’un espace de travail Asie du Sud-est seront également hébergées Asie du Sud-est. À l’avenir, vous ne pourrez pas la possibilité de créer des expériences dans une zone géographique et déploiement généré des points de terminaison de service web dans différentes régions.  

## <a name="more-information"></a>Plus d’informations

Poser une question sur le [forum d’apprentissage automatique Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<properties
    pageTitle="Création d’un modèle avec la Recommnendations UI | Microsoft Azure"
    description="Azure recommandations d’apprentissage automatique - création d’un modèle avec l’interface utilisateur de recommandations"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Création d’un modèle avec l’interface utilisateur de recommandations

Ce document est un guide étape par étape. Notre objectif est de vous guident dans les étapes nécessaires pour former un modèle à l’aide de l' [Interface utilisateur de recommandations](https://recommendations-portal.azurewebsites.net/).
Passant par l’exercice permet de comprendre le processus de création d’un modèle avant d’effectuer par programme. Il également permet de familiarise vous avec l’interface utilisateur, qui est utile lorsque vous commencez à l’aide du service.

Cet exercice prend environ 30 minutes.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Étape 1 : se dans l’interface utilisateur des recommandations ##

1. Si vous n’avez pas déjà fait, vous devez [d’abonnement](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) pour un nouvel abonnement [API recommandations](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) . Vous pouvez vous inscrire pour le service sur **Azure** à [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) et vous connecter avec votre compte Azure. Des instructions détaillées sur le processus de connexion sont fournies dans la *tâche 1* du [Guide de démarrage](cognitive-services-recommendations-quick-start.md) 

1. Une fois que vous avez obtenu une **clé** pour votre abonnement API de recommandations, accédez à [L’interface utilisateur de recommandations](https://recommendations-portal.azurewebsites.net/). 

1. Connectez-vous au portail en entrant votre clé dans le champ de **Clé de compte** , puis cliquez sur le bouton de **connexion** .

    ![Interface utilisateur de recommandations : Connexion boîte de dialogue.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Étape 2 : nous allons recueillir des données de formation ##

Vous pouvez créer une version, le moteur doit deux éléments d’information : un fichier de catalogue et un jeu de fichiers d’utilisation. 

Le fichier catalogue contient des informations sur les éléments que vous proposez à votre client. Un fichier de l’utilisation contient des informations sur la façon dont ces éléments sont utilisés, ou les transactions à partir de votre entreprise.

En règle générale, vous le feriez interroger votre base de données pour ces éléments d’information. L’heure actuelle, nous vous fournissons des exemples de données pour vous afin que vous pouvez apprendre à utiliser l’API de recommandations.

Vous pouvez télécharger les données à partir de [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copier et décompresser le fichier **Books.Zip** dans un dossier sur votre ordinateur local. Par exemple, **c:\data**.

Vous trouverez des informations détaillées sur le schéma des fichiers catalogue et l’utilisation dans l’article de la [collecte de données pour l’apprentissage votre modèle](cognitive-services-recommendations-collecting-data.md) .
 
Pour cet exercice, nous allons pour fonctionner avec un petit fichier afin que vous n’êtes pas obligé très attendre trop longue pour la formation. Si vous voulez essayer un fichier plus plausible, nous avons également **MsStoreData.zip** contenant des exemples de transactions à partir de Microsoft Store dans [même emplacement](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Étape 3 : créer un projet et télécharger catalogue et l’utilisation des données ##

Lors de la connexion à l' [Interface utilisateur de recommandations](https://recommendations-portal.azurewebsites.net/), vous consultez la Page de projets. Si vous avez précédemment créé tous les projets, vous devez les voir ici.
Un projet (également connu sous le nom *d’un modèle* dans la référence de l’API) est un conteneur pour vos données de catalogue et l’utilisation. Vous pouvez créer plusieurs *crée* à l’intérieur du projet. Nous vous guidera le processus dans les étapes suivantes.

1. Pour créer un nouveau projet, tapez le nom dans la zone de texte (par exemple « MyFirstModel » pourrait fonctionner) et cliquez sur **Ajouter un projet**.
 
    ![Interface utilisateur recommandations : Page de projets.][reco_projects]

1. Une fois que le projet est créé, cliquez sur le bouton **Rechercher un fichier** dans la section **Ajouter un fichier de catalogue** . Télécharger le catalogue que vous avez obtenu à l’étape 2. Si vous avez enregistré à *c:\data*, vous devez accéder à ce dossier.

    ![Recommandations l’interface utilisateur : Ajouter des données à un projet.][reco_firstmodel]

1. Une fois que le catalogue est téléchargé, cliquez sur le bouton **Rechercher un fichier** dans la section **Ajouter des fichiers d’utilisation** . Ajoutez le fichier usage_large.txt.

> **Que se passe-t-il si j’ai un gros fichier de données d’utilisation ?**
>
> Seuls les fichiers l’utilisation inférieures à 200 Mo peuvent être téléchargés. Ceci étant dit, le système peut contenir jusqu'à 2 Go valeur des données de transaction, afin de télécharger plusieurs fichiers si nécessaire.
> Vous devrez peut-être pas toutes ces données pour générer un bon modèle, il n’est pas simplement la taille des données qui est important, mais la qualité des données. Il est courant pour afficher l’utilisation de données dans lequel la plupart des transactions est simplement sur un petit nombre d’éléments les plus consultés, et il est « peu signaler » pour d’autres éléments.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Étape 4 : nous allons faire une formation ! ##

À présent que vous avez téléchargé le catalogue et les données d’utilisation, nous sommes prêts à former le moteur afin qu’il peut découvrir modèles à partir de nos données.

1.  Cliquez sur le bouton **Créer nouveau** .

1.  Sélectionnez **les recommandations** comme type de génération. Vous pouvez remarquer que nous prenons en charge générer un classement types ainsi que de build un FBT (fréquemment acheté ensemble).

    ![Recommandations l’interface utilisateur : Boîte de dialogue Créer.][reco_build_dialog.png]


    Une version FBT permet d’identifier des modèles pour les produits qui sont généralement acheté/consommées dans la même transaction.
    Une version de classement est utilisée pour identifier les fonctionnalités utiles. 
    Nous n’Approfondissez très FBT ou classement crée dans cet atelier, mais si vous souhaitez vous devez extraire [Générer des types et page de documentation de la qualité du modèle](cognitive-services-recommendations-buildtypes.md).

1. Cliquez sur le bouton **Générer** . Le processus de génération prend environ 5 minutes si vous utilisez les données de carnet d’adresses. Il est plus longue sous jeux de données plus grande.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Étape 5 : nous allons découvrir ce que l’ordinateur appris ! ##

Une fois que votre build est terminée, vous remarquerez une nouvelle version dans la liste des versions. Cette build peut être interrogée pour obtenir des recommandations élément et utilisateur.

1. Une fois que votre build est terminée, cliquez sur **note**. Cela vous permet de jouer avec le modèle et voir quels éléments sont recommandés.

    ![Recommandations l’interface utilisateur : Bouton de Score][reco_score_button]

1. Sélectionnez un élément pour afficher les éléments sont retournés sous forme de recommandations pour cet élément. Notez que s’il n'existe pas assez de mouvements de prédire une recommandation pour un élément particulier, le système renverra pas les recommandations pour cet élément.  Si pour une raison quelconque, vous avez un élément qui ne renvoie aucune recommandation, essayez d’autres éléments de score.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Étape 6 : étapes suivantes ##
Félicitations ! vous avez formé un modèle et puis ont présenté recommandations à partir de ce modèle.  L’interface utilisateur de recommandations est un outil utile qui permet d’accéder à l’état de vos projets et crée. 

À présent que vous disposez d’un modèle, vous souhaiterez savoir comment faire par programme toutes les étapes ci-dessus. Afin de plus d’appeler l’API par programme, nous vous invitons à vérifier la [Référence de l’API recommandations](http://go.microsoft.com/fwlink/?LinkId=759348) et télécharger l' [Exemple d’Application recommandations](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png

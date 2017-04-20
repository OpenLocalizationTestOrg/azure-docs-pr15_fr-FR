<properties 
    pageTitle="Configurer et utiliser l’API de recommandations apprentissage Machine | Microsoft Azure" 
    description="API de recommandations Microsoft créées avec Azure Machine apprentissage Forum aux questions" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>La configuration et la Machine apprentissage recommandations API Forum aux questions


**Quelles sont les recommandations ?**

>[AZURE.NOTE]Vous devez commencer au moyen du Service de troubles de l’API de recommandations au lieu de cette version. Le Service de recommandations cognitifs remplacera ce service, et toutes les nouvelles fonctionnalités seront développées il. Elle comporte des nouvelles fonctionnalités telles que le traitement par lots de prise en charge, une meilleure API Explorer, une expérience de l’inscription/facturation surface, plus cohérentes nettoyage API, etc..
> Pour plus d’informations sur la [migration vers le nouveau Service cognitifs](http://aka.ms/recomigrate)

Pour les organisations et activités qui s’appuient sur les recommandations à croisées et incitative produits et services à leurs clients, recommandations dans l’apprentissage automatique Azure fournit un moteur de recommandations libre-service. Il s’agit d’une implémentation de filtrage collaboratif qui utilise une factorisation matrice comme algorithme de base. Les développeurs d’applications peuvent accéder aux recommandations en utilisant des API REST. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Que puis-je faire avec des recommandations ?**

RECOMMANDATIONS prend comme entrée d’un élément ou un ensemble d’éléments et renvoie une liste de recommandations pertinentes. Par exemple : un client lors d’un revendeur en ligne clique sur un produit. Le détaillant en ligne envoie ce produit comme entrée aux recommandations, obtient une liste de produits en retour et décide lequel de ces produits seront affichés pour le client. Vous souhaiterez peut-être utiliser les recommandations pour optimiser votre magasin en ligne ou même d’informer votre intérieur vente centre département ou appel.

**Existe-t-il des restrictions d’utilisation ?**

Recommandations comporte les limites de l’utilisation suivantes :
* Nombre maximal de modèles par abonnement : 10
* Nombre maximal d’éléments pouvant être un catalogue : 100 000
* Le nombre maximal de points de l’utilisation qui sont conservées est ~ 5 000 000. La plus ancienne sera supprimée si nouveaux doit être téléchargés ou signalés.
* Taille maximale des données qui peuvent être envoyées par courrier électronique (par exemple, importer catalogue des données, importer des données de l’utilisation) est de 200 Mo
* Nombre de transactions par seconde (support) pour construction d’un modèle recommandations qui n’est pas active est support ~ 2. Construction d’un modèle recommandations qui est active peut contenir jusqu'à 20 support.

##<a name="purchase-and-billing"></a>Achat et facturation 


**Combien coûte coût recommandations pendant la période de lancement ?**

Recommandations est un service basé sur un abonnement. Planification des est basé sur le volume de transactions par mois. Vous pouvez vérifier la [page de l’offre] (https://datamarket.azure.com/dataset/amla/recommendations) dans Microsoft Azure Marketplace pour les informations de prix.

**Existe-t-il les coûts liés à la nécessité de recommandations suivre et de stockent l’activité des utilisateurs pour moi ?**

Pas pour le moment.

**Recommandations existe-t-il une version d’évaluation gratuite ?**

Il existe une trace gratuite qui est limitée à 10 000 transactions par mois.

**Lorsque j’ai facturation pour obtenir des recommandations ?**

Un abonnement payant est un abonnement pour lequel il existe un abonnement mensuel. Lorsque vous achetez un abonnement payant, vous êtes chargé immédiatement pour une utilisation du premier mois. Vous êtes chargé le montant associé à l’offre sur la page Abonnements (plus taxes applicables). Cet abonnement mensuel est rendu chaque mois à la même date de calendrier en tant que votre achat d’origine jusqu'à l’annulation de l’abonnement. 

**Comment passer à un service de niveau supérieur ?**

Vous pouvez acheter ou mettre à jour votre abonnement à partir de la [page de l’offre] page (https://datamarket.azure.com/dataset/amla/recommendations) sur Microsoft Azure Marketplace.

Lorsque vous mettez à niveau un abonnement :

* Transactions restant sur votre ancien abonnement ne sont pas ajoutées à votre nouvel abonnement. 
* Vous payez prix complet pour le nouvel abonnement, même si vous avez des transactions inutilisées de votre ancien abonnement.

Procédure à suivre pour mettre à niveau un abonnement :

* Nevigate à la page] offre (https://datamarket.azure.com/dataset/amla/recommendations).
* Se connecter à la place de marché si vous n’êtes pas déjà connecté.
* Dans le volet droit, tous les plans disponibles sont répertoriées. Cliquez sur le bouton d’option pour le plan que vous voulez mettre à niveau vers.
* Si vous souhaitez mettre à niveau, cliquez sur **OK**. Si vous ne souhaitez pas mettre à niveau, cliquez sur **Annuler**.

**Important** Lisez attentivement la boîte de dialogue Mise à niveau, car il existe des implications facturation et l’utilisation.

**Quand mon abonnement aux recommandations se termine ?**

Votre abonnement se termine lorsque vous l’annuler. Si vous voulez annuler votre abonnement, reportez-vous aux instructions suivantes.

**Comment annuler mon abonnement recommandations ?**

Pour annuler votre abonnement, procédez comme suit. Si votre abonnement actuel est un abonnement payant, votre abonnement continue en vigueur jusqu'à la fin de la période de facturation actuelle. Si vous avez besoin d’être efficace immédiatement de l’annulation, contactez-nous au [Support technique Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Remarque** Aucun remboursement n’est remplacé par si vous annulez avant la fin d’une période de facturation ou pour les transactions non utilisées dans une période de facturation.

* Accédez à la page] offre (https://datamarket.azure.com/dataset/amla/recommendations).
* Se connecter à la place de marché si vous n’êtes pas déjà connecté.
* Cliquez sur **Annuler** à droite du nom du jeu de données et état. Vous pouvez utiliser cet abonnement jusqu'à la fin de la période de facturation actuelle ou votre transaction imparti (selon ce qui se produit en premier).

Si vous voulez annuler votre abonnement immédiatement ainsi que vous pouvez acheter un nouvel abonnement, archiver un tickets à [Support technique Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Mise en route des recommandations

**Est recommandations pour moi ?** 

Recommandations dans l’apprentissage automatique est pour les entreprises qui s’appuient sur les recommandations pour croisées et incitative produits ou services à leurs clients. Si vous avez un site Web destiné au client, une force de vente, un inside force de vente, ou un centre d’appels, et si vous proposez un catalogue de plus de quelques douzaines produits ou services, votre chiffre d’affaires peuvent bénéficier de l’utilisation de recommandations. 

Expérimenter recommandations est conçu pour être assez simple. La version actuelle en fonction de l’API requiert des compétences de programmation simples. Si vous avez besoin d’aide, contactez le fournisseur qui développé votre site Web. Si vous avez un service informatique interne ou un développeur interne, il doivent être en mesure d’afficher des recommandations pour vous convient. 

**Quelles sont les conditions préalables à la configuration recommandations ?**

Recommandations requiert que vous disposiez d’un journal des choix de l’utilisateur par rapport à votre catalogue. Si vous ne t disposer d’un tel journal et vous avez un site Web en regard du client, recommandations recueille des activités des utilisateurs à votre place. 

Recommandations nécessite également un catalogue de vos produits ou services. Si vous ne t que le catalogue, recommandations peuvent utiliser les données d’utilisation client réel et diffuser un catalogue. Un catalogue implicite n’inclut pas les éléments qui ont été signalés pas dans le cadre de transactions utilisateur.

**Comment configurer la recommandations pour la première fois ?**

Après [l’abonnement] (https://datamarket.azure.com/dataset/amla/recommendations) pour obtenir des recommandations, vous devez utiliser la documentation de l’API [Azure Machine apprentissage recommandations Guide de démarrage rapide](machine-learning-recommendation-api-quick-start-guide.md) pour configurer le service.

**Où puis-je trouver documentation API ?** 

La documentation de l’API est [Azure Machine apprentissage recommandations Guide de démarrage rapide](machine-learning-recommendation-api-quick-start-guide.md).

**Les options que je dois télécharger des données de catalogue et l’utilisation des recommandations**

Vous avez deux possibilités pour le téléchargement de votre catalogue et l’utilisation des données : vous pouvez exporter les données à partir de votre système CRM ou d’autres journaux et téléchargez-le sur recommandations, ou vous pouvez ajouter des balises à votre site Web qui effectue le suivi des activités de l’utilisateur. Si vous utilisez ce dernier mode, les données seront stockées dans Azure.

##<a name="maintenance-and-support"></a>Prise en charge et la maintenance

**La taille peut être mon jeu de données ?**

Chaque jeu de données peut contenir jusqu'à 100 000 éléments du catalogue et jusqu'à 2 048 Mo de données d’utilisation.
En outre, un abonnement peut contenir jusqu'à 10 jeux de données (modèles).

**Où puis-je obtenir un support technique pour obtenir des recommandations ?**

Support technique est disponible sur le site [Microsoft Azure prend en charge](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**Où puis-je trouver les conditions d’utilisation ?**

[Machine Microsoft Azure conditions d’utilisation de l’API recommandations d’apprentissage](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 

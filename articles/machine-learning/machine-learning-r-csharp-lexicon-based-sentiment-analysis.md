<properties 
    pageTitle="Lexique basé opinion analyse | Microsoft Azure" 
    description="Lexique basé opinion analyse" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Lexique basé opinion analyse 

Comment pouvez-vous mesurer utilisateurs avis et attitude des marques ou rubriques de réseaux sociaux en ligne, tels que Facebook publie, de tweets, révisions, etc.. ? Analyse opinion offre une méthode d’analyse de ces questions.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il existe généralement deux méthodes pour une analyse opinion. Une utilise un algorithme d’apprentissage contrôlés et l’autre peut être traité en tant qu’apprentissage. Un algorithme d’apprentissage contrôlés crée généralement un modèle de classement dans une grande corpus annotés. Sa précision est principalement basée sur la qualité de l’annotation, et généralement le processus de formation prendra beaucoup de temps. En outre, lorsque nous appliquez l’algorithme à un autre domaine, le résultat est généralement pas bonne. Par rapport aux contrôlés apprentissage, en fonction de lexique utilise apprentissage un dictionnaire opinion, qui ne nécessite pas le stockage un corpus grande quantité de données et la formation - qui rend l’ensemble du processus beaucoup plus rapide. 

Notre [service](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) est basé sur le lexique subjectivité MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), ce qui correspond à un des lexiques subjectivité plus couramment utilisés. Il existe 5097 mots positifs négatives et 2533 dans MPQA. Et tous ces mots sont annotées comme polarité forte ou faible. Le corpus entière se trouve sous licence Grand Public GNU. Le service web peut être appliqué à toute phrase courte, tels que tweets et billets Facebook. 

>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.

##<a name="consumption-of-web-service"></a>Consommation de service web

Les données d’entrée peuvent être du texte, mais le service web fonctionne mieux avec des phrases. Le résultat est une valeur numérique comprise entre -1 et 1. Toute valeur inférieure à 0 indique que l’opinion du texte est négative ; positif si supérieur à 0. La valeur absolue du résultat indique la puissance de l’opinion associée. 

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



L’entrée est « Aujourd'hui est une bonne journée ». Le résultat est « 1 », qui indique un sentiment positif associée à la phrase d’entrée. 

##<a name="creation-of-web-service"></a>Création du service web
>Ce service web a été créé à l’aide d’apprentissage automatique Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication web services](machine-learning-publish-a-machine-learning-web-service.md), voir [azure.com/ml](http://azure.com/ml). Vous trouverez ci-dessous une capture d’écran de l’expérience créé le code de service et exemple web pour chacun des modules au sein de l’expérience.


À partir d’apprentissage automatique Azure, une nouvelle expérience vide a été créée. L’illustration suivante montre le flux expérience d’analyse en fonction de lexique opinion. Le fichier « sent_dict.csv » est le lexique subjectivité MPQA et est défini comme une des entrées [d’Exécuter le Script R][execute-r-script]. Une autre entrée est une révision échantillonnée du DataSet de révision Amazon pour test, dans lequel nous effectué sélection, modification du nom de colonne et opérations de fractionnement. Nous utilisons un package du hachage pour stocker le lexique subjectivité dans la mémoire et accélérer le processus de calcul score. Le texte complet est sous forme de jetons par lot « tm » et comparaison avec le mot dans le dictionnaire opinion. Enfin, un score est calculé en ajoutant l’épaisseur de chaque mot subjective dans le texte. 

###<a name="experiment-flow"></a>Flux expérience :

![expérience flux][2]


####<a name="module-1"></a>Module 1 :
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Installer package du hachage
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Limitations

Du point de vue de l’algorithme, analyse opinion basée sur lexique est un outil d’analyse opinion générale, ce qui peut ne pas fonctionner mieux que la méthode de classification de champs spécifiques. Le problème de négation n’est pas correctement traité. Nous coder négation plusieurs mots dans notre programme, mais un meilleur moyen est à l’aide d’un dictionnaire de négation et créer des règles. Le service web performant phrases courts et simples, tels que tweets et billets de Facebook, à long et complexe phrases tels que des révisions Amazon. 

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

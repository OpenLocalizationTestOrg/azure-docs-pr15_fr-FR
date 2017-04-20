<properties 
    pageTitle="Analyse de survie avec apprentissage automatique Azure | Microsoft Azure" 
    description="Probabilité de l’occurrence de survie analyse événement" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>Analyse de survie 

Sous beaucoup de scénarios, le résultat en cours d’évaluation principal est le moment pour un événement de votre choix. En d’autres termes, la question « lorsque cet événement se produira ? » Il est demandé. Par exemple, tenir compte des cas où les données décrivent le temps écoulé (jours, années, kilométrage, etc.) jusqu'à ce que l’événement d’intérêt (RECHUTE maladie, degré faisant pivoter reçu, échec du pavé de numérotation frein) se produit. Chaque instance dans les données représente un objet particulier (une patients, un étudiant, une voiture, etc.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) répond à la question « Quelle est la probabilité que l’événement d’intérêt produira par heure n pour objet x? » En fournissant un modèle d’analyse de survie, ce service web permet aux utilisateurs de fournir des données pour former le modèle et tester. Le thème de l’expérience principal consiste à modéliser la longueur du temps écoulé jusqu'à ce que l’événement d’intérêt se produit. 

>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.  

##<a name="consumption-of-web-service"></a>Consommation de service web

Le schéma de données d’entrée du service web est présenté dans le tableau suivant. Six informations sont nécessaires comme entrée : les données d’apprentissage, test des données, temps d’intérêt, l’index de « heure » de dimension, l’index de dimension « événement » et les types de variables (continu ou facteur). Les données de formation sont représentées par une chaîne, où les lignes sont séparées par une virgule et les colonnes sont séparées par des points-virgules. Le nombre de fonctionnalités des données est flexible. Tous les éléments dans la chaîne d’entrée doivent être numériques. Dans les données de formation, la dimension « temps » indique le nombre d’unités de temps (jours, années, kilométrage, etc.) qui se sont écoulées depuis le point de départ de l’étude (un patients recevoir des programmes de traitement de médicaments, une étude faisant pivoter départ étudiant, une voiture commence à être par l’effort, etc.) jusqu'à ce que l’événement d’intérêt (les patients revenir à l’utilisation de médicaments, la probabilité d’obtenir le degré faisant pivoter, la voiture que frein pavé défaillance etc..) se produit. La dimension « événement » indique si l’événement d’intérêt se produit à la fin de l’étude. Une valeur de « événement = 1 » signifie que l’événement d’intérêt se produit à l’heure indiquée par la dimension « temps » ; « événement = 0 » signifie que l’événement d’intérêt n'a pas eu lieu à l’heure indiquée par la dimension « temps ».

- trainingdata - une chaîne de caractères. Lignes sont séparées par une virgule, et les colonnes sont séparées par des points-virgules. Chaque ligne inclut dimension « temps », « événement » dimension et variables explicatives.
- testingdata - une ligne de données qui contient des variables predictor d’un objet particulier.
- time_of_interest - le temps écoulé d’intérêt n.
- index_time - l’index de colonne de la dimension « time » (en commençant par 1).
- index_event - l’index de colonne de la dimension « événement » (en commençant par 1).
- variable_types - une chaîne de caractères par des points-virgules comme séparateurs qu’il contient. 0 représente variables continues et 1 variables facteur.


Le résultat est la probabilité d’un événement se produisant à une date spécifique. 

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




L’interprétation de ce test est la suivante. En supposant que l’objectif des données consiste à modéliser le temps écoulé jusqu’au retour à l’utilisation de médicaments pour les patients ayant reçu un des deux traitement programmes. Le résultat de la lecture de service web : pour plus de patients en cours 35 ans, ayant précédente de médicaments traitement 2 fois, en tenant le programme de traitement long résidentielle, et avec l’utilisation héroïne et cocaïne, la probabilité de revenir sur l’utilisation de médicaments 95.64 % par jour 500.

##<a name="creation-of-web-service"></a>Création du service web

>Ce service web a été créé à l’aide d’apprentissage automatique Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication web services](machine-learning-publish-a-machine-learning-web-service.md), voir [azure.com/ml](http://azure.com/ml). Vous trouverez ci-dessous une capture d’écran de l’expérience créé le code de service et exemple web pour chacun des modules au sein de l’expérience.

À partir d’apprentissage automatique Azure, une nouvelle expérience vide a été créée et deux [Exécuter le Script R] [ execute-r-script] modules ont été extrait sur l’espace de travail. Le schéma de données a été créé avec un simple [Exécuter le Script R][execute-r-script], qui définit le schéma de données d’entrée pour le service web. Ce module est ensuite lié au deuxième [Exécuter le Script R] [ execute-r-script] module, ce qui principales travail. Ce module effectue prétraitement des données, création de modèles et des prédictions. Dans l’étape prétraitement des données, les données d’entrée représentées par une longue chaîne sont transformées et converties en une trame de données. Dans l’étape de construction de modèle, un package R externe « survival_2.37 7.zip » est installé tout d’abord pour la réalisation d’analyse de survie. La fonction « coxph » est ensuite exécutée après une tâche de traitement des données de série. Les détails de la fonction « coxph » pour l’analyse de survie peuvent être lus à partir de la documentation R. Dans l’étape de prévision, une instance de test est fournie dans le modèle formé avec la fonction « surfit », et la courbe de survie pour cette instance de test est générée comme variable « courbe ». Enfin, la probabilité de l’heure d’intérêt est obtenue. 

###<a name="experiment-flow"></a>Flux expérience :

![expérience flux][1]

####<a name="module-1"></a>Module 1 :

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>Module 2 :

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>Limitations

Ce service web peut prendre uniquement les valeurs numériques en tant que variables fonctionnalité (colonnes). La colonne « événement » peut prendre la valeur 0 ou 1. La colonne « time » doit être un nombre entier positif.

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

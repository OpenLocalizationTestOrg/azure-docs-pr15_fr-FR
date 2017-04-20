<properties 
    pageTitle="Réaliser des prévisions - ETS + STL | Microsoft Azure" 
    description="Réaliser des prévisions - ETS + STL" 
    services="machine-learning" 
    documentationCenter="" 
    authors="xueshanz" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="yijichen"/> 

#<a name="forecasting---ets--stl"></a>Réaliser des prévisions - ETS + STL  

Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/demand_forecast) met en œuvre des modèles saisonnier tendance décomposition (STL) et lissage exponentiel (ETS) pour produire des prédictions basées sur les données historiques fournies par l’utilisateur. La demande d’un produit spécifique augmente cette année ? Puis-je prévoir des ventes de mes produits pour Noël, afin que je peux planifier efficacement Mon inventaire ? Modèles de prévision peuvent répondre à ces questions. Étant donné les données passées, ces modèles d’examiner les tendances cachées et saisonnalité pour prévoir des tendances futures. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
 
>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.  
 
##<a name="consumption-of-web-service"></a>Consommation de service web 

Ce service accepte les 4 arguments et calcule les prévisions.
Les arguments d’entrée sont :

* Fréquence - indique la fréquence des données brutes (quotidien/hebdomadaire/mensuelle/trimestrielle/annuelle).
* Horizon - Future de prévision intervalle de temps.
* Date : ajoutez les nouvelles données de série de temps pour le temps.
* La valeur - ajouter les nouvelles valeurs de données de série de temps.

La sortie du service est les prévision et les valeurs calculées.
 
Exemple d’entrée pourrait être : 

* Fréquence - 12
* Horizon - 12
* Date - 1/15/2012 ; 2/15/2012 ; 3/15/2012 ; 4/15/2012 ; 5/15/2012 ; 6/15/2012 ; 7/15/2012 ; 8 / 15/2012 ; 9/15/2012 ; 10/15/2012 ; 11/15/2012 ; 12/15/2012 ; 1/15/2013 ; 2/15/2013 ; 3/15/2013 ; 4/15/2013 ; 5/15/2013 ; 6/15/2013 ; 7/15/2013 ; 8 / 15/2013 9/15/2013 15/10/2013 ; 15/11/2013 ; 15/12/2013 ; 15/1/2014 ; 2/15/2014 ; 3/15/2014 ; 4/15/2014 ; 5/15/2014 ; 6/15/2014 ; 7/15/2014 ; 8 / 15/2014 ; 15/9/2014
* Valeur - 3.479 ; 3.68 ; 3.832 ; 3.941 ; 3.797 ; 3.586 ; 3.508 ; 3.731 ; 3.915 ; 3.844 ; 3.634 ; 3.549 ; 3.557 ; 3.785 ; 3.782 ; 3.601 ; 3.544 ; 3.556 ; 3.65 ; 3.709 ; 3.682 ; 3.511 ; 3.429 3.51 ; 3.523 ; 3.525 ; 3.626 ; 3.695 ; 3.711 ; 3.711 ; 3.693 ; 3.571 ; 3.509

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :

    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };         var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


##<a name="creation-of-web-service"></a>Création du service web 

>Ce service web a été créé à l’aide d’apprentissage automatique Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication web services](machine-learning-publish-a-machine-learning-web-service.md), voir [azure.com/ml](http://azure.com/ml). Vous trouverez ci-dessous une capture d’écran de l’expérience créé le code de service et exemple web pour chacun des modules au sein de l’expérience.

À partir d’apprentissage automatique Azure, une nouvelle expérience vide a été créée. Exemple de données a été téléchargé avec un schéma de données prédéfinie. Lié au schéma de données est un [Exécuter le Script R] [ execute-r-script] module, qui génère STL et ETS modèles de prévision à l’aide de « stl', « ets » et « prévision » fonctionne sur R. 

###<a name="experiment-flow"></a>Flux expérience :

![Flux expérience][2]

####<a name="module-1"></a>Module 1 :
 
    # Add in the CSV file with the data in the format shown below 
![Exemples de données][3]   

####<a name="module-2"></a>Module 2 :

    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # Fit a time series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- stl(train_ts,  s.window="periodic")
    train_model <- forecast(fit1, h = data$horizon, method = 'ets')
    plot(train_model)
    
    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # Data output
    maml.mapOutputPort("data.forecast");

##<a name="limitations"></a>Limitations 

Il s’agit d’un exemple simple pour ETS + STL réaliser des prévisions. Comme le montre l’exemple de code ci-dessus, aucune interception d’erreurs n’est activée et le service suppose que toutes les variables sont des valeurs continue/positif et la fréquence doit être un nombre entier supérieur à 1. La longueur vecteur valeur de date et de doit être identique, et la longueur de la série chronologique doit être supérieure à 2 * fréquence. La variable date doit respecter au format « mm/jj/aaaa ».

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

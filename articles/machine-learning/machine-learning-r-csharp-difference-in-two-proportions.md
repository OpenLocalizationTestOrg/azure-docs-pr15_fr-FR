<properties 
    pageTitle="Différence de Test Proportions | Microsoft Azure" 
    description="Différence en Proportions Test" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Différence en Proportions Test


Les deux proportions statistique différents ? Supposons qu’un utilisateur souhaite comparer deux vidéos pour déterminer si un film a une proportion beaucoup plus élevée de « J’aime » quand par rapport à l’autre. Avec un échantillon de grande taille, vous pouvez rencontrer une différence statistique significative entre les proportions 0,50 et 0.51. Avec un petit exemple, il peut ne pas être suffisamment de données pour déterminer si ces proportions sont réellement différentes. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) effectue un test d’hypothèse de la différence entre deux proportions basée sur les informations utilisateur du nombre de succès et le nombre total de versions d’essai pour les groupes de 2 comparaison. Dans un scénario possible, ce service web peut être appelé à partir d’au sein d’une application de comparaison film, indiquant si un des films est vraiment 'aime » plus souvent que l’autre, en fonction des évaluations de vidéo à l’utilisateur.

>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.


##<a name="consumption-of-web-service"></a>Consommation de service web

Ce service accepte les 4 arguments et une hypothèse teste des proportions.

Les arguments d’entrée sont :

* Successes1 - nombre d’événements de succès dans l’échantillon 1.
* Successes2 - nombre d’événements de succès dans l’échantillon 2.
* Total1 - taille de l’échantillon 1.
* Total2 - taille de l’échantillon 2.

La sortie du service est le résultat de l’hypothèse tester ainsi que l’appel statistique, df, p-valeur et de pourcentage dans les limites de 1/2 et l’intervalle de confiance exemple.

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
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

À partir d’apprentissage automatique Azure, une nouvelle expérience vide a été créée avec deux [Exécuter le Script R] [ execute-r-script] modules. Dans le premier module que le schéma de données est défini, tandis que la seconde module utilise la commande prop.test de R pour effectuer le test hypothèse pour 2 proportions. 


###<a name="experiment-flow"></a>Flux expérience :

![Flux expérience][2]


####<a name="module-1"></a>Module 1 :
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Module 2 :

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Limitations 

Il s’agit d’un exemple très simple d’un test de la différence entre 2 proportions. Comme le montre l’exemple de code ci-dessus, aucune interception d’erreurs n’est activée et le service suppose que toutes les variables sont continues.

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

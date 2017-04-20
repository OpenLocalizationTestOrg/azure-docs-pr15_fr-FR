<properties 
    pageTitle="Suite de Service Web de Distribution normale | Microsoft Azure" 
    description="Suite de Service Web de distribution normale." 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 

#<a name="normal-distribution-suite"></a>Suite de la distribution normale.



La Suite de la Distribution normale est un ensemble de services web exemple ([Générateur]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Calculatrice Quantile]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculatrice de probabilité]( https://datamarket.azure.com/dataset/aml_labs/ndp5)) qui vous aider à générer et gérer la distribution normale. Les services permettent de générer une séquence de distribution normale de n’importe quelle longueur, le calcul étendue à partir d’une probabilité donnée et le calcul de probabilité à partir d’un quantile donné. Chacun des services émet des sorties différentes en fonction du service sélectionné (voir description ci-dessous). La Suite de la Distribution normale est basée sur le R fonctions qnorm, rnorm et pnorm, qui sont inclus dans le package statistiques R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.  
 

##<a name="consumption-of-web-service"></a>Consommation de service web
La Suite de Distribution normale inclut les services suivants 3.

###<a name="normal-distribution-quantile-calculator"></a>Calculatrice de Quantile Distribution normale
Ce service accepte 4 arguments d’une distribution normale et calcule la quantile associé.

Les arguments d’entrée sont :

* p - une seule probabilité d’un événement avec distribution normale. 
* Moyenne - moyenne distribution normale.
* SD - l’écart type distribution normale. 
* Côté - L du côté inférieur de la distribution et U pour le côté supérieur de la distribution.

La sortie du service est le quantile calculé associé à la probabilité donnée.

###<a name="normal-distribution-probability-calculator"></a>Calculatrice de probabilité de distribution normale.
Ce service accepte 4 arguments d’une distribution normale et calcule la probabilité associée.

Les arguments d’entrée sont :

* q : un quantile unique d’un événement avec distribution normale. 
* Moyenne - moyenne distribution normale.
* SD - l’écart type distribution normale. 
* Côté - L du côté inférieur de la distribution et U pour le côté supérieur de la distribution.

La sortie du service est la probabilité calculée associé à la quantile donnée.

###<a name="normal-distribution-generator"></a>Générateur de distribution normale.
Ce service accepte 3 arguments d’une distribution normale et génère une séquence aléatoire de nombres sont normalement distribué. Les arguments suivants doivent être qui lui sont fournies dans la demande :

* n - le nombre d’observations. 
* Moyenne - moyenne distribution normale.
* SD - l’écart type distribution normale. 

Le résultat du service est une séquence de longueur n avec une distribution normale basée sur les arguments moyenne et sd.

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (exemple applications sont ici : [Générateur](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Calculatrice de probabilité](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Calculatrice Quantile](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :

###<a name="normal-distribution-quantile-calculator"></a>Calculatrice de Quantile Distribution normale
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>Calculatrice de probabilité de distribution normale.
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>Générateur de distribution normale.
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Ce service web a été créé à l’aide d’apprentissage automatique Azure. Pour une version d’évaluation gratuite, ainsi que des vidéos d’introduction sur la création d’expériences et [publication web services](machine-learning-publish-a-machine-learning-web-service.md), voir [azure.com/ml](http://azure.com/ml). 

Vous trouverez ci-dessous une capture d’écran de l’expérience créé le code de service et exemple web pour chacun des modules au sein de l’expérience.

###<a name="normal-distribution-quantile-calculator"></a>Calculatrice de Quantile Distribution normale

Flux expérience :

![Flux expérience][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>Calculatrice de probabilité de distribution normale.
Flux expérience :

![Flux expérience][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>Générateur de distribution normale.
Flux expérience :

![Flux expérience][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitations 

Voici quelques exemples très simples qui l’entoure la distribution normale. Comme le montre l’exemple de code ci-dessus, peu interception des erreurs est activée.

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 

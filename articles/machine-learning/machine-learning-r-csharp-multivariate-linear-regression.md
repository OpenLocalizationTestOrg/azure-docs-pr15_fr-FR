<properties 
    pageTitle="Régression linéaire a variables | Microsoft Azure" 
    description="A variables régression linéaire." 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>A variables régression linéaire.   
 

 
Supposons que vous disposez d’un jeu de données et aimez prédire rapidement une variable dépendante (y) pour chaque personne (i) en fonction de variables indépendantes. Régression linéaire est une technique statistique populaires utilisée pour ces prévisions. Ici, la valeur y variable dépendante est supposé égal à une valeur continue.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Un scénario simple peut être l’endroit où l’Organise-notes tenter de prévoir l’épaisseur d’une personne (y) en fonction de leur hauteur (x). Un scénario plus avancé peut être l’endroit où l’Organise-notes contient des informations supplémentaires pour la personne (par exemple, poids, sexe, course) et tente de deviner l’épaisseur de la personne. Ce [service web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) correspond au modèle de régression linéaire aux données et renvoie la valeur prévue (y) pour chacun des observations dans les données.

>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.  

##<a name="consumption-of-web-service"></a>Consommation de service web  
Ce service web contient les valeurs prévues de la variable dépendante basée sur les variables indépendantes pour l’ensemble des observations. Le service web attend l’utilisateur final d’entrer des données sous forme de chaîne dans laquelle les lignes sont séparées par des virgules (,) et les colonnes sont séparées par un point-virgule ( ;). Le service web attend 1 ligne à la fois et attend la première colonne soit la variable dépendante. Un jeu de données exemple peut ressembler à ceci :

![Exemples de données][1]

Observations sans une variable dépendante doivent être entrées sous « NA » pour y. Les données d’entrée pour le jeu de données ci-dessus serait la chaîne suivante : « 10 ; 5 ; 2,18 ; 1 ; 6,6 ; 5.3 ; 2.1,7 ; 5 ; 5,22 ; 3 ; 4,12 ; 2 ; 1, NA ; 3 ; 4 ». Le résultat est la valeur prévue pour chacune des lignes selon les variables indépendantes. 

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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


À partir d’apprentissage automatique Azure, une nouvelle expérience vide a été créée et deux [Exécuter le Script R] [ execute-r-script] modules ont été extrait sur l’espace de travail. Ce service web s’exécute une expérience d’apprentissage automatique Azure avec un script R sous-jacente. Il existe 2 parties à cette expérience : définition de schéma et modèle de formation + score. Le premier module définit la structure attendue du dataset d’entrée, où la première variable est la variable dépendante et les autres variables sont indépendantes. Le second module correspond à un modèle de régression linéaire générique pour les données d’entrée.  
  
![Flux expérience][3]

####<a name="module-1"></a>Module 1 :
 
####<a name="schema-definition"></a>Définition de schéma  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Module 2 :
####<a name="lm-modeling"></a>Modélisation LM   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Limitations
Il s’agit d’un exemple très simple d’un service web de régression linéaire plusieurs. Comme le montre l’exemple de code ci-dessus, aucune interception d’erreurs n’est activée et le service suppose que tout est une variable continue (pas par catégorie de fonctionnalités autorisés), comme les valeurs numériques service uniquement les entrées au moment de la création de ce service web. En outre, le service actuellement gère taille des données limitée, d’échéance à la nature demande/réponse de l’appel du service web et du fait que le modèle est en cours correspondre chaque fois que le service web est appelé. 

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

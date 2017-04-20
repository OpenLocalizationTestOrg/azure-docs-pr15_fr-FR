<properties 
    pageTitle="Modèle de cluster | Microsoft Azure" 
    description="Modèle de cluster" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Modèle de cluster    

Comment pouvons prédire groupes des comportements des service bancaire afin de réduire les risques désactiver frais de carte de crédit émetteur ? Comment nous pouvons pour définir groupes des traits de personnalité des employés afin d’améliorer leurs performances au travail ? Comment les médecins pouvant classer les patients en groupes selon les caractéristiques de leurs maladies ? En principe, toutes ces questions peuvent être réponses via l’analyse du cluster.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Analyse cluster considère une série d’observations en deux ou plusieurs s’exclus mutuellement inconnues groupes en fonction des combinaisons de variables. L’objectif de l’analyse de cluster consiste à trouver un système d’organisation des observations, généralement personnes ou leurs caractéristiques, en groupes, où les membres des groupes ont des propriétés en commun. Ce [service](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utilise la méthodologie moyens K, une technique cluster couramment utilisée, aux données arbitraire cluster en groupes. Ce service web prend les données et le nombre de k clusters comme entrée et génère des prédictions dont des groupes k auquel appartient chaque observations. 

>Ce service web peut être utilisé par les utilisateurs – potentiellement par le biais d’une application mobile, via un site Web, ou même sur un ordinateur local, par exemple. Mais l’objectif du service web est également à utiliser comme illustre comment apprentissage automatique Azure peuvent servir à créer des services web en haut du code R. Avec quelques lignes de code R et les clics de bouton Azure Machine apprentissage Studio, une expérience peut être créée avec un code R et publiée sous la forme d’un service web. Le service web pouvant être publié sur le Azure Marketplace et utilisé par les utilisateurs et dispositifs dans le monde entier sans configuration infrastructure par l’auteur du service web.  

##<a name="consumption-of-web-service"></a>Consommation de service web   
Ce service web regroupe les données dans un ensemble de groupes k et renvoie l’affectation de groupe pour chaque ligne. Le service web attend l’utilisateur final d’entrer des données sous forme de chaîne dans laquelle les lignes sont séparées par des virgules (,) et les colonnes sont séparées par un point-virgule ( ;). Le service web attend 1 ligne à la fois. Un jeu de données exemple peut ressembler à ceci :

![Exemples de données][1]

Supposons que l’utilisateur souhaite séparer en 3 groupes qui s’exclus mutuellement. Les données d’entrée pour le jeu de données ci-dessus est le suivant : valeur = « 10 ; 5 ; 2,18 ; 1 ; 6,7 ; 5 ; 5,22 ; 3 ; 4,12 ; 2 ; 1,10 ; 3 ; 4 » ; k = « 3 ». Le résultat est l’appartenance aux groupes prévue pour chacune des lignes.

>Ce service, comme hébergé sur Azure Marketplace, est un service OData ; Il peuvent être appelés par le biais des méthodes POST ou GET. 

Il existe plusieurs manières d’utilisation du service de manière automatique (une application exemple est [ici](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Début code c# de consommation de service web :

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

À partir d’apprentissage automatique Azure, une nouvelle expérience vide a été créée et deux [Exécuter le Script R] [ execute-r-script] modules extraits sur l’espace de travail. Le schéma de données a été créé avec un simple [Exécuter le Script R][execute-r-script]. Ensuite, le schéma de données a été lié à la section modèle de cluster, créée à nouveau avec un [Exécuter le Script R][execute-r-script]. Dans le [Exécuter le Script R] [ execute-r-script] utilisé pour le modèle de cluster, le service web puis utilise la fonction « moyens k », qui est prédéfinie dans le [Exécuter le Script R] [ execute-r-script] d’apprentissage automatique Azure.    
   

     
![Flux expérience][3]

####<a name="module-1"></a>Module 1 : 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Module 2 :
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Limitations
Il s’agit d’un exemple très simple d’un service web cluster. Comme le montre l’exemple de code ci-dessus, aucune interception d’erreurs n’est activée et le service suppose que tout est une variable continue (pas par catégorie de fonctionnalités autorisés), comme les valeurs numériques service uniquement les entrées au moment de la création de ce service web. En outre, le service actuellement gère taille des données limitée, d’échéance à la nature demande/réponse de l’appel du service web et du fait que le modèle est en cours correspondre chaque fois que le service web est appelé. 

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché Azure, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

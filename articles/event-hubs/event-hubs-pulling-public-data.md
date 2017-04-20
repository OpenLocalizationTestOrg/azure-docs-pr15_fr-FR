<properties
    pageTitle="Extraire des données et des données publiques dans Azure événement Hubs | Microsoft Azure"
    description="Vue d’ensemble des événements centraux importer à partir d’exemple web"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>Extraire des données et des données publiques dans Azure événement Hubs

Dans les scénarios Internet des objets (IoT) classiques, vous avez périphériques que vous pouvez programmer à des données push pour Azure, soit à un concentrateur événement Azure ou à un concentrateur IoT. Les deux conditions ces hubs sont les points d’entrée dans Azure pour stocker, de l’analyse et de visualisation avec un large éventail d’outils mis à disposition sur Microsoft Azure. Toutefois, ils sont tous deux nécessitent la transmission des données à ces mises en forme en tant que JSON, sécurisé de manière spécifique. Cela affiche la question suivante. Que faire si vous souhaitez importer des données à partir de sources publics ou privés dans lequel les données sont exposées sous forme de service web ou flux quelconque, mais vous n’avez pas la possibilité de modifier la façon dont les données sont publiées ? Pensez à la météo, ou le trafic ou boursiers - vous ne savez pas NOAA, ou WSDOT ou NASDAQ pour configurer une diffusion sur votre plateforme d’événement. Pour résoudre ce problème, nous avons écrit et un échantillon cloud petite que vous pouvez modifier et déployer qui sera extraire les données à partir de certaines de ces sources et insérez-la à votre plateforme d’événement source ouverte. À partir de là, vous pouvez faire comme vous le souhaitez, objet, bien entendu, avec les termes de licence auprès du producteur. Vous pouvez trouver l’application [ici](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Notez que le code dans cet exemple illustre uniquement comment extraire des données à partir du web classique flux et comment écrire à un concentrateur événement Azure. Ce n’est pas destiné à être une application de production et aucune tentative n’ont été apportées aux adapter à utiliser dans un environnement de ce type, c’est strictfly un soi-même, exemple destinés aux développeurs uniquement. En outre, l’existence de cet exemple n'est-il pas équivalent à une recommandation doit **Extraire** des données dans Azure plutôt que de **push** . Vous consultez sécurité, les performances, les fonctionnalités et coût facteurs avant de s’installer sur une architecture de bout en bout.

## <a name="application-structure"></a>Structure de l’application

L’application est écrite en c# et la [description d’exemple](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contient toutes les informations que nécessaires pour modifier, créer et publier l’application. Les sections suivantes fournissent une vue d’ensemble de l’application.

Nous allons commencer en partant du principe que vous avez accès à une flux de données. Par exemple, vous souhaiterez peut-être extraire dans les données du trafic le service d’état de Washington du transport ou les données météorologiques NOAA, pour afficher des rapports personnalisés ou pour combiner des données à l’aide d’autres données dans votre application. Vous devez également avoir configuré un concentrateur événement Azure et connaître la connexion chaîne nécessaire pour y accéder.

Démarrage de la solution GenericWebToEH, il lit un fichier de configuration (App.config) pour obtenir un nombre d’éléments :

1. L’URL ou une liste d’URL pour le site de publication les données. Dans l’idéal, il s’agit d’un site qui publie des données dans JSON, tels que ceux référencées par WSDOT [ici](http://www.wsdot.wa.gov/Traffic/api/). 
2. Informations d’identification pour l’URL, si nécessaire. Nombreuses sources publiques n’est pas nécessaire d’informations d’identification, ou vous pouvez placer les informations d’identification dans la chaîne d’URL. D’autres requièrent fournis séparément. (Notez que vous pouvez uniquement spécifier un ensemble d’informations d’identification dans cette application, afin qu’il fonctionne uniquement si vous ne spécifiez qu’une seule URL, pas une liste d’URL).
3. La chaîne de connexion et le nom du concentrateur événement dans cet espace de noms Hubs de l’événement auquel vous fera les données. Ces informations sont disponibles dans le portail Azure.
4. Un intervalle de la mise en veille, en millisecondes, pour l’intervalle entre l’interrogation du site de données publiques. Ce paramètre nécessite réflexion. Si vous interrogez trop peu, vous pouvez rater des données ; en revanche, si vous interrogez trop fréquemment, vous pouvez obtenir un grand nombre de données répétitives ou pire encore, vous pouvez être bloqué en robot malveillant. Prendre en considération la fréquence de mise à jour de la source de données - données météorologiques ou le trafic peuvent être actualisés toutes les 15 minutes, mais stock devis peut-être après quelques secondes, en fonction de l’endroit où vous les obtenez. 
5. Un indicateur pour indiquer si les données arrive JSON ou XML à l’application. Dans la mesure où vous voulez déplacer les données vers un concentrateur de l’événement, l’application a un module pour convertir XML JSON avant l’envoi.

Après avoir lu le fichier de configuration, l’application est placé dans une boucle - accès au site web public, en convertissant les données si nécessaire, écriture à votre plateforme d’événement et d’attendre l’intervalle de mise en veille avant d’effectuer tout à nouveau. Plus précisément :

  * Lecture du site Web public. Pour recevoir des données prêt à envoyer l’instance de classe RawXMLWithHeaderToJsonReader est utilisé à partir de Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Il lit les flux de données source dans la méthode GetData() et puis fractionne à volumineux (c'est-à-dire records) à l’aide de GetXmlFromOriginalText. 
  Cette méthode lira XML ainsi que la forme correcte JSON ou JSON de tableau. Puis traitement est démarré à l’aide de la configuration MergeToXML d’App.config (par défaut = vide).
  * Les données de réception et d’envoi sont implémentées comme une boucle avec la méthode Process() dans Program.cs. 
  Après réception des résultats de sortie GetData(), la méthode file valeurs séparées par une pour le Hub de l’événement.

## <a name="next-steps"></a>Étapes suivantes

Pour déployer la solution, cloner ou télécharger l’application [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , modifiez le fichier App.config, créer et publier pour terminer. Une fois que vous avez publié l’application, vous pouvez voir s’exécuter dans le portail classique Azure sous Services Cloud, et vous pouvez modifier certains paramètres de configuration (par exemple, la cible du concentrateur de l’événement et l’intervalle de mise en veille) dans l’onglet **configurer** .

Voir les autres exemples événement Hubs dans la [Galerie d’exemples Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) et sur [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).

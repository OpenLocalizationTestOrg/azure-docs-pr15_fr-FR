<properties 
    pageTitle="Comment ajouter des unités de codage" 
    description="Découvrez comment ajouter des unités codage avec .NET"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Comment mettre à l’échelle codage avec .NET SDK

> [AZURE.SELECTOR]
- [Portail](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [RESTE](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Vue d’ensemble

>[AZURE.IMPORTANT] Veillez à passer en revue la rubrique [vue d’ensemble](media-services-scale-media-processing-overview.md) pour obtenir plus d’informations sur la mise à l’échelle media rubrique de traitement.
 
Pour modifier le type d’unité réservé et le nombre d’unités réservées à l’aide du Kit de développement .NET le codage, procédez comme suit :

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Ouverture d’un tickets de Support

Par défaut chaque compte Media Services peut évoluer jusqu'à 25 codage et 5 à la demande en continu réservés unités. Vous pouvez demander une limite supérieure en ouvrant un tickets de support.

###<a name="open-a-support-ticket"></a>Ouvrez une demande d’assistance

Pour ouvrir une prise en charge de tickets procédez comme suit :

1. Cliquez sur [obtenir une assistance technique](https://manage.windowsazure.com/?getsupport=true). Si vous n’êtes pas connecté, vous devrez entrer vos informations d’identification.

1. Sélectionnez votre abonnement.

1. Sous type de support, sélectionnez « Technique ».

1. Cliquez sur « Créer tickets ».

1. Sélectionnez « Azure Media Services » dans la liste de produits présentés dans la page suivante.

1. Sélectionnez un type de problème « » qui est appropriée pour votre problème.

1. Cliquez sur Continuer.

1. Suivez les instructions sur la page suivante, puis entrez les informations concernant votre problème.

1. Cliquez sur Envoyer pour ouvrir le bon.



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

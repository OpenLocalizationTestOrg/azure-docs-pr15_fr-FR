<properties
   pageTitle="Service Azure tissu sur Linux | Microsoft Azure"
   description="Service TISSU clusters prennent en charge Linux et Java, ce qui signifie que vous pourrez déployer et les applications de Service TISSU hôtes écrites en Java et c# sur Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>Service tissu sur Linux

L’aperçu du Service tissu sur Linux permet de générer, déployer et gérer les applications hautement disponibles, hautement scalable sur Linux comme vous le feriez sur Windows. Les structures de tissu de services (Services fiables et intervenants fiable) sont disponibles en Java sous Linux outre c# (.NET Core).  Vous pouvez également créer des [services exécutable invité](service-fabric-deploy-existing-app.md) avec une langue ou un cadre. En outre, l’aperçu prend également en charge des conteneurs Docker orchestrant ces opérations. Conteneurs docker peuvent exécuter exécutables invité ou services natifs tissu de Service, qui utilisent les structures de tissu de Service.

Service tissu sur Linux concept équivaut à tissu de Service sur Windows (à l’exception des caractéristiques du système d’exploitation et prise en charge du langage de programmation). Par conséquent, la plupart des notre [documentation existante](http://aka.ms/servicefabricdocs) s’applique pour vous aider à vous familiariser avec la technologie.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Systèmes d’exploitation et langages de programmation pris en charge

L’aperçu limité prend en charge la création de clusters de développement une case outre clusters comportant plusieurs ordinateurs dans Azure Ubuntu Server 16.04 en cours d’exécution. L’aperçu prend en charge les intervenants fiable et les structures de Services sans état fiable dans Java et c# outre invité exécutables et d’organiser des conteneurs Docker.  

>[AZURE.NOTE] Collections fiables non prises en charge Linux encore. Stand seuls clusters ne sont pas pris en charge soit - qu’une seule zone et clusters comportant plusieurs ordinateurs Linux Azure sont pris en charge dans l’aperçu.

## <a name="supported-tooling"></a>Outils pris en charge

L’aperçu prend en charge l’interaction avec le cluster via Azure infrastructure du langage commun. Pour les développeurs Java, intégration à Eclipse et Yeoman est fournie avec Eclipse pris en charge sur Linux et OSX. L’intégration OSX utilise un VM Linux les paramètres avancés via vagrant. Pour les développeurs c#, l’intégration à Yeoman est fournie pour générer des modèles d’application.

## <a name="next-steps"></a>Étapes suivantes


1. Se familiariser avec les structures de programmation [Intervenants fiable](service-fabric-reliable-actors-introduction.md) et [Services fiables](service-fabric-reliable-services-introduction.md) .

2. [Préparer votre environnement de développement sous Linux](service-fabric-get-started-linux.md)

3. [Préparer votre environnement de développement sur OSX](service-fabric-get-started-mac.md)

4. [Créer votre première application Service TISSU Java sous Linux](service-fabric-create-your-first-linux-application-with-java.md)

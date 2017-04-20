<properties
   pageTitle="Les exceptions FabricClient levées | Microsoft Azure"
   description="Décrit les exceptions courantes et les erreurs qui peuvent être levées par les FabricClient APIs lors de l’application et les opérations de gestion de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceptions et erreurs lorsque vous travaillez avec les FabricClient APIs courants
Les API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permettent aux administrateurs de cluster et d’application effectuer des tâches administratives sur un cluster, service ou application tissu de Service. Par exemple, le déploiement d’applications, mise à niveau et la suppression, contrôlant le fonctionnement un cluster, ou test d’un service. Les développeurs d’applications et administrateurs de cluster peuvent utiliser les FabricClient APIs pour développer des outils permettant de gérer le cluster à structure de Service et les applications.

Il existe de nombreux types d’opérations qui peuvent être effectuées à l’aide de FabricClient.  Chaque méthode peut lever des exceptions pour les erreurs en raison de saisie incorrecte, des erreurs d’exécution ou problèmes temporaires infrastructure.  Consultez la documentation de référence API pour rechercher les exceptions sont levées par une méthode spécifique. Il existe quelques exceptions près, cependant, qui peuvent être levées par de nombreuses API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) différents. Le tableau suivant répertorie les exceptions qui sont communes à FabricClient APIs.

|Exception| Levée lorsque|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|L’objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) est dans un état fermé. Supprimer l’objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) vous utilisez et instanciez d’un nouvel objet [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) . |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|L’opération a expiré. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) est retournée lorsque l’opération prend plus MaxOperationTimeout pour terminer.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|Échec de la vérification d’accès pour l’opération. E_ACCESSDENIED est renvoyé.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Une erreur d’exécution s’est produite lors de l’opération. Une des méthodes FabricClient susceptibles de lever [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), la propriété de [code d’erreur](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indique la cause exacte de l’exception. Codes d’erreur sont définis dans l’énumération [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|L’opération a échoué en raison d’une condition d’erreur transitoires quelconque. Par exemple, une opération peut échouer, car un quorum de réplicas n’est pas temporairement accessible. Exceptions transitoires correspondent aux opérations échecs pouvant être renvoyés.|

Des erreurs courantes [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) qui peuvent être retournés dans un [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Erreur| Condition|
|---------|:-----------|
|CommunicationError|Une erreur de communication dû l’opération à échouer, recommencez l’opération.|
|InvalidCredentialType|Le type d’informations d’identification n’est pas valide.|
|InvalidX509FindType|La X509FindType n’est pas valide.|
|InvalidX509StoreLocation|La X509 magasin n’est pas valide.|
|InvalidX509StoreName|La X509 enregistrer le nom n’est pas valide.|
|InvalidX509Thumbprint|Le X509 chaîne empreinte numérique du certificat n’est pas valide.|
|InvalidProtectionLevel|Le niveau de protection n’est pas valide.|
|InvalidX509Store|La X509 magasin de certificats ne peuvent pas être ouverts.|
|InvalidSubjectName|Le nom de sujet n’est pas valide.|
|InvalidAllowedCommonNameList|Le format de la chaîne de la liste nom commune n’est pas valide. Il doit être une liste séparée par des virgules.|

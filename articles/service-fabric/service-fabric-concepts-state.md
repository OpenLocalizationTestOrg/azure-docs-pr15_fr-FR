<properties
   pageTitle="Création et gestion de l’état | Microsoft Azure"
   description="Comment définir et gérer l’état de service dans tissu de Service"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="service-state"></a>État du service
**État du service** fait référence aux données dont le service a besoin pour fonctionner. Il inclut les structures de données et les variables qui le service lit et écrit votre travail.

Prenons un service de calculatrice simple, par exemple. Ce service prend deux nombres et retourne leur somme. Il s’agit d’un service purement sans état qui ne comporte aucune donnée associée.

Examinons maintenant la même Calculatrice, mais en plus de somme informatique, il possède également une méthode pour renvoyer la somme dernière qu'il a calculée. Ce service est à présent avec état--qu’il contient certains état dans lequel il écrit dans (lorsqu’il calcule une nouvelle somme) et le lit (quand elle renvoie la somme calculée dernier).

Dans Azure tissu de Service, le premier service est appelé service sans état. Le second service est appelé service dynamique.

## <a name="storing-service-state"></a>État du service de stockage
État peut être externalisé ou être hébergé avec le code est manipulation de l’état. Externalisation d’état est généralement effectuée à l’aide d’un magasin ou une base de données externe. Dans notre exemple de calculatrice, cela peut être une base de données SQL dans laquelle le résultat actuel est stocké dans un tableau. Chaque demande pour calculer la somme effectue une mise à jour sur cette ligne.

État peut également être situé au même endroit avec le code permettant de manipuler ce code. Services avec état dans tissu de Service sont créées à l’aide de ce modèle. Service TISSU fournit l’infrastructure pour s’assurer que cet état est hautement disponible et la tolérance en cas de panne.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts de tissu de Service, voir les rubriques suivantes :

- [Disponibilité des services de structure de Service](service-fabric-availability-services.md)

- [Extensibilité élevées de services de structure de Service](service-fabric-concepts-scalability.md)

- [Partition des services de structure de Service](service-fabric-concepts-partitioning.md)

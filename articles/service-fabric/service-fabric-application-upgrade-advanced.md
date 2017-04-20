<properties
   pageTitle="Mise à niveau de l’application : rubriques avancées | Microsoft Azure"
   description="Cet article décrit certaines rubriques avancées relatives à la mise à niveau une application de Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Mise à niveau de service TISSU application : rubriques avancées

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Ajout ou suppression des services durant une mise à niveau de l’application

Si un nouveau service est ajouté à l’application qui est déjà déployée, publiée sous la forme d’une mise à niveau, le nouveau service est ajouté à l’application déployée.  Une mise à niveau n’affecte pas les services qui ont été déjà partie de l’application. Toutefois, une instance du service qui a été ajouté doit être démarrée pour le nouveau service soit active (à l’aide de la `New-ServiceFabricService` applet de commande).

Services peuvent également être supprimés à partir d’une application dans le cadre d’une mise à niveau. Toutefois, toutes les instances du service à supprimés doivent être arrêtés avant de procéder à la mise à niveau (à l’aide de la `Remove-ServiceFabricService` applet de commande). 

## <a name="manual-upgrade-mode"></a>Mode de mise à niveau manuel

> [AZURE.NOTE]  Le mode manuel pas contrôlé à considérer que pour une mise à niveau a échoué ou suspendu. Le mode analysé est le mode de mise à niveau recommandé pour les applications de Service tissu.

TISSU Service Azure fournit plusieurs modes de mise à niveau pour prendre en charge des clusters de développement et de production. Options de déploiement choisies peuvent être différentes pour les différents environnements.

La mise à niveau propagée application contrôlée est la plus courante mise à niveau à utiliser en production. Lorsque la stratégie de mise à niveau est spécifiée, TISSU Service garantit que l’application est correcte avant le démarrage de la mise à niveau.

 L’administrateur d’application pouvez utiliser le mode de mise à niveau application propagée manuel pour avoir un contrôle total sur l’avancement de la mise à niveau via les différents domaines de mise à niveau. Ce mode est utile si une stratégie d’évaluation d’intégrité personnalisée ou complexe est requise, ou une mise à niveau non conventionnelle neuf (par exemple, l’application est déjà perte de données).

Enfin, la mise à niveau propagée application automatisée est utile pour le développement ou environnements de tests pour offrir un cycle d’itération rapide pendant le développement de service.

## <a name="change-to-manual-upgrade-mode"></a>Basculer en mode mise à niveau manuel
**Manuel**--arrêter la mise à niveau de l’application à la UD actuel et passer en mode de mise à niveau non contrôlée manuel. L’administrateur doit manuellement appeler **MoveNextApplicationUpgradeDomainAsync** pour procéder à la mise à niveau ou d’un déclencheur d’annulation en lançant une nouvelle mise à niveau. Une fois que la mise à niveau Active le mode manuel, il reste dans le mode manuel jusqu'à ce qu’une nouvelle mise à jour est lancée. La commande **GetApplicationUpgradeProgressAsync** renvoie TISSU\_APPLICATION\_mise à niveau\_état\_propagées\_transférer\_en attente.

## <a name="upgrade-with-a-diff-package"></a>Mettre à niveau avec un package de comparaison

Une application de Service TISSU peut être mis à niveau par mise en service avec un package d’application complète et autonome. Une application peut également être mis à niveau à l’aide d’un package de comparaison qui contient uniquement les fichiers d’application mis à jour, le manifeste d’application mis à jour et les fichiers manifeste du service.

Un package d’application complète contient tous les fichiers nécessaires pour démarrer et exécuter une application de Service tissu. Un package de comparaison contient uniquement les fichiers qui a été modifié entre la dernière disposition et la mise à niveau en cours, ainsi que le manifeste d’application complet et le service de fichiers manifeste. Toute référence dans le manifeste d’application ou service qui ne figurent pas dans la mise en page Créer est recherchée dans le magasin d’image.

Application complète packages sont requis pour la première installation d’une application pour le cluster. Mises à jour ultérieures peuvent être un package d’application complète ou un package de comparaison.

Occasions, lors de l’aide d’un package de comparaison serait un bon choix :

* Un package de comparaison est préférable lorsque vous disposez d’un package d’application de grande taille qui fait référence à plusieurs fichiers manifeste service et/ou plusieurs packages code, packages de configuration de l’ou les packages de données.

* Un package de comparaison est préférable lorsque vous disposez d’un système de déploiement qui génère la mise en page générer directement à partir de votre processus de génération application. Dans ce cas, même si le code n’a pas été modifié, assemblys nouvellement créés obtenir un total de contrôle différent. À l’aide d’un package d’application complète nécessite que vous mettez à jour de la version sur tous les modules de code. À l’aide d’un package de comparaison, vous ne fournissez les fichiers modifiés et les fichiers manifeste dans lequel la version a changé.

Lorsqu’une application est mise à niveau à l’aide de Visual Studio, le package de comparaison est publié automatiquement. Pour créer un package de comparaison manuellement, le manifeste d’application et les manifestes de service doivent être mis à jour, mais uniquement les packages modifiés doivent être inclus dans le package d’application final. 

Par exemple, commençons par l’application suivante (numéros de version fournis pour faciliter la compréhension) :

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

À présent, supposons que vous voulez mettre à jour uniquement le code package de service1 à l’aide d’un package de comparaison à l’aide de PowerShell. À présent, votre application mise à jour comporte la structure de dossiers suivants :

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Dans ce cas, vous mettez à jour le manifeste d’application 2.0.0 et le manifeste de service de service1 refléter la mise à jour du package de code. Le dossier pour votre package d’application aurait la structure suivante :

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Étapes suivantes

[La mise à niveau votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide dans une mise à niveau de l’application à l’aide de Visual Studio.

[La mise à niveau votre Application utilisation de Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide dans une mise à niveau de l’application à l’aide de PowerShell.

Contrôler la façon dont votre application met à niveau en utilisant des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Vérifiez les mises à niveau votre application compatibles en apprendre à utiliser la [Sérialisation de données](service-fabric-application-upgrade-data-serialization.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en faisant référence aux étapes de [Dépannage les mises à niveau de l’Application](service-fabric-application-upgrade-troubleshooting.md).
 

<properties
   pageTitle="Créer une règle basée sur le chemin d’accès pour une passerelle d’application à l’aide du portail | Microsoft Azure"
   description="Découvrez comment créer une règle basée sur le chemin d’accès pour une passerelle d’application à l’aide du portail"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Créer une règle basée sur le chemin d’accès pour une passerelle d’application à l’aide du portail

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-url-route-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-url-route-arm-ps.md)

Routage basé sur le chemin d’URL vous permet d’associer itinéraires basés sur le chemin d’URL de demande Http. Il vérifie s’il existe un itinéraire à un pool principale configuré pour les listes d’URL dans une Application passerelle et envoyer le trafic réseau vers le pool de principale défini. Souvent utilisée pour le routage des URL consiste à charger solde demandes de différents types de contenu pour des pools autre serveur principal.

Routage basé sur une URL présente un nouveau type de règle passerelle d’application. La passerelle application propose deux types de règles : les règles de base et basée sur le chemin d’accès. Type de règle base fournit un service cyclique pour les pools principale lors de règles basées sur le chemin d’accès en plus de distribution alternées, prend également motif chemin d’accès de l’URL de demande en considération lors du choix du pool principal.

## <a name="scenario"></a>Scénario

Le scénario suivant parcourt de création d’une règle basée sur le chemin d’accès dans une passerelle d’application existant.
Le scénario part du principe que vous avez déjà suivi les étapes pour [créer une passerelle d’Application](application-gateway-create-gateway-portal.md).

![itinéraire de l’URL][scenario]

## <a name="createrule"></a>Créer la règle basée sur le chemin d’accès

Une règle basée sur le chemin d’accès nécessite sa propre récepteur, avant de créer la règle n’oubliez pas de vérifier vous avez un récepteur disponible à l’utiliser.

### <a name="step-1"></a>Étape 1

Accédez à http://portal.azure.com et sélectionnez une passerelle d’application existant. Cliquez sur **règles**

![Vue d’ensemble de la passerelle application][1]

### <a name="step-2"></a>Étape 2

Cliquez sur bouton **basées sur le chemin d’accès** pour ajouter une règle basée sur le chemin d’accès.

### <a name="step-3"></a>Étape 3

La carte de **Ajouter une règle basée sur le chemin d’accès** comporte deux sections. La première section est l’endroit où vous avez défini le récepteur, le nom de la règle et les paramètres de chemin d’accès par défaut. Les paramètres de chemin d’accès par défaut sont appliquent aux itinéraires qui ne sont pas concernées par l’itinéraire personnalisé basé sur le chemin d’accès. La deuxième section de la cuillère **Ajouter une règle basée sur le chemin d’accès** est l’endroit où vous définissez les règles basées sur le chemin d’accès eux-mêmes.

**Paramètres de base**

- **Nom** : il s’agit d’un nom convivial pour la règle qui n’est accessible dans le portail.
- **Récepteur** - il s’agit que l’auditeur qui est utilisé pour la règle.
- **Regroupement d’arrière-plan par défaut** : ce paramètre est le paramètre qui définit la principale à utiliser pour la règle par défaut
- **Les paramètres par défaut HTTP** - ce paramètre est le paramètre qui définit les paramètres HTTP à utiliser pour la règle par défaut.

**Règles basées sur le chemin d’accès**

- **Nom** : il s’agit d’un nom convivial pour une règle basée sur le chemin d’accès.
- **Chemins d’accès** - ce paramètre définit le chemin d’accès de que la règle va rechercher pour transférer le trafic
- **Serveur principal Pool** : ce paramètre est le paramètre qui définit la principale à utiliser pour la règle
- **Paramètre HTTP** : ce paramètre est le paramètre qui définit les paramètres HTTP à utiliser pour la règle.

>[AZURE.IMPORTANT] Chemins d’accès : La liste des modèles de chemin d’accès pour mettre en correspondance. Chacun doit commencer par / et le seul endroit un «\*» est autorisé à la fin. Exemples valides sont /xyz, /xyz* ou /xyz/*.  

![Ajouter carte règle basée sur le chemin d’accès avec information complétée][2]

Ajout d’une règle basée sur le chemin d’accès à une passerelle d’application existant est un processus simple via le portail. Après avoir créé une règle basée sur le chemin d’accès, il peut être modifié pour ajouter facilement des règles supplémentaires. 

![Ajout de règles basées sur le chemin d’accès supplémentaires][3]

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à configurer SSL décharger avec Azure Application passerelle voir [Configurer décharger SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
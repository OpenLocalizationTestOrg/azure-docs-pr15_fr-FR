<properties
    pageTitle="Dépannage des contrôle d’accès basé sur un rôle | Microsoft Azure"
    description="Obtenir de l’aide des problèmes ou des questions sur les ressources de contrôle d’accès en fonction de rôle."
    services="azure-portal"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="kgremban"/>

# <a name="role-based-access-control-troubleshooting"></a>Dépannage de contrôle d’accès basé sur un rôle

## <a name="introduction"></a>Introduction

[Contrôle d’accès basé sur un rôle](role-based-access-control-configure.md) est une fonctionnalité puissante qui vous permet de déléguer l’accès permissions aux ressources dans Azure. Cela signifie que vous pouvez estimez confiant l’octroi d’une personne en particulier le droit d’utiliser exactement dont elles ont besoin et pas plus. Toutefois, dans certains cas, le modèle de ressources pour les ressources Azure peut être compliqué et il peut être difficile à comprendre exactement ce que vous accordez des autorisations à.

Ce document vous permettent de savoir ce qui se passe lorsque certains rôles d’à l’aide du portail Azure. Ces trois rôles couvrent tous les types de ressource :

- Propriétaire  
- Collaboration  
- Reader  

Les propriétaires et les collaborateurs ont un accès total à l’expérience de gestion, mais un collaborateur ne peut pas accéder à d’autres utilisateurs ou groupes. Les choses deviennent un peu plus intéressants avec le rôle de lecteur, c’est l’endroit où nous allons passer du temps. Consultez l' [article Démarrer de contrôle d’accès basé sur un rôle](role-based-access-control-configure.md) pour plus d’informations sur la façon d’accorder l’accès.

## <a name="app-service-workloads"></a>Charges de service d’application

### <a name="write-access-capabilities"></a>Fonctionnalités d’accès en écriture

Si vous accordez un accès en lecture seule utilisateur pour une application web unique, certaines fonctionnalités sont désactivées que vous ne pouvez pas vous y attendre. Les fonctionnalités de gestion suivantes requièrent un accès en **écriture** pour une application web (collaboration ou propriétaire) et ne seront pas disponibles dans tous les scénarios en lecture seule.

- Commandes (par exemple, Démarrer, arrêter, etc.)
- Modification des paramètres comme configuration générale, paramètres d’échelle, les paramètres de sauvegarde et paramètres de surveillance
- Accéder aux informations d’identification de publication et autres secrets tels que les paramètres de l’application et des chaînes de connexion
- Journaux de diffusion en continu
- Configuration des journaux de diagnostic
- Console (invite de commandes)
- Déploiements actives et récentes (pour le déploiement continue git local)
- Estimation des dépenses
- Tests de site Web
- Réseau virtuel (visible uniquement à un lecteur si un réseau virtuel a été configuré précédemment par un utilisateur possédant un accès en écriture).

Si vous ne pouvez pas accéder à un de ces mosaïques, vous devez demander à votre administrateur pour l’accès des collaborateurs à l’application web.

### <a name="dealing-with-related-resources"></a>En matière de gestion des ressources connexes

Applications Web sont compliquées par la présence de quelques différentes ressources interaction. Voici un groupe de ressources type avec deux sites Web :

![Groupe de ressources de l’application Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Par conséquent, si vous accordez l’accès à simplement l’application web, la plupart des fonctionnalités existant sur la carte de site Web dans le portail Azure sont désactivées.

Ces éléments nécessitent un accès en **écriture** pour le **plan de services d’application** qui correspond à votre site Web :  

- Affichage de l’application web de tarification couche (libre ou Standard)  
- Configuration de l’échelle (nombre d’instances de taille de la machine virtuelle, paramètres d’échelle automatique)  
- Quotas (stockage, la bande passante, processeur)  

Ces éléments nécessitent un accès en **écriture** à l’ensemble du **groupe de ressources** qui contient votre site Web :  

- Les certificats SSL et les liaisons (c’est parce que les certificats SSL peuvent être partagés entre sites dans le même groupe de ressources et géolocalisation)  
- Règles d’alerte  
- Paramètres d’échelle  
- Composants d’analyse des applications  
- Tests de site Web  

## <a name="virtual-machine-workloads"></a>Machine virtuelle les charges de travail

Beaucoup tels que les applications web, certaines fonctionnalités sur la carte machine virtuelle requièrent un accès en écriture à la machine virtuelle, ou à d’autres ressources dans le groupe de ressources.

Machines virtuelles sont liés aux noms de domaine, réseaux virtuels, comptes de stockage et règles d’alerte.

Ces éléments nécessitent un accès en **écriture** à la **machine virtuelle**:

- Points de terminaison  
- Adresses IP  
- Disques  
- Extensions  

Elles nécessitent un accès en **écriture** à la **machine virtuelle**et le **groupe de ressources** (ainsi que le nom de domaine) où il se trouve :  

- Jeu de disponibilité  
- Charger ensemble équilibrée  
- Règles d’alerte  

Si vous ne pouvez pas accéder à un de ces mosaïques, vous devez demander à votre administrateur pour l’accès collaborateur au groupe de ressources.

## <a name="see-more"></a>Afficher une plus grande
- [Contrôle d’accès en fonction de rôle](role-based-access-control-configure.md): prise en main RBAC dans le portail Azure.
- [Rôles intégrés](role-based-access-built-in-roles.md): obtenir des informations sur les rôles sont fournis par défaut dans la section RBAC.
- [Rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md): Apprenez à créer des rôles personnalisés pour répondre à vos besoins d’accès.
- [Créer un accès à un rapport historique des modifications](role-based-access-control-access-change-history-report.md): effectuer le suivi de modification attributions de rôle dans RBAC.

<properties
    pageTitle="Quelles sont les nouveautés dans Azure pile | Microsoft Azure"
    description="Quelles sont les nouveautés dans la pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Quelles sont les nouveautés dans Azure pile Technical Preview 2
Cette option offre de nouvelles fonctionnalités pour les clients et les administrateurs.

## <a name="network"></a>Réseau   
   - [les IDN](azure-stack-understanding-dns-in-tp2.md) fournit réseau interne l’enregistrement et la résolution du nom de domaine (DNS) sans infrastructure DNS supplémentaire.
   - [Les passerelles réseau virtuelles](azure-stack-create-vpn-connection-one-node-tp2.md) proposent des options de connectivité VPN aux ressources Azure ou en local.
   - Itinéraires définies par l’utilisateur peut router le trafic réseau via le pare-feu, la sécurité, d’autres appareils et d’autres services.
   - Vous pouvez créer des ressources réseau à partir de la place de marché.   

## <a name="storage"></a>Espace de stockage
 - [Files d’attente Azure](https://msdn.microsoft.com/library/dd179353.aspx) activer fiable et permanente service de messagerie.
 - [Analytique de stockage](https://msdn.microsoft.com/library/azure/hh343270.aspx) des données de performance stockage capture. Vous pouvez utiliser ces données à tracer les demandes, analyser les tendances d’utilisation et diagnostiquer les problèmes avec votre compte de stockage.
 - Stockage d’objets BLOB prend en charge le [bloc opération d’ajout](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Prise en charge de compte Premium API de stockage.
 - Prise en charge du service de stockage pour les outils et SDK, tels que Azure infrastructure du langage commun PowerShell, .NET, Python et Java SDK communs du client. 
 - [Stockage compte partagé Access Signature](https://msdn.microsoft.com/library/azure/mt584140.aspx) activer la délégation d’accès à vos services de stockage sans avoir à partager votre clé de compte complet.  
 - Services de stockage maintenant utilisent [Groupe les comptes de Service](https://technet.microsoft.com/library/hh831477.aspx) pour une sécurité renforcée avec des frais de gestion faible.
 - Vous pouvez récupérer client inutilisées ressources à la demande.
 - Longueur de rétention de compte de stockage supprimés peut être configuré.
 - Vous pouvez récupérer des comptes de stockage client supprimés.

## <a name="compute"></a>Cluster
- Vous pouvez libérer machines virtuelles.
- Vous pouvez redéployez extensions machine virtuelle fins de configuration et résolution des problèmes de gestion.
- Vous pouvez redimensionner machine virtuelle disques.
- Machines virtuelles peut avoir plusieurs interfaces réseau.

### <a name="portal-experience"></a>Utilisation du portail
 - Les régions pile Azure sont une unité d’échelle et gestion au sein de pile Azure logique. Dans cet aperçu, vous pouvez afficher les informations sur les services, tels que le cluster, de réseau et de stockage par région.
 - Vous pouvez désormais afficher un aperçu de l’interface [azure-pile-updates.md] (mises à jour).

## <a name="key-vault"></a>Archivage sécurisé clé
- [L’archivage sécurisé de clé dans une pile Azure](azure-stack-kv-intro.md) fournit la sécurisation de vos clés et les mots de passe pour les applications cloud.
- Vous pouvez auditer et surveiller l’utilisation de clés en applications et machines virtuelles.

## <a name="billing-and-usage"></a>Facturation et l’utilisation
 - [Facturation et consommation API](azure-stack-billing-and-chargeback.md) exposer des données sur la façon dont vos services sont utilisés.  
 - Fournisseurs déléguées activer des revendeurs de proposer vos services Azure pile à leurs clients.

## <a name="monitoring-and-health"></a>Surveillance et la santé
 - Nouvelles fonctionnalités disponibles dans le portail et API d’analyse autoriser le fait afficher et gérer les alertes dans votre environnement.  

## <a name="next-steps"></a>Étapes suivantes
- [Comprendre l’Architecture de contact pile Azure](azure-stack-architecture.md)      
- [Comprendre les conditions préalables au déploiement](azure-stack-deploy.md)
- [Déploiement d’Azure pile](azure-stack-run-powershell-script.md)

  

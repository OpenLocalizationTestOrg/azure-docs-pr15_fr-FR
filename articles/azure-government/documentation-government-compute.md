<properties
    pageTitle="Documentation pour le gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des recommandations sur le développement d’applications pour le gouvernement Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Pour le gouvernement Azure cluster

##  <a name="virtual-machines"></a>Machines virtuelles

Pour plus d’informations sur ce service et son utilisation, consultez [Azure Machines virtuelles tailles](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variantes

Les références de machine virtuelle suivantes sont généralement disponibles (disponibles) sur le secteur public Azure :

MACHINE VIRTUELLE RÉFÉRENCE (SKU)|États-Unis et pour le gouvernement VA|États-Unis et pour le gouvernement IA|Notes
---|---|---|---
A|DISPONIBILITÉ GÉNÉRALE|DISPONIBILITÉ GÉNÉRALE|Aucun
Dv1|DISPONIBILITÉ GÉNÉRALE|-|Aucun
DSv1|DISPONIBILITÉ GÉNÉRALE|-|Aucun
Dv2|DISPONIBILITÉ GÉNÉRALE|DISPONIBILITÉ GÉNÉRALE|15 sera bientôt disponible
F|DISPONIBILITÉ GÉNÉRALE|DISPONIBILITÉ GÉNÉRALE|Aucun
G|Planifiée|-|Aucun

###  <a name="data-considerations"></a>Considérations relatives à des données

Les informations suivantes identifient la limite pour le gouvernement Azure pour le Machines virtuelles Azure :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Les données entrées, stockées et traitées dans une machine virtuelle peut contenir des données contrôlé à l’exportation. Fichiers binaires ordinateurs au sein d’Azure virtuels. Authentificateurs statiques, tels que les mots de passe et des codes confidentiels carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Chaînes de connexion SQL.  Autres sécurité informations/secrets, tels que les certificats, les clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure.  | Métadonnées ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre Machine virtuelle Azure.  Ne saisissez réglementés/contrôlé des données dans les champs suivants : des noms de rôle, groupes de ressources, les noms de déploiement, noms des ressources, les balises de ressources du client  

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, vous abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure pour le gouvernement.</a>

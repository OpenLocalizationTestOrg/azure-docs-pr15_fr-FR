<properties
    pageTitle="Créer un rapport de l’historique des modifications access | Microsoft Azure"
    description="Générer un état qui répertorie toutes les modifications apportées à accéder à vos abonnements Azure avec contrôle d’accès basé sur un rôle au cours des 90 derniers jours."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="create-an-access-change-history-report"></a>Créer un rapport de l’historique des modifications access

Chaque fois que quelqu'un accorde ou refuse accès au sein de vos abonnements, l'accéder les modifications sont enregistrées dans les événements Azure. Vous pouvez créer des rapports sur l’historique access modifier pour afficher toutes les modifications apportées au cours des 90 derniers jours.

## <a name="create-a-report-with-azure-powershell"></a>Créer un rapport avec PowerShell Azure
Pour créer un rapport historique des modifications access dans PowerShell, utilisez la `Get-AzureRMAuthorizationChangeLog` commande. Plus d’informations sur cette applet de commande sont disponibles dans la [Galerie de PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Lorsque vous appelez cette commande, vous pouvez spécifier les propriétés des affectations qui doit apparaître, y compris les suivantes :

| Propriété | Description |
| -------- | ----------- |
| **Action** | Si access a été accordé ou refusé |
| **Appelant** | Le propriétaire de la modification de l’accès |
| **Date** | Date et heure d’accès a été modifié |
| **Nom du répertoire** | Le répertoire Azure Active Directory |
| **Au PrincipalName** | Le nom de l’utilisateur, un groupe ou une application |
| **PrincipalType** | Si l’affectation a été pour un utilisateur, un groupe ou une application |
| **RoleId** | GUID du rôle qui a été accordé ou refusé |
| **RoleName** | Le rôle qui a été accordé ou refusé |
| **ScopeName** | Le nom de l’abonnement, le groupe de ressources ou la ressource |
| **Type d’étendue** | Si l’affectation a été à l’abonnement, le groupe de ressources ou la portée de la ressource |
| **SubscriptionId** | GUID de l’abonnement Azure |
| **SubscriptionName** | Le nom de l’abonnement Azure |

Cet exemple de commande répertorie toutes les modifications d’accès de l’abonnement pour les sept derniers jours :

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - capture d’écran](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Créer un rapport avec Azure infrastructure du langage commun
Pour créer un état access modifier l’historique dans l’interface de ligne Azure (commande), utilisez la `azure role assignment changelog list` commande.

## <a name="export-to-a-spreadsheet"></a>Exporter vers une feuille de calcul
Pour enregistrer le rapport, ou manipuler les données, exporter le niveau d’accès des modifications dans un fichier .csv. Vous pouvez ensuite afficher le rapport dans une feuille de calcul pour révision.

![ChangeLog affiché sous la forme d’une feuille de calcul - capture d’écran](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="see-also"></a>Voir aussi
- Prise en main [Azure Role-Based le contrôle d’accès](role-based-access-control-configure.md)
- Utiliser les [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md)

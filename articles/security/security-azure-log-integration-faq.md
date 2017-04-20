<properties
   pageTitle="Intégration du journal Azure FAQ | Microsoft Azure"
   description="Ce forum aux questions répond aux questions sur l’intégration de journal Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Intégration du journal Azure Forum aux questions (FAQ)

Ce forum aux questions répond aux questions sur l’intégration de journal Azure, un service qui vous permet d’intégrer des journaux brutes à partir de vos ressources Azure dans vos systèmes locaux les informations de sécurité et de gestion des événements (SIEM). Cette intégration fournit un tableau de bord unifiée pour tous vos actifs, en local ou dans le cloud, afin que vous pouvez agréger, corrélation, analyser et d’alerte pour les événements de sécurité associées à vos applications.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Comment puis-je afficher les comptes de stockage à partir de laquelle l’intégration de journal Azure est tirant journaux Azure machine virtuelle à partir de ?

Exécuter la commande **liste source azlog**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Comment puis-je mettre à jour la configuration du proxy ?

Si vos paramètres de proxy n’autorise pas l’accès au stockage Azure directement, ouvrez la **AZLOG. EXE. Configuration de la** fichier dans **c:\Program Files\Microsoft Azure journal d’intégration**. Mettre à jour le fichier pour inclure la section **defaultProxy** avec l’adresse proxy de votre organisation. Une fois la mise à jour terminée, arrêtez et démarrer le service à l’aide des commandes **azlog net stop** et **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Comment puis-je afficher les informations d’abonnement dans les événements de Windows ?

Ajouter le **subscriptionid** au nom convivial lors de l’ajout de la source.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

L’événement XML comporte les métadonnées comme indiqué ci-dessous, y compris l’id de l’abonnement.

![Événement XML][1]

## <a name="error-messages"></a>Messages d’erreur

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Lorsque vous exécutez la commande **azlog createazureid**, je reçois l’erreur suivante ?

Erreur :

  *Impossible de créer l’Application AAD - client 72f988bf-86f1-41af-91ab-2d7cd011db37-motif = 'Interdit' - Message = 'Privilèges insuffisantes pour terminer l’opération.'*

**Azlog createazureid** essaie de créer une entité de service dans toutes les installations de Azure AD pour les abonnements sur lequel la connexion Azure a accès à. Si votre connexion Azure n’est seulement un utilisateur invité dans ce client Azure AD, puis la commande échoue avec « Des autorisations suffisantes pour terminer l’opération. » Demander l’administrateur client pour ajouter votre compte en tant qu’utilisateur dans le client.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Lorsque vous exécutez la commande **azlog autoriser**, je reçois l’erreur suivante ?

Erreur :

  *Avertissement création affectation de rôles - AuthorizationFailed : le client janedo@microsoft.com' avec objet id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' ne dispose pas d’autorisation pour effectuer l’action 'Microsoft.Authorization/roleAssignments/write' sur étendue '/ abonnements/70 d 95299-d689-4c 97-b971-0d8ff0000000'.*

Commande **Azlog autoriser** attribue le rôle de lecteur au service Azure AD principal (créé avec **Azlog createazureid**) pour les abonnements fournis. Si la connexion Azure n’est pas un administrateur de co-création ou un propriétaire de l’abonnement, il échoue avec le message d’erreur « Échec de l’autorisation ». Accès basé sur un rôle Azure contrôle de co-création administrateur ou propriétaire est nécessaire pour terminer cette action.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Où puis-je trouver la définition des propriétés dans le journal d’audit ?

Voir :

- [Opérations d’audit avec le Gestionnaire de ressources](../resource-group-audit.md)
- [Répertorier les événements de gestion dans un abonnement dans l’API REST Azure moniteur](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Où puis-je trouver plus d’informations sur les alertes du centre de sécurité Azure ?

Voir [gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Comment puis-je modifier de ce que sont collecté par les diagnostics de mémoire virtuelle ?

Voir [Utiliser PowerShell pour activer des Diagnostics sur une machine virtuelle exécutant Windows Azure](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) pour savoir comment les obtenir, modifier et définir les Diagnostics Azure dans Windows *(WAD)* configuration. Voici un exemple :

### <a name="get-the-wad-config"></a>Obtenir la configuration de la WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modifier le fichier Config WAD

L’exemple suivant est une configuration où uniquement EventID 4624 et EventId 4625 sont collectés dans le journal des événements sécurité. Événements Microsoft Antimalware sont collectées à partir de journal des événements système. Consultez [consommation d’événements] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) pour plus d’informations sur l’utilisation d’expressions XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Définir la configuration WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Après avoir apporté des modifications, vérifiez le compte de stockage pour vous assurer que les événements correctes sont collectées.

Si vous avez des questions sur l’intégration de journal Azure, envoyez un message électronique à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

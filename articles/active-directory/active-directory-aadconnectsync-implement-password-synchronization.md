<properties
    pageTitle="Mise en œuvre la synchronisation de mot de passe avec synchronisation Azure AD Connect | Microsoft Azure"
    description="Fournit des informations sur le fonctionnement de la synchronisation de mot de passe et comment l’activer."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Mise en œuvre la synchronisation de mot de passe avec synchronisation Azure AD Connect
Cette rubrique vous offre les informations nécessaires synchroniser vos mots de passe utilisateur à partir d’un local Active Directory (AD) à un nuage Azure Active Directory (AD Azure).

## <a name="what-is-password-synchronization"></a>Quelle est la synchronisation de mot de passe
La probabilité que vous êtes bloqué à partir de la réalisation de votre travail en raison d’un mot de passe oublié est lié au nombre de mots de passe différents n’oubliez pas. Les mots de passe plus que vous devrez vous rappeler, plus la probabilité d’une oublier. Questions et les appels à propos des mots de passe et d’autres problèmes liés à un mot de passe exigent la majorité des ressources de support technique.

Synchronisation de mot de passe est une fonctionnalité pour la synchronisation des mots de passe utilisateur dans Active Directory local à un nuage Azure Active Directory (AD Azure).
Cette fonctionnalité permet de vous connecter aux services Azure Active Directory (par exemple, Office 365, Microsoft Intune, CRM Online et Azure Active Directory Domain Services) à l’aide de la même mot de passe que vous utilisez pour vous connecter à Active Directory local.

![Qu’est Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

En réduisant le nombre de mots de passe que vos utilisateurs ont besoin pour mettre à jour la seule, la synchronisation de mot de passe vous aide à :

- Améliorer la productivité de vos utilisateurs
- Réduire les coûts de support technique  

En outre, si vous choisissez d’utiliser la [**fédération avec AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), vous pouvez activer la synchronisation de mot de passe comme une sauvegarde en cas d’échec de votre infrastructure AD FS.

Synchronisation de mot de passe est une extension de la fonctionnalité de synchronisation d’annuaire implémentée par synchronisation Azure AD Connect. Pour utiliser la synchronisation de mot de passe dans votre environnement, vous devez :

- Installation Azure AD se connecter  
- Configurer la synchronisation d’annuaire entre votre locaux AD et Azure Active Directory
- Activer la synchronisation de mot de passe

Pour plus d’informations, voir [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] Pour plus d’informations sur les Services de domaine Active Directory qui sont configurés pour la synchronisation FIPS et mot de passe, voir [synchronisation de mot de passe et FIPS](#password-synchronization-and-fips).

## <a name="how-password-synchronization-works"></a>Fonctionnement de la synchronisation de mot de passe
Le service de domaine Active Directory stocke les mots de passe sous forme d’une représentation de valeur hachée du mot de passe utilisateur réel. Une valeur de hachage est un résultat d’une fonction mathématique à sens unique (le «*algorithme de hachage*»). Il n’existe aucune méthode pour rétablir le résultat d’une fonction à sens unique à la version au format texte brut d’un mot de passe. Vous ne pouvez pas utiliser un hachage de mot de passe pour vous connecter à votre réseau local.

Pour synchroniser votre mot de passe, synchronisation Azure AD Connect extrait hachage votre mot de passe Active Directory local. Traitement de sécurité supplémentaire est appliquée au hachage du mot de passe avant qu’il est synchronisé avec le service d’authentification Azure Active Directory. Les mots de passe sont synchronisés sur une base par utilisateur et dans l’ordre chronologique.

Le flux de données réelles du processus de synchronisation de mot de passe est similaire à la synchronisation des données utilisateur telles que nom complet ou adresses de messagerie. Toutefois, les mots de passe sont synchronisés plus fréquemment que la fenêtre de synchronisation d’annuaire standard pour d’autres attributs. Le processus de synchronisation de mot de passe s’exécute toutes les 2 minutes. Vous ne pouvez pas modifier la fréquence de ce processus. Lorsque vous synchronisez un mot de passe, il remplace le mot de passe cloud existant.

La première fois, vous activez la fonctionnalité de synchronisation de mot de passe, elle effectue une synchronisation initiale des mots de passe de tous les utilisateurs dans l’étendue. Vous ne pouvez pas définir explicitement un sous-ensemble de mots de passe utilisateur que vous voulez synchroniser.

Lorsque vous modifiez un mot de passe en local, le mot de passe mis à jour est synchronisé, la plupart du temps en quelques minutes.
La fonctionnalité de synchronisation de mot de passe tente automatiquement de renvoyer tentatives de synchronisation qui a échoué. Si une erreur se produit au cours d’une tentative de synchronisation d’un mot de passe, une erreur est enregistrée dans votre Observateur d’événements.

La synchronisation d’un mot de passe n’a aucun impact sur l’utilisateur connecté.
Votre session de service cloud actuelle n’est pas immédiatement affectée par une modification de mot de passe synchronisé qui se produit lorsque vous êtes connecté à un service cloud. Toutefois, lorsque le service en nuage requiert de s’authentifier à nouveau, vous devez fournir votre nouveau mot de passe.

> [AZURE.NOTE] Synchronisation de mot de passe est uniquement prise en charge pour l’utilisateur de type d’objet dans Active Directory. Il n’est pas pris en charge pour le type d’objet iNetOrgPerson.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Fonctionnement de la synchronisation de mot de passe avec les Services de domaine Active Directory Azure
Vous pouvez également utiliser la fonctionnalité de synchronisation de mot de passe pour synchroniser vos mots de passe locales aux [Services de domaine Active Directory Azure](../active-directory-domain-services/active-directory-ds-overview.md). Ce scénario permet les Services de domaine Active Directory Azure pour vous authentifier vos utilisateurs dans le nuage avec toutes les méthodes disponibles dans vos locaux AD. L’expérience de ce scénario est similaire à l’aide de l’outil de Migration Active Directory (ADMT) dans un environnement local.

### <a name="security-considerations"></a>Considérations sur la sécurité
Lors de la synchronisation des mots de passe, la version en texte brut de votre mot de passe n’est pas exposée à la fonctionnalité de synchronisation de mot de passe, à Azure Active Directory, ou un des services associés.

En outre, il n’est pas obligatoire dans Active Directory local pour stocker le mot de passe dans un format réversible. Un résumé du hachage de mot de passe Active Directory est utilisé pour la transmission entre les locaux AD et Azure Active Directory. Le résumé du hachage de mot de passe ne peuvent pas être utilisé pour accéder aux ressources dans votre environnement local.

### <a name="password-policy-considerations"></a>Remarques sur les stratégies de mot de passe
Il existe deux types de stratégies de mot de passe qui sont affectés par l’activation de la synchronisation de mot de passe :

1. Stratégie de complexité de mot de passe
2. Stratégie d’Expiration de mot de passe

**Stratégie de complexité des mots de passe**  
Lorsque vous activez la synchronisation de mot de passe, les stratégies de la complexité de mot de passe dans Active Directory local remplacent les stratégies de complexité des mots dans le cloud pour les utilisateurs synchronisés. Vous pouvez utiliser tous les mots de passe valides d’Active Directory local pour accéder aux services Azure AD.

> [AZURE.NOTE] Les mots de passe pour les utilisateurs sont créés directement dans le cloud sont toujours soumis à des stratégies de mot de passe, telle que définie dans le cloud.

**Stratégie d’expiration de mot de passe**  
Si un utilisateur se trouve dans l’étendue de la synchronisation de mot de passe, le mot de passe du compte cloud est défini sur «*N’Expire jamais*».
Vous pouvez continuer à se connecter à vos services cloud à l’aide d’un mot de passe synchronisée qui a expiré dans votre environnement local. Votre mot de passe cloud est mis à jour la prochaine fois que vous modifiez le mot de passe dans l’environnement local.

### <a name="overwriting-synchronized-passwords"></a>Remplacement de synchroniser les mots de passe
Un administrateur peut réinitialiser manuellement votre mot de passe à l’aide de Windows PowerShell.

Dans ce cas, le nouveau mot de passe substitue votre mot de passe synchronisé et toutes les stratégies de mot de passe définis dans le cloud sont appliqués au nouveau mot de passe.

Si vous modifiez votre mot de passe local à nouveau, nouveau mot de passe est synchronisé avec le cloud et remplace le mot de passe mis à jour manuellement.

## <a name="enabling-password-synchronization"></a>Activation de la synchronisation de mot de passe
Synchronisation de mot de passe est activée automatiquement lorsque vous installez Azure AD Connect à l’aide de la **Configuration rapide**. Pour plus d’informations, voir [mise en route d’Azure AD Connect à l’aide de paramètres express](./connect/active-directory-aadconnect-get-started-express.md).

Si vous utilisez des paramètres personnalisés lorsque vous installez Azure AD Connect, vous activez la synchronisation de mot de passe dans la page de connexion utilisateur. Pour plus d’informations, voir [installation personnalisée de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

![Activation de la synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Synchronisation de mot de passe et FIPS
Si votre serveur a été verrouillé en fonction des informations Standard FIPS (Federal Processing), MD5 a été désactivé.

**Pour activer MD5 pour la synchronisation de mot de passe, effectuez les opérations suivantes :**

1. Accédez à **Sync\Bin %programfiles%\Azure AD**.
2. Ouvrez **miiserver.exe.config**.
3. Accédez au nœud **configuration/runtime** (à la fin du fichier).
4. Ajouter le nœud suivant :`<enforceFIPSPolicy enabled="false"/>`
5. Enregistrez vos modifications.

Pour référence, cette capture est comment il doit ressembler à :

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Pour plus d’informations sur la sécurité et FIPS voir [conformité AAD Sync de mot de passe, le chiffrement et FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Résolution des problèmes de synchronisation de mot de passe
Si les mots de passe ne se synchronisent pas comme prévu, il peut s’agir d’un sous-ensemble d’utilisateurs ou pour tous les utilisateurs.

- Si vous avez un problème avec les objets individuels, consultez [résoudre les problèmes un objet qui ne se synchronise pas les mots de passe](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Si vous avez un problème lesquelles aucun mot de passe n’est synchronisés, consultez [résolution des problèmes lesquelles aucun mot de passe n’est synchronisés](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Résoudre les problèmes d’un objet qui ne se synchronise pas les mots de passe
Vous pouvez facilement résoudre les problèmes de synchronisation de mot de passe en passant en revue le statut d’un objet.

Démarrer dans **utilisateurs et ordinateurs Active Directory**. Recherchez l’utilisateur et vérifiez que le **Qu'utilisateur doit changer le mot de passe à la prochaine ouverture de session** est désactivée.
![Les mots de passe productifs actives Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Si elle est activée, puis demandez à l’utilisateur pour se connecter et modifier le mot de passe. Les mots de passe temporaires ne sont pas synchronisés avec Azure AD.

Si elle est à votre goût dans Active Directory, l’étape suivante consiste à suivre l’utilisateur dans le moteur de synchronisation. En suivant l’utilisateur dans Active Directory local vers Azure Active Directory, vous pouvez voir s’il existe une erreur descriptif sur l’objet.

1. Démarrez le **[Gestionnaire de services de synchronisation](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Cliquez sur **les connecteurs**.
3. Sélectionnez le **Connecteur Active Directory** de l’utilisateur se trouve dans.
4. Sélectionnez **l’espace de connecteur de recherche**.
5. Recherchez l’utilisateur que vous recherchez.
6. Sélectionnez l’onglet **lignage** et vérifiez qu’au moins une règle de synchronisation affiche la **Synchronisation de mot de passe** en tant que **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **dans à partir d’AD - utilisateur AccountEnabled**.  
    ![Informations de lignage sur un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. Puis [Suivez l’utilisateur](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) par le biais du métaverse à l’espace le connecteur Azure Active Directory. L’objet espace connecteur doit avoir une règle sortante avec la **Synchronisation de mot de passe** défini sur **True**. Dans la configuration par défaut, le nom de la règle de synchronisation est **les AAD - utilisateur joindre**.  
    ![Propriétés de l’espace connecteur d’un utilisateur](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. Pour voir les détails de la synchronisation de mot de passe de l’objet de la semaine précédente, cliquez sur **journal...**.  
    ![Détails du journal objet](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

La colonne statut peut avoir les valeurs suivantes :

État | Description
---- | -----
Opération réussie | Mot de passe a été synchronisé avec succès.
FilteredByTarget | Mot de passe est configuré pour **l’utilisateur doit changer le mot de passe à la prochaine ouverture de session**. Mot de passe n’a pas été synchronisé.
NoTargetConnection | Aucun objet dans le méta-verse ou de l’espace de connecteur Azure AD.
SourceConnectorNotPresent | Aucun objet trouvé dans l’espace de connecteur Active Directory local.
TargetNotExportedToDirectory | L’objet dans l’espace de connecteur Azure AD n’a pas encore été exporté.
MigratedCheckDetailsForMoreInfo | Entrée de journal créée avant de générer 1.0.9125.0 et est présentée dans son état héritée.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Résoudre les problèmes où aucun mot de passe n’est synchronisés
Démarrez en exécutant le script dans la section [obtenir le statut de paramètres de synchronisation de mot de passe](#get-the-status-of-password-sync-settings). Il vous donne un aperçu de la configuration de la synchronisation de mot de passe.  
![Sortie du script PowerShell à partir des paramètres de synchronisation de mot de passe](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Si la fonctionnalité n’est pas activée dans Azure AD ou si l’état de canal synchronisation n’est pas activée, puis exécutez l’Assistant installation de se connecter. Sélectionnez **Personnaliser les options de synchronisation** et annuler la sélection de synchronisation de mot de passe. Cette modification désactive temporairement la fonctionnalité. Réexécutez l’Assistant, puis réactivez synchronisation de mot de passe. Exécuter le script à nouveau pour vérifier que la configuration est correcte.

Si le script indique qu’il n’existe aucune pulsation, puis exécutez-le le script de [déclencher une synchronisation complète de tous les mots de passe](#trigger-a-full-sync-of-all-passwords). Ce script peut également être utilisé pour d’autres scénarios où la configuration est correcte, mais les mots de passe ne sont pas synchronisés.

#### <a name="get-the-status-of-password-sync-settings"></a>Obtenir le statut de paramètres de synchronisation de mot de passe

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Déclencher une synchronisation complète de tous les mots de passe
Vous pouvez déclencher une synchronisation complète de tous les mots de passe en utilisant le script suivant :

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Étapes suivantes

* [Synchronisation Azure Active Directory se connecter : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

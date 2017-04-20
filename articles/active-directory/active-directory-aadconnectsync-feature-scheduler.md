<properties
   pageTitle="Azure AD Connect synchronisation : Scheduler | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité Planificateur intégré dans Azure AD Connect synchroniser."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect synchronisation : Scheduler
Cette rubrique décrit le Planificateur intégré dans synchronisation Azure AD Connect (également appelé moteur de synchronisation).

Cette fonctionnalité a été introduite avec génération 1.1.105.0 (publiée février 2016).

## <a name="overview"></a>Vue d’ensemble
Azure AD Connect synchronisation synchroniser les modifications effectuées dans votre annuaire local à l’aide d’un planificateur. Il existe deux processus scheduler, une pour la synchronisation de mot de passe et une autre pour synchroniser/attribut d’objet et des tâches de maintenance. Cette rubrique couvre ce dernier.

Dans les versions antérieures, le planificateur des objets et attributs était externe au moteur de synchronisation et le Planificateur de tâches Windows ou un service Windows distinct a été utilisé pour déclencher le processus de synchronisation. Le planificateur est avec 1.1 versions prédéfinis pour la synchronisation du moteur et n’autorisent les personnaliser. La nouvelle fréquence de synchronisation par défaut est de 30 minutes.

Le planificateur est chargé de deux tâches :

- **Cycle de synchronisation**. Procédure à suivre pour importer, synchronisez et exporter les modifications apportées.
- **Tâches de maintenance**. Renouveler clés et des certificats pour la réinitialisation de mot de passe et appareil d’inscription Service (DRS). Vider anciennes entrées dans le journal des opérations.

Le planificateur elle-même est toujours en cours d’exécution, mais il peut être configuré pour exécuter uniquement une ou aucune de ces tâches. Par exemple si vous avez besoin pour que votre propre processus de cycle de synchronisation, vous pouvez désactiver cette tâche dans le planificateur mais toujours exécuter la tâche de maintenance.

## <a name="scheduler-configuration"></a>Configuration du planificateur
Pour afficher vos paramètres de configuration en cours, accédez à PowerShell et exécuter `Get-ADSyncScheduler`. Il vous montrent ressembler à ceci :

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Si vous voyez **la commande de synchronisation ou l’applet de commande n’est pas disponible** lorsque vous exécutez cette applet de commande, le module PowerShell n’est pas chargé. Cela peut se produire si vous exécutez Azure AD Connect sur un contrôleur de domaine ou sur un serveur avec des niveaux de restriction PowerShell supérieurs à ceux de paramètres par défaut. Si vous voyez cette erreur, puis exécutez `Import-Module ADSync` pour libérer de l’applet de commande.

- **AllowedSyncCycleInterval**. Le plus souvent Azure AD permettra synchronisations. Vous ne pouvez pas synchroniser plus fréquemment et toujours être pris en charge.
- **CurrentlyEffectiveSyncCycleInterval**. La planification actuellement en vigueur. Il aura la même valeur que CustomizedSyncInterval (si la valeur) si ce n’est plus fréquent que AllowedSyncInterval. Si vous changez de CustomizedSyncCycleInterval, cela prend effet après la prochaine synchronisation.
- **CustomizedSyncCycleInterval**. Si vous souhaitez que le planificateur d’exécuter à n’importe quel autre fréquence de celle de la valeur par défaut 30 minutes, vous allez configurer ce paramètre. Dans l’image ci-dessus le planificateur a été défini pour exécuter chaque heure à la place. Si vous choisissez une valeur inférieure à AllowedSyncInterval, ces derniers seront utilisées.
- **NextSyncCyclePolicyType**. Delta ou initiale. Définit si la prochaine exécution ne doit que des modifications delta processus, ou si la prochaine exécution doit faire un plein importer et synchroniser, qui aurait également traiter de nouveau toutes les règles nouvelles ou modifiées.
- **NextSyncCycleStartTimeInUTC**. Prochaine fois que le planificateur commence la prochaine synchronisation.
- **PurgeRunHistoryInterval**. L’heure opération journaux doivent être conservés. Il peuvent être consultées dans le Gestionnaire de services de synchronisation. La valeur par défaut est de conserver ces pendant 7 jours.
- **SyncCycleEnabled**. Indique si le planificateur exécute l’importation, la synchronisation et le processus d’exportation en tant que composant de son fonctionnement.
- **MaintenanceEnabled**. Indique si le processus de maintenance est activé. Il met à jour les certificats/clés et vider le journal des opérations.
- **IsStagingModeEnabled**. Indique si [les intermédiaires mode](active-directory-aadconnectsync-operations.md#staging-mode) est activée.

Vous pouvez modifier certaines de ces paramètres avec `Set-ADSyncScheduler`. Vous pouvez modifier les paramètres suivants :

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

La configuration du planificateur est stockée dans AD Azure. Si vous avez un serveur intermédiaire, toute modification sur le serveur principal aura également un effet sur le serveur intermédiaire (à l’exception de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxe :`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - jours, HH - heures, mm - minutes, ss - secondes

Exemple :`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Remplacera le planificateur d’exécuter toutes les 3 heures.

Exemple :`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Remplacera le planificateur d’exécuter quotidiennement.

## <a name="start-the-scheduler"></a>Démarrer le planificateur
Le planificateur exécutera par défaut toutes les 30 minutes. Dans certains cas, vous souhaiterez peut-être exécuter un cycle de synchronisation entre les cycles planifiées ou requis pour exécuter un type différent.

**Cycle de synchronisation delta**  
Un cycle de synchronisation delta inclut les étapes suivantes :

- Importation delta de tous les connecteurs
- Synchronisation delta sur tous les connecteurs
- Exportation de tous les connecteurs

Il est possible que vous avez urgent modifier qui doivent être synchronisé immédiatement c’est pourquoi vous devez exécuter manuellement un cycle. Si vous devez exécuter manuellement un cycle, puis à partir de PowerShell exécuter `Start-ADSyncSyncCycle -PolicyType Delta`.

**Cycle de synchronisation complète**  
Si vous avez apporté une des modifications de configuration suivantes, vous devez exécuter un cycle de synchronisation complet (également appelé Initiale) :

- Ajouter plusieurs objets ou des attributs à importer à partir d’un répertoire source
- Apporté des modifications aux règles de synchronisation
- Changé [filtrage](active-directory-aadconnectsync-configure-filtering.md) afin d’un nombre différent d’objets doit être inclus

Si vous avez apporté une de ces modifications, vous devez exécuter un cycle de synchronisation complète afin que le moteur de synchronisation a la possibilité de reconsolider les espaces de connecteur. Un cycle de synchronisation complète inclut les étapes suivantes :

- Importation complète de tous les connecteurs
- Synchronisation complète de tous les connecteurs
- Exportation de tous les connecteurs

Pour initier un cycle de synchronisation complète, exécutez `Start-ADSyncSyncCycle -PolicyType Initial` à partir d’une invite PowerShell. Démarre un cycle de synchronisation complète.

## <a name="stop-the-scheduler"></a>Arrêter le planificateur
Si le planificateur est en cours d’exécution un cycle de synchronisation, vous devrez peut-être arrêter. Par exemple, si vous démarrez l’Assistant installation et que vous obtenez cette erreur :

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Lorsqu’un cycle de synchronisation s’exécute, vous ne pouvez pas modifier la configuration. Vous pouvez attendre que le planificateur a terminé le processus, mais vous pouvez également arrêter pour effectuer vos modifications immédiatement. Arrêter le cycle en cours n’est pas dangereux et les modifications ne pas encore traitées seront traitées avec la commande Exécuter suivante.

1. Commencez par indiquant le planificateur d’arrêter son cycle en cours avec l’applet de commande PowerShell `Stop-ADSyncSyncCycle`.
2. Arrêter le planificateur n’empêche pas le lien en cours à partir de la tâche actuelle. Pour forcer le connecteur pour arrêter, effectuez les actions suivantes : ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Démarrer le **Service de synchronisation** dans le menu Démarrer. Accédez aux **connecteurs**, mettez en surbrillance le connecteur avec l’état **en cours d’exécution** et sélectionnez **Arrêter** à partir des Actions.

Le planificateur est toujours actif et commence une nouvelle opportunité suivante.

## <a name="custom-scheduler"></a>Planificateur personnalisé
Les applets de commande présentées dans cette section ne sont disponibles dans la version [1.1.130.0](active-directory-aadconnect-version-history.md#111300) et versions ultérieures.

Si le Planificateur intégré ne répond pas à vos besoins, vous pouvez planifier les connecteurs à l’aide de PowerShell.

### <a name="invoke-adsyncrunprofile"></a>ADSyncRunProfile appeler
Vous pouvez démarrer un profil pour un connecteur de cette façon :

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Vous trouverez les noms à utiliser pour les [noms de connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md) et [exécuter profil](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) dans l' [Interface utilisateur du Gestionnaire de services de synchronisation](active-directory-aadconnectsync-service-manager-ui.md).

![Appeler le profil d’exécution](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

La `Invoke-ADSyncRunProfile` applet de commande est synchrone, par exemple, il ne renvoie pas contrôle jusqu'à ce que le connecteur a terminé l’opération, avec succès ou avec un message d’erreur.

Lorsque vous planifiez vos connecteurs, il est recommandé de planifier leur dans l’ordre suivant :

1. (Plein/Delta) Importer à partir des annuaires en local, tel que Active Directory
2. (Plein/Delta) Importer à partir d’Azure AD
3. (Plein/Delta) Synchronisation d’annuaires en local, tel que Active Directory
4. (Plein/Delta) Synchronisation à partir d’Azure AD
5. Exporter vers Azure Active Directory
6. Exporter des répertoires en local, tel que Active Directory

Si vous examinez le Planificateur intégré, il s’agit de l’ordre que les connecteurs seront exécutera.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Vous pouvez également contrôler le moteur de synchronisation pour voir si elle est occupé (e) ou inactif. Cette applet de commande renvoie un résultat vide si le moteur de synchronisation est inactif et ne fonctionne pas un connecteur. Si un connecteur est en cours d’exécution, elle renvoie le nom du connecteur.

```
Get-ADSyncConnectorRunStatus
```

![Connecteur exécuter l’état](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Dans l’image ci-dessus, la première ligne est d’un état dans lequel le moteur de synchronisation est inactif. La deuxième ligne à partir de quand le connecteur AD Azure est en cours d’exécution.

## <a name="scheduler-and-installation-wizard"></a>Assistant Planificateur et installation
Si vous démarrez l’Assistant installation, puis le planificateur est temporairement suspendu. Il s’agit, car il est supposé égal vous pouvez apporter des modifications de configuration et celles-ci ne peuvent pas être appliquées si le moteur de synchronisation est en cours d’exécution. Pour cette raison, ne laissez pas l’Assistant installation ouvert dans la mesure où elle s’arrête le moteur de synchronisation d’effectuer les actions de synchronisation.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

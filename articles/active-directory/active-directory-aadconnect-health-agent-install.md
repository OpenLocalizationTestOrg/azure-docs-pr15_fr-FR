<properties
    pageTitle="Installation Agent d’intégrité connecter AD Azure | Microsoft Azure"
    description="Il s’agit de la page Azure AD connecter santé qui décrit l’installation de l’agent pour AD FS et de synchronisation."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Installation de l’Agent d’intégrité

Ce document Guide vous aide à installer et configurer les Agents d’intégrité de se connecter de Azure AD. Vous pouvez télécharger les agents à partir [d’ici](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Configuration requise
Le tableau suivant est une liste de la configuration requise pour l’utilisation d’état Azure AD se connecter.

| Configuration requise | Description|
| ----------- | ---------- |
|Azure AD Premium| Azure AD connecter santé est une fonctionnalité Azure AD Premium et nécessite Azure AD Premium. </br></br>Pour plus d’informations, voir [mise en route d’Azure AD Premium](active-directory-get-started-premium.md) </br>Pour démarrer une version d’évaluation gratuite de 30 jours, consultez [Démarrer une version d’évaluation.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Vous devez être un administrateur global de prise en main d’Azure AD connecter intégrité de votre annonce Azure|Par défaut, seuls les administrateurs globaux peuvent installer et configurer les agents d’intégrité pour prise en main, accéder au portail et effectuer des opérations au sein de la santé Azure AD se connecter. Pour plus d’informations, consultez [administration des recherches dans l’annuaire Active Directory Azure](active-directory-administer.md). <br><br> À l’aide de contrôle d’accès rôle vous pouvez autoriser l’accès à Azure état AD se connecter à d’autres utilisateurs de votre organisation. Pour plus d’informations, voir [rôle contrôle d’accès d’Azure AD connecter intégrité.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importantes :** Le compte utilisé lors de l’installation des agents doit être un compte professionnel ou scolaire. Il ne peut pas être un compte Microsoft. Pour plus d’informations, voir [vous inscrire Azure en tant qu’organisation](sign-up-organization.md)
|L’Agent d’intégrité connecter AD Azure est installé sur chaque serveur ciblé| Azure AD connecter santé nécessite l’installation d’agents sur des serveurs cibles pour fournir les données qui sont affichées dans le portail. </br></br>Par exemple, pour obtenir des données à partir de votre infrastructure locaux AD FS, l’agent doit être installé sur les serveurs Proxy de l’Application Web AD FS et Proxy AD FS. De même, pour obtenir des données dans vos locaux infrastructure AD DS, l’agent doit être installé sur le domaine. </br></br>**Importantes :** Le compte utilisé lors de l’installation des agents doit être un compte professionnel ou scolaire. Il ne peut pas être un compte Microsoft. Pour plus d’informations, voir [vous inscrire Azure en tant qu’organisation](sign-up-organization.md)|
|Connectivité sortante aux points de terminaison de service Azure|Pendant l’installation et d’exécution, l’agent nécessite une connexion à points de terminaison de service Azure AD connecter santé. Si connectivité sortante est bloquée, vous assurer que les points de terminaison suivants sont ajoutés à la liste autorisée : </br></br><li>& #42 ;. BLOB.Core.Windows.NET </li><li>& #42 ;. Queue.Core.Windows.NET</li><li>adhsprodwus.ServiceBus.Windows.NET - Port : 5671 </li><li>https://Management.Azure.com </li><li>https://s1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.AD.msft.NET/</li><li>https://login.Windows.NET</li><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Ports de pare-feu sur le serveur qui exécute l’agent.| L’agent requiert les ports de pare-feu suivants être ouverts dans l’ordre de l’agent communiquer avec les points de terminaison du service d’intégrité Azure AD.</br></br><li>TCP/UDP port 443</li><li>Ports TCP/UDP 5671</li>
|Autoriser les sites Web suivants si la sécurité renforcée d’Internet Explorer est activée| Si la sécurité renforcée d’Internet Explorer est activée, les sites Web suivants doivent être autorisés sur le serveur qui va agir en l’agent est installé.</br></br><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://login.Windows.NET</li><li>Le serveur de fédération pour votre organisation approuvé par Azure Active Directory. Par exemple : https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>L’installation de l’annonce Azure connecter Agent d’intégrité pour AD FS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Dans le premier écran, cliquez sur Installer.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health-requirements/install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health-requirements/install2.png)

Une invite de commandes est lancée, suivi de certains PowerShell qui s’exécute Registre AzureADConnectHealthADFSAgent. Lorsque vous êtes invité à se connecter à Azure, continuez et se connecter.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health-requirements/install3.png)


Après la connexion, PowerShell continueront. Une fois terminée, vous pouvez fermer PowerShell et la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement qui permet à l’agent surveiller et collecter des données. Si vous n’avez pas remplies toutes les conditions requises décrites dans les sections précédentes, les avertissements s’affichent dans la fenêtre PowerShell. Veillez à effectuer [Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) avant d’installer l’agent. La capture d’écran suivante est un exemple de ces erreurs.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health-requirements/install4.png)

Pour vérifier que l’agent a été installé, recherchez les services suivants sur le serveur. Si la configuration terminée, ils doivent être en cours d’exécution. Dans le cas contraire, ils sont arrêtés jusqu'à ce que la configuration est terminée.

- Azure AD Connect santé AD FS Diagnostics Service
- Azure AD Connect santé AD FS Insights Service
- Azure AD Connect santé AD FS analyse du Service

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Installation de l’agent sur les serveurs Windows Server 2008 R2

Étapes pour les serveurs de Windows Server 2008 R2 :

1. Assurez-vous que le serveur est en cours d’exécution au Service Pack 1 ou version ultérieure.
1. Activer ou désactiver renforcée d’Internet Explorer pour l’installation de l’agent :
1. Installer Windows PowerShell 4.0 sur chacun des serveurs avant l’installation de l’agent d’intégrité AD. Pour installer Windows PowerShell 4.0 :
 - Installez [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) en utilisant le lien suivant pour télécharger le programme d’installation en mode hors connexion.
 - Installer PowerShell ISE (à partir de fonctionnalités de Windows)
 - Installer le [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installer Internet Explorer version 10 ou version ultérieure sur le serveur. (Requis par le Service d’intégrité s’authentifier à l’aide de vos informations d’identification d’administration Azure).
1. Pour plus d’informations sur l’installation de Windows PowerShell 4.0 sur Windows Server 2008 R2, voir l’article wiki [ici](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Activer l’audit pour AD FS

> [AZURE.NOTE] Cette section s’applique uniquement aux serveurs de fédération AD FS.

Dans l’ordre de la fonction de l’utilisation Analytique collecter et analyser des données, l’agent d’Azure AD connecter intégrité doit les informations contenues dans les journaux d’Audit AD FS. Ces journaux n’est pas activés par défaut. Utilisez les procédures suivantes pour activer l’audit AD FS et recherchez les journaux d’audit AD FS, sur vos serveurs d’AD FS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Pour activer l’audit pour AD FS 2.0

1. Cliquez sur **Démarrer**, pointez sur **programmes**, sur **Outils d’administration**, puis cliquez sur **Stratégie de sécurité locale**.
2. Accédez au dossier **Sécurité sécurité\Stratégies locales\Attribution Rights Management** , puis sur Générer des audits de sécurité.
3. Sous l’onglet **Paramètres de sécurité locale** , vérifiez que le compte de service 2.0 AD FS est répertorié. Si elle n’est pas présenter, cliquez sur **Ajouter un utilisateur ou groupe** et ajouter à la liste, puis sur **OK**.
4. Pour activer l’audit, ouvrez une invite de commande avec des privilèges élevés et exécutez la commande suivante :<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Fermez la stratégie de sécurité locale et ouvrez le composant logiciel enfichable Gestion. Pour ouvrir le composant logiciel enfichable Gestion, cliquez sur **Démarrer**, pointez sur **programmes**, sur **Outils d’administration**, puis cliquez sur AD FS 2.0 Management.
6. Dans le volet Actions, cliquez sur Modifier les propriétés de Service de fédération.
7. Dans la boîte de dialogue **Propriétés du Service de fédération** , cliquez sur l’onglet **Events** .
8. Activez les cases à cocher **l’audit de succès** et **audits des échecs** .
9. Cliquez sur **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Pour activer l’audit pour AD FS sur Windows Server 2012 R2

1. Ouvrir la **Stratégie de sécurité locale** en ouvrant **Le Gestionnaire de serveur** sur l’écran d’accueil ou le Gestionnaire de serveur dans la barre des tâches sur le bureau, puis cliquez sur **Outils/Local la stratégie de sécurité**.
2. Accédez au dossier **Paramètres de sécurité\Stratégies Locales\attribution droits** et double-cliquez **des audits de sécurité générer**.
3. Sous l’onglet **Paramètres de sécurité locale** , vérifiez que le compte de service AD FS est répertorié. Si elle n’est pas présenter, cliquez sur **Ajouter un utilisateur ou groupe** et ajouter à la liste, puis sur **OK**.
4. Pour activer l’audit, ouvrez une invite de commande avec des privilèges élevés et exécutez la commande suivante :<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Fermez la **Stratégie de sécurité locale**, puis ouvrez le composant logiciel enfichable **AD FS Management** (dans le Gestionnaire de serveur, cliquez sur outils et sélectionnez AD FS Management).
6. Dans le volet Actions, cliquez sur **Modifier les propriétés de Service de fédération**.
7. Dans la boîte de dialogue Propriétés de Service de fédération, cliquez sur l’onglet **Events** .
8. Activez les cases à cocher **l’audit de succès et audits des échecs** , puis sur **OK**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Pour localiser l’audit AD FS ouvre une session


1. Ouvrez **l’Observateur d’événements**.
2. Accédez aux journaux Windows et sélectionnez **sécurité**.
3. Sur la droite, cliquez sur **Filtre les journaux en cours**.
4. Sous Source de l’événement, sélectionnez **AD FS audit**.

![Journaux d’audit AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Si une stratégie de groupe qui désactive AD FS audit existe, l’Agent d’intégrité de se connecter de Azure AD ne peut pas collecter les informations. Vérifiez que vous n’avez pas une stratégie de groupe qui peut-être être désactivation de l’audit.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Installation de l’agent de la santé Azure AD se connecter pour la synchronisation
L’agent de la santé Azure AD se connecter pour la synchronisation est installée automatiquement dans la dernière version de Azure AD Connect. Pour utiliser Azure AD Connect pour la synchronisation, vous devez télécharger la dernière version d’Azure AD Connect et l’installer. Vous pouvez télécharger la version la plus récente [ici](http://www.microsoft.com/download/details.aspx?id=47594).

Pour vérifier que l’agent a été installé, recherchez les services suivants sur le serveur. Si la configuration terminée, ils doivent être en cours d’exécution. Dans le cas contraire, ils sont arrêtés jusqu'à ce que la configuration est terminée.

- Azure AD Connect santé synchronisation Insights Service
- Azure AD Connect synchronisation intégrité du Service de surveillance

![Vérifier Azure AD Connect santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] N’oubliez pas que l’utilisation d’état connecter Azure AD requiert Azure AD Premium. Si vous n’avez pas Azure AD Premium, vous ne parvenez pas à terminer la configuration du portail Azure. Pour plus d’informations, consultez la [page Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements).


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Manuel Azure AD se connecter au niveau d’enregistrement de synchronisation
Si l’état de connexion de AD Azure pour l’inscription de l’agent de synchronisation échoue après avoir installé correctement Azure AD Connect, vous pouvez utiliser la commande PowerShell suivante pour enregistrer manuellement l’agent.

>[AZURE.IMPORTANT] L’utilisation de cette commande PowerShell n’est obligatoire si l’inscription de l’agent échoue après l’installation d’Azure AD Connect.

La session PowerShell suivante commande est nécessaire uniquement lorsque l’inscription de l’agent d’intégrité échoue après une installation réussie et la configuration de Azure AD Connect. Les services Azure AD connecter santé démarre une fois que l’agent a été correctement inscrit.

Vous pouvez enregistrer manuellement l’agent de la santé Azure AD se connecter pour la synchronisation à l’aide de la commande PowerShell suivante :

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

La commande vous permet d’accéder à la suite de paramètres :

- AttributeFiltering : $true (par défaut) - si Azure AD Connect ne se synchronise pas l’attribut par défaut définie et a été personnalisée pour utiliser un ensemble d’attributs filtrés. $false autrement.
- StagingMode : $false (par défaut) - si le serveur Azure AD Connect n’est pas dans les intermédiaires mode, $true si le serveur est configuré pour être en mode de mise en attente.

Lorsque vous y êtes invité pour l’authentification, vous devez utiliser le même compte d’administrateur global (tel que admin@domain.onmicrosoft.com) qui a été utilisé pour la configuration Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>L’installation de l’annonce Azure connecter Agent d’intégrité pour AD DS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Dans le premier écran, cliquez sur Installer.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Une invite de commandes est lancée, suivi de certains PowerShell qui s’exécute Registre AzureADConnectHealthADDSAgent. Lorsque vous êtes invité à se connecter à Azure, continuez et se connecter.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Après la connexion, PowerShell continueront. Une fois terminée, vous pouvez fermer PowerShell et la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement qui permet à l’agent surveiller et collecter des données. Si vous n’avez pas remplies toutes les conditions requises décrites dans les sections précédentes, les avertissements s’affichent dans la fenêtre PowerShell. Veillez à effectuer [Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) avant d’installer l’agent. La capture d’écran suivante est un exemple de ces erreurs.

![Vérifier Azure AD Connect santé pour AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Pour vérifier que l’agent a été installé, recherchez les services suivants sur le contrôleur de domaine.

- Azure AD Connect santé AD DS Insights Service
- Azure AD Connect santé AD DS analyse du Service

Si la configuration terminée, ces services doivent être en cours d’exécution. Dans le cas contraire, ils sont arrêtés jusqu'à ce que la configuration est terminée.

![Vérifier Azure AD Connect d’intégrité](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>L’installation de l’annonce Azure connecter Agent d’intégrité pour les services AD DS sur Server Core.
Après avoir installé le fichier .exe, vous pouvez effectuer le processus d’inscription à l’aide de la commande PowerShell suivante :

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurer Azure AD connecter Agents d’intégrité pour utiliser le HTTP Proxy
Vous pouvez configurer Azure AD connecter Agents d’intégrité pour l’utiliser avec un HTTP Proxy.

>[AZURE.NOTE]
- À l’aide de « Netsh WinHttp définir ProxyServerAddress » n’est pas pris en charge que l’agent utilise System.Net pour effectuer des requêtes web au lieu de Services Microsoft Windows HTTP.
- L’adresse Http Proxy configurée sert à SQL directes messages Https chiffrés.
- Proxy authentifié (à l’aide de HTTPBasic) n’est pas pris en charge.

### <a name="change-health-agent-proxy-configuration"></a>Configuration du serveur Proxy Agent modifier d’intégrité
Vous disposez des options suivantes pour configurer Azure AD connecter Agent d’intégrité pour utiliser un HTTP Proxy.

>[AZURE.NOTE]Tous les services Agent d’Azure AD connecter intégrité doivent être redémarrés afin que les paramètres de proxy à mettre à jour. Exécutez la commande suivante :<br>
Redémarrer-Service AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importer les paramètres de proxy existant

##### <a name="import-from-internet-explorer"></a>Importer à partir d’Internet Explorer
Paramètres de proxy Internet Explorer HTTP peuvent être importés pour être utilisé par les Agents d’intégrité de se connecter de Azure AD. Sur chacun des serveurs exécutant l’agent d’intégrité, exécutez la commande PowerShell suivante :

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importer à partir de WinHTTP
Paramètres de proxy WinHTTP peuvent être importés pour être utilisé par les Agents d’intégrité de se connecter de Azure AD. Sur chacun des serveurs exécutant l’agent d’intégrité, exécutez la commande PowerShell suivante :

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Spécifier manuellement des adresses de Proxy
Vous pouvez spécifier manuellement un serveur proxy sur chacun des serveurs exécutant l’Agent d’intégrité, en exécutant la commande PowerShell suivante :

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemple : *myproxyserver Set-AzureAdConnectHealthProxySettings - HttpsProxyAddress : 443*

- « adresse » peut être un nom de serveur peut être résolu de DNS ou une adresse IPv4
- « port » peut être omis. En cas d’omission puis 443 est choisie comme port par défaut.

#### <a name="clear-existing-proxy-configuration"></a>Effacer la configuration proxy existant
Vous pouvez effacer la configuration du proxy existant en exécutant la commande suivante :

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Lire les paramètres de proxy actuels
Vous pouvez lire les paramètres de proxy actuellement configuré en exécutant la commande suivante :

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Tester la connectivité à Azure AD connecter intégrité du Service
Il est possible que les problèmes peuvent survenir entraînant l’agent Azure AD connecter santé perte de connectivité avec le service de santé Azure AD se connecter. Ces incluent les problèmes de réseau, problèmes d’autorisation ou diverses autres raisons.

Si l’agent ne parvient pas à envoyer des données au service la santé Azure AD se connecter pendant plus de deux heures, il est indiqué par l’alerte suivante dans le portail : « données intégrité du Service ne sont pas à jour ». Vous pouvez vérifier si la concerné Azure AD Connect agent d’intégrité est en mesure de télécharger des données dans le service de santé Azure AD se connecter en exécutant la commande PowerShell suivante :

    Test-AzureADConnectHealthConnectivity -Role ADFS

Le paramètre de rôle prend les valeurs suivantes :

- ADFS
- Synchronisation
- AJOUTE

Vous pouvez utiliser l’indicateur - ShowResults dans la commande pour afficher les journaux détaillées. Utilisez l’exemple suivant :

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Pour utiliser l’outil de connectivité, vous devez d’abord achever l’inscription de l’agent. Si vous n’êtes pas en mesure de terminer l’enregistrement de l’agent, vérifiez que vous remplissez toutes les [conditions requises](active-directory-aadconnect-health-agent-install.md#requirements) pour la santé Azure AD se connecter. Ce test de connectivité est effectué par défaut lors de l’inscription de l’agent.



## <a name="related-links"></a>Liens connexes

* [Azure AD Connect d’intégrité](active-directory-aadconnect-health.md)
* [Azure AD Connect opérations d’intégrité](active-directory-aadconnect-health-operations.md)
* [À l’aide de Azure AD connectez santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [L’utilisation d’état connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide de Azure AD connectez santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect santé Forum aux questions](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historique des versions d’intégrité](active-directory-aadconnect-health-version-history.md)

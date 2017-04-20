<properties
   pageTitle="Notes de publication de catalogue de données Azure | Microsoft Azure"
   description="Notes de publication pour le catalogue de données Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Notes de publication de catalogue de données Azure

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notes de 20 novembre 2015 version du catalogue de données Azure

### <a name="opening-data-sources-in-power-bi-desktop"></a>Ouverture des Sources de données dans Power BI Desktop

Lorsque vous utilisez l’option « Ouvrir dans Power BI Desktop » à partir du portail de **Catalogue de données Azure** , les utilisateurs peuvent rencontrer un des deux problèmes dans l’application Power BI Desktop :

- Une boîte de dialogue avec le titre « Impossible à ouvrir le fichier » s’affiche
- L’application Power BI Desktop s’ouvre, mais le fichier semble vide

Pour chaque situation, le problème peut être résolu en téléchargeant et en installant la dernière version de Power BI Desktop à partir de [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notes de 13 novembre 2015 version du catalogue de données Azure

### <a name="registering-and-connecting-to-teradata"></a>Enregistrement et connexion à Teradata

Lors de la connexion aux sources de données Teradata utilisateurs doivent avoir installé le pilote ODBC Teradata qui correspondent au nombre de bits (32 bits ou 64 bits) du logiciel utilisé.

À partir de cette date de publication connecteur Active Directory, le plus récent [pilote ODBC Teradata pour windows (version 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) est compatible avec Office 2013, mais pas avec Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notes de 13 juillet 2015 version du catalogue de données Azure

### <a name="registering-and-connecting-to-oracle-database"></a>Enregistrement et la connexion à la base de données Oracle

Lors de la connexion aux sources de données de base de données Oracle utilisateurs doivent avoir installé les pilotes Oracle correctes qui correspondent au nombre de bits (32 bits ou 64 bits) du logiciel utilisé.

-   Lorsque vous enregistrez des sources de données Oracle sur un ordinateur exécutant Windows 32 bits, les pilotes Oracle 32 bits seront utilisés
-   Lorsque vous enregistrez des sources de données Oracle sur un ordinateur exécutant Windows 64 bits, les pilotes Oracle 64 bits seront utilisés
-   Lors de la connexion aux sources de données Oracle à l’aide d’Excel sur un ordinateur exécutant la version 32 bits de Microsoft Office, y compris sur Windows 64 bits, les pilotes Oracle 32 bits servira
-   Lors de la connexion aux sources de données Oracle à l’aide d’Excel sur un ordinateur exécutant la version 64 bits de Microsoft Office, les pilotes Oracle 64 bits seront utilisés

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Enregistrement et la connexion à SQL Server Reporting Services

Prise en charge des sources de données SQL Server Reporting Services (SSRS) est actuellement limitée aux serveurs en Mode natif uniquement. Prise en charge de SSRS en mode SharePoint est ajouté dans une version ultérieure.

### <a name="opening-data-assets-in-excel"></a>Ressources de données ouverture dans Excel

Lors de l’ouverture des ressources de données dans Microsoft Excel à partir du portail de **Catalogue de données Azure** , les utilisateurs peuvent être invités avec une boîte de dialogue de **Notification de sécurité Microsoft Excel** . Il s’agit standard, comportement attendu et vos utilisateurs pouvant sélectionnez **Activer** pour continuer.

Pour plus d’informations, voir [Activer ou désactiver les alertes de sécurité relatives aux liens et des fichiers provenant de sites Web suspects](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy et stratégie de configuration et les données source d’enregistrement

Les utilisateurs peuvent rencontrer une situation où se connecter au portail catalogue de données Azure, mais quand ils essaient d’ouvrir une session sur l’outil d’enregistrement de source de données qu’ils rencontrent un message d’erreur qui empêche l’ouverture de session.

Il existe deux causes possibles à ce problème :

**Cause 1 : configuration d’Active Directory Federation Services** L’outil de l’enregistrement de source de données utilise l’authentification par formulaire pour valider les connexions utilisateur dans Active Directory. Pour l’ouverture de session, l’authentification par formulaire doit être activée dans la stratégie d’authentification globale par un administrateur Active Directory.

Dans certains cas, ce comportement d’erreur peut se produire uniquement lorsque l’utilisateur est connecté au réseau d’entreprise, ou uniquement lorsque l’utilisateur se connecte à partir de l’extérieur du réseau d’entreprise. La stratégie d’authentification globale permet de méthodes d’authentification doit être activé séparément pour intranet et les connexions extranet. Erreurs de connexion peuvent se produire si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel l’utilisateur se connecte.

Pour plus d’informations, voir [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).

**Cause 2 : configuration du proxy réseau** Si le réseau d’entreprise utilise un serveur proxy, l’outil d’inscription peut ne pas être en mesure de se connecter à Azure Active Directory via le serveur proxy. Les utilisateurs peuvent s’assurer que l’outil d’inscription en modifiant le fichier de configuration de l’outil, ajout de cette section au fichier :


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Pour localiser le fichier RegistrationTool.exe.config, lancez l’outil d’enregistrement, puis ouvrez l’utilitaire de Gestionnaire des tâches Windows. Sous l’onglet Détails dans le Gestionnaire des tâches, avec le bouton droit sur RegistrationTool.exe et choisissez l’emplacement du fichier dans le menu contextuel.

<properties
    pageTitle="Azure Active Directory applets de commande pour configurer les paramètres de groupe | Microsoft Azure"
    description="Comment gérer les paramètres de groupes à l’aide des applets de commande Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory applets de commande pour configurer les paramètres de groupe

Les paramètres suivants pour les groupes unifiées peuvent être configurés dans votre annuaire :

1.  Classifications : séparées par des virgules liste des classifications que les utilisateurs peuvent définir sur un groupe. Exemples serait « Classiques », « Secret » et « Top Secret ».

2.  Utilisation des instructions URL : une URL qui pointe utilisateurs vers les conditions d’utilisation d’utilisation des groupes unifiée, telle que définie par votre organisation. Cette URL apparaît dans l’interface utilisateur où les utilisateurs utilisent les groupes.

3.  Groupe création activée : Si aucun, un ou plusieurs utilisateurs sont autorisés à créer des groupes unifiée. Lorsque vous définissez sur activé, tous les utilisateurs peuvent créer des groupes. Lorsque la valeur OFF, aucun utilisateurs peuvent créer des groupes. Lorsque désactivé, vous pouvez également spécifier un groupe de sécurité dont les utilisateurs qui sont toujours autorisés à créer des groupes.

Ces paramètres sont configurés à l’aide d’un fichier de paramètres et des objets SettingsTemplate. Au départ, vous verrez pas les objets paramètres dans votre annuaire. Cela signifie que votre répertoire est configuré avec les paramètres par défaut. Pour modifier les paramètres par défaut, vous allez créer un nouvel objet de paramètres à l’aide d’un modèle de paramètres. Modèles de paramètres sont définies par Microsoft.

Vous pouvez télécharger le module qui contient les applets de commande utilisées pour effectuer ces opérations à partir du [site Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Créer des paramètres au niveau du répertoire

Ces étapes créent des paramètres au niveau du répertoire, qui s’appliquent à tous les groupes Office dans le répertoire.

1. Si vous ne connaissez pas le SettingTemplate à utiliser, cette applet de commande renvoie la liste des modèles de paramètres :

    `Get-MsolAllSettingTemplate`

    ![Liste des modèles de paramètres](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Pour ajouter une URL de l’indication l’utilisation, vous devez d’abord obtenir l’objet SettingsTemplate qui définit la valeur d’URL orientation utilisation ; Autrement dit, le modèle Group.Unified :

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Ensuite, créez un nouvel objet paramètres basé sur ce modèle :

    `$setting = $template.CreateSettingsObject()`

4. Puis mettez à jour la valeur d’orientation utilisation :

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Pour finir, appliquez les paramètres :

    `New-MsolSettings –SettingsObject $setting`

    ![Ajouter une URL de l’indication l’utilisation](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Voici les paramètres définis dans les Group.Unified SettingsTemplate.

 **Paramètre**                          | **Description**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Type : chaîne<li>Par défaut : « »                  | Une liste délimitée par des virgules de valeurs de classification valide qui peuvent être appliquées aux groupes unifiée.                
 <ul><li>EnableGroupCreation<li>Type : booléenne<li>Par défaut : True              | Indicateur indiquant si la création de groupes unifiée est autorisée dans l’annuaire.                               
 <ul><li>GroupCreationAllowedGroupId<li>Type : chaîne<li>Par défaut : « »         | GUID du groupe de sécurité qui est autorisé à créer des groupes unifiée même lorsque EnableGroupCreation == false.
 <ul><li>UsageGuidelinesUrl<li>Type : chaîne<li>Par défaut : « »                  | Un lien vers les instructions de l’utilisation de groupe.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Lire les paramètres au niveau du répertoire

Ces étapes lire les paramètres au niveau du répertoire, qui s’appliquent à tous les groupes Office dans le répertoire.

1. Lire tous les paramètres d’annuaire existants :

    `Get-MsolAllSettings`

2. Lire tous les paramètres pour un groupe spécifique :

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Lire les paramètres de répertoire spécifique, à l’aide de SettingId GUID :

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID des ID de paramètres](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Mettre à jour les paramètres au niveau du répertoire

Ces étapes mettre à jour les paramètres au niveau du répertoire, qui s’appliquent à tous les groupes Office dans le répertoire.

1. Obtenir l’objet de paramètres existant :

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Obtenir la valeur que vous souhaitez mettre à jour :

    `$value = $Setting.GetSettingsValue()`

3. Mettre à jour la valeur :

    `$value["AllowToAddGuests"] = "false"`

4. Mettre à jour le paramètre :

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Supprimer des paramètres au niveau du répertoire

Cette étape supprime les paramètres au niveau du répertoire, qui s’appliquent à tous les groupes Office dans le répertoire.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Référence de syntaxe applet de commande

Vous pouvez trouver plus Azure Active Directory PowerShell documentation sur [Azure Active Directory applets de commande](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Référence d’objet SettingsTemplate (Group.Unified SettingsTemplate objet)

- « nom » : « EnableGroupCreation », « type » : « System.Boolean », « ValeurParDéfaut » : « true », « description » : « Indicateur booléen indiquant si la fonctionnalité de création de groupe unifiée est activée. »

- « nom » : « GroupCreationAllowedGroupId », « type » : « System.Guid », « ValeurParDéfaut » : « », « description » : « GUID du groupe de sécurité qui est autorisés à créer des groupes unifiée ».

- « nom » : « ClassificationList », « type » : « System.String », « ValeurParDéfaut » : « », « description » : « Délimitée par des virgules liste de valeurs de classification valide qui peuvent être appliquées aux groupes unifiés. »

- « nom » : « UsageGuidelinesUrl », « type » : « System.String », « ValeurParDéfaut » : « », « description » : « Un lien vers les instructions de l’utilisation de groupe ».

nom | type | valeur par défaut | Description
----------  | ----------  | ---------  | ----------
« EnableGroupCreation »  | « System.Boolean »  | « true »  | « Indicateur booléen indiquant si la fonctionnalité de création de groupe unifiée est activée. »
« GroupCreationAllowedGroupId »  | « System.Guid »  | ""  | « GUID du groupe de sécurité qui est autorisés à créer des groupes unifiée ».
« ClassificationList »  | « System.String »  | ""  | « Délimitée par des virgules liste de valeurs de classification valide qui peuvent être appliquées aux groupes unifiée. »
« UsageGuidelinesUrl »  | « System.String »  | ""  | « Un lien vers les instructions de l’utilisation de groupe ».

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez trouver plus Azure Active Directory PowerShell documentation sur [Azure Active Directory applets de commande](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Jeu d’instructions supplémentaire à partir du Gestionnaire de programme Microsoft Rob de Jong est disponible sur [Groupes Blog de René](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Gestion de l’accès aux ressources des groupes de Azure Active Directory](active-directory-manage-groups.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

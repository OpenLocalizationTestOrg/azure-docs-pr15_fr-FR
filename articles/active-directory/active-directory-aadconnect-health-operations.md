<properties
    pageTitle="Azure AD Connect opérations d’intégrité."
    description="Cet article décrit les opérations supplémentaires qui peuvent être exécutées une fois que vous avez déployé Azure AD connecter santé."
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
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect opérations d’intégrité

La rubrique suivante décrit les diverses opérations qui peuvent être effectuées à l’aide de la santé Azure AD se connecter.

## <a name="enable-email-notifications"></a>Activer les notifications par courrier électronique
Vous pouvez configurer le Azure AD connecter intégrité du Service pour envoyer des notifications par courrier électronique lorsque des alertes sont générées indiquant que votre infrastructure d’identité n’est pas correct. Ce problème survient lorsqu’une alerte est générée, ainsi que lorsqu’il est marqué comme résolu. Suivez les instructions ci-dessous pour configurer les notifications par courrier électronique.

![Azure AD Connect santé messagerie Notification découvrir](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Notifications par courrier électronique sont désactivées par défaut.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Pour activer Azure AD connecter santé notifications par courrier électronique

1. Ouvrez la carte d’alertes pour le service pour lequel vous souhaitez recevoir de notification par courrier électronique.
2. Cliquez sur le bouton « Paramètres de Notification » à partir de la barre d’action.
3. Activer la Notification par courrier électronique sur activé.
4. Activez la case à cocher pour configurer tous les administrateurs globaux pour recevoir des notifications par courrier électronique.
5. Si vous souhaitez recevoir des notifications par courrier électronique sur d’autres adresses de messagerie, vous pouvez les spécifier dans la zone destinataire de messagerie supplémentaire. Pour supprimer une adresse de messagerie de cette liste, cliquez avec le bouton avec le bouton droit sur l’entrée, puis sélectionnez Supprimer.
6. Pour finaliser la modification, cliquez sur « Enregistrer ». Toutes les modifications ne prendront effets uniquement après que vous sélectionnez « Enregistrer ».

## <a name="delete-a-server-or-service-instance"></a>Supprimer une instance du serveur ou le service

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Supprimer un serveur de Service de Azure AD connecter santé
Dans certains cas, vous souhaiterez peut-être supprimer un serveur d’en cours d’analyse. Suivez les instructions ci-dessous pour supprimer un serveur de Service de Azure AD connecter santé.

Lorsque vous supprimez un serveur, gardez les éléments suivants :

- Cette action arrête de collecter des données supplémentaires à partir de ce serveur. Ce serveur a été supprimé du service de surveillance. Une fois cette action, vous ne serez pas en mesure d’afficher les nouvelles alertes, surveillance ou utilisation des données analytique pour ce serveur.
- Cette action ne sera pas désinstaller ou supprimer l’Agent d’intégrité de votre serveur. Si vous n’avez pas désinstallé l’Agent d’intégrité avant d’effectuer cette étape, vous pouvez voir les événements d’erreur sur le serveur relatifs à l’Agent d’intégrité.
- Cette action ne va pas supprimer les données déjà collectées à partir de ce serveur. Ces données seront supprimées conformément à la stratégie de rétention de données Microsoft Azure.
- Après avoir effectué cette action, si vous souhaitez démarrer l’analyse du même serveur à nouveau, vous avez besoin de désinstaller et de réinstaller l’agent d’intégrité sur ce serveur.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Pour supprimer un serveur de Service d’intégrité de connecter des Azure AD

Azure AD Connect santé pour AD FS et Azure AD Connect (synchronisation) :

1. Ouvrez la carte de serveur à partir de la carte de liste serveur en sélectionnant le nom du serveur à supprimer.
2. Dans la carte de serveur, cliquez sur le bouton « Supprimer » à partir de la barre d’action.
3. Confirmer l’action pour supprimer le serveur en tapant le nom du serveur dans la boîte de dialogue de confirmation.
4. Cliquez sur le bouton « Supprimer ».

Azure AD Connect santé pour AD DS :

1. Ouvrez le tableau de bord de domaine.
2. Sélectionnez le contrôleur de domaine à supprimer.
3. Cliquez sur le bouton « Supprimer sélectionnée » à partir de la barre d’action.
4. Confirmer l’action pour supprimer le serveur.
5. Cliquez sur le bouton « Supprimer ».

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Supprimer une instance de service à partir d’Azure AD connecter les Service de santé

Dans certains cas, il se pouvez que vous souhaitez supprimer une instance de service. Suivez les instructions ci-dessous pour supprimer une instance de service à partir d’Azure AD connecter les Service de santé.

Lorsque vous supprimez une instance de service, gardez les éléments suivants :

- Cette action supprime l’instance actuelle du service le service de surveillance.
- Cette action ne sera pas désinstaller ou supprimer l’Agent d’intégrité d’un des serveurs qui ont été observés dans le cadre de cette instance de service. Si vous n’avez pas désinstallé l’Agent d’intégrité avant d’effectuer cette étape, vous pouvez voir les événements d’erreur sur l’ou les serveurs lié à l’Agent d’intégrité.
- Toutes les données de cette instance de service seront supprimées conformément à la stratégie de rétention de données Microsoft Azure.
- Après avoir effectué cette action, si vous souhaitez démarrer l’analyse du service, veuillez désinstallez et réinstallez l’agent d’intégrité sur tous les serveurs à analyser. Après avoir effectué cette action, si vous souhaitez démarrer l’analyse du même serveur à nouveau, vous avez besoin de désinstaller et de réinstaller l’agent d’intégrité sur ce serveur.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Pour supprimer une instance de service à partir d’Azure AD connecter les Service de santé

1. Ouvrez la carte de Service à partir de la carte de liste Service en sélectionnant l’identificateur de service (nom de la batterie) que vous souhaitez supprimer.
2. Dans la carte de serveur, cliquez sur le bouton « Supprimer » à partir de la barre d’action.
3. Confirmez le nom du service en le tapant dans la boîte de dialogue de confirmation. (par exemple : sts.contoso.com)
4. Cliquez sur le bouton « Supprimer ».
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gérer l’accès avec basé sur un rôle contrôle d’accès
### <a name="overview"></a>Vue d’ensemble
[Contrôle d’accès rôle](role-based-access-control-configure.md) d’Azure AD connecter intégrité offre un accès service Azure AD connecter santé aux utilisateurs et/ou groupes en dehors des administrateurs globaux. Ceci est atteint en attribuant des rôles aux groupes et/ou utilisateurs appropriés et fournit un mécanisme pour limiter les administrateurs globaux dans votre annuaire.

#### <a name="roles"></a>Rôles
Azure AD connecter santé prend en charge les rôles prédéfinis suivants.

| Rôle | Autorisations |
| ----------- | ---------- |
| Propriétaire | Propriétaires peuvent ***gérer l’accès*** (par exemple, affecter le rôle à un utilisateur/groupe), ***Afficher toutes les informations*** (par exemple, afficher les alertes) à partir du portail et ***Modifier les paramètres*** (par exemple, les notifications par courrier électronique) au sein de la santé Azure AD se connecter. <br>Par défaut, les administrateurs globaux Azure AD sont voient attribuer ce rôle et il ne peut pas être modifiée.  |
|Collaboration|  Les collaborateurs peuvent ***Afficher toutes les informations*** (par exemple, afficher les alertes) à partir du portail et ***Modifier les paramètres*** (par exemple, les notifications par courrier électronique) au sein de la santé Azure AD se connecter.|
|Lecteur| Lecteurs peuvent ***Afficher toutes les informations*** (par exemple, afficher les alertes) à partir du portail au sein de la santé Azure AD se connecter.|

Tous les autres rôles (par exemple, les administrateurs de l’accès utilisateur ou « DevTest ateliers utilisateurs »), même s’il est disponible dans l’utilisation du portail, n’ont aucun impact avoir accès à partir d’Azure AD connecter intégrité.

#### <a name="access-scope"></a>Étendue d’Access

Azure AD Connect prend en charge gestion de l’accès à deux niveaux :

- ***Toutes les instances de service***: il s’agit de la procédure recommandée pour la plupart des clients et contrôle d’accès pour toutes les instances de service (par exemple, une batterie de serveurs ADFS) pour tous les types de rôle sont analysés par la santé Azure AD se connecter.

- ***Instance de service***: dans certains cas, vous devrez peut-être séparer les accès basé sur les types de rôles ou par une instance de service. Dans ce cas, vous pouvez gérer l’accès au niveau de l’instance de service.  

Autorisation si un utilisateur a accès soit au niveau du répertoire ou Instance de Service.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Comment autoriser l’accès des utilisateurs ou des groupes à Azure AD connecter état
#### <a name="steps-1-select-the-appropriate-access-scope"></a>Étapes 1 : Sélectionnez l’étendue d’accès appropriés
Pour autoriser un utilisateur à accéder au niveau de *toutes les instances de service* dans Azure AD connecter santé, ouvrez la carte principale en Azure AD connecter santé.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Étape 2 : Ajouter des utilisateurs, groupes et attribuer des rôles
1. Cliquez sur la partie « Utilisateurs » à partir de la section Configurer.<br>
![Azure AD Connect santé RBAC principale carte](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Sélectionnez « Ajouter »
3. Sélectionnez le « rôle » comme « Propriétaire »<br>
![Azure AD Connect santé RBAC ajouter un utilisateur](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Tapez le nom ou l’identificateur de l’utilisateur ciblé ou un groupe. Vous pouvez sélectionner un ou plusieurs utilisateurs ou groupes en même temps. Cliquez sur « select ».
![Azure AD Connect santé RBAC sélectionnez un utilisateur](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Sélectionnez « Ok ».<br>

6. Une fois que l’attribution de rôle est terminée, les utilisateurs et/ou les groupes seront affichent dans la liste.<br>
![Azure AD Connect santé RBAC liste des utilisateurs](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Ces étapes permettra les utilisateurs répertoriés et un groupe l’accès en fonction de leurs rôles.
>[AZURE.NOTE]
- Les administrateurs globaux ont toujours accès complet à toutes les opérations mais comptes d’administrateur global ne seront pas disponible dans la liste ci-dessus.
- Fonctionnalité « Inviter des utilisateurs » n’est pas pris en charge dans Azure AD connecter santé.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Étape 3 : Partager l’emplacement de carte avec des utilisateurs ou des groupes
1. Après l’attribution d’autorisations, un utilisateur peut accéder Azure AD connecter santé en accédant à [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Une fois sur la carte, l’utilisateur peut épingler la carte ou différentes parties au tableau de bord en cliquant simplement sur « Ajouter au tableau de bord »<br>
![Carte de code confidentiel Azure AD connecter santé RBAC](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un utilisateur avec le rôle de « Lecteur » affecté ne seront pas en mesure d’effectuer l’opération « créer » pour obtenir extension Azure AD connecter santé depuis la Azure Marketplace. Cet utilisateur peut toujours accéder à la carte en accédant au lien ci-dessus. Pour une utilisation ultérieure, l’utilisateur peut épingler la carte au tableau de bord.

### <a name="remove-users-andor-groups"></a>Supprimer des utilisateurs ou groupes
Vous pouvez supprimer un utilisateur ou un groupe ajouté au composant Azure AD connecter santé rôle contrôle d’accès en cliquant avec le bouton droit sur et en sélectionnant Supprimer.<br>
![Azure AD Connect santé RBAC supprimer utilisateur](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Liens connexes

* [Azure AD Connect d’intégrité](active-directory-aadconnect-health.md)
* [Azure AD Connect Installation de l’Agent d’intégrité](active-directory-aadconnect-health-agent-install.md)
* [À l’aide de Azure AD connectez santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [L’utilisation d’état connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide de Azure AD connectez santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect santé Forum aux questions](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historique des versions d’intégrité](active-directory-aadconnect-health-version-history.md)

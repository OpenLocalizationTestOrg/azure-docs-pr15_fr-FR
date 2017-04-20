<properties
  pageTitle="Suite IoT Azure et Azure Active Directory | Microsoft Azure"
  description="Décrit comment Azure IoT Suite utilise Azure Active Directory pour gérer les autorisations."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Autorisations sur le site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Que se passe-t-il lorsque vous êtes connecté

Lorsque vous êtes connecté à [azureiotsuite.com][lnk-azureiotsuite], le site détermine les niveaux d’autorisation que vous avez basé sur le client Azure Active Directory (DAS) actuellement sélectionnée et un abonnement Azure.

1.  Le site tout d’abord apprend à partir d’Azure les clients AAD auxquels vous appartenez afin de remplir la liste des clients apparaît en regard de votre nom d’utilisateur connecté. Pour le moment, le site ne pouvez obtenir jetons utilisateur pour un client à la fois. Par conséquent, lorsque vous basculez vers un autre client à l’aide de la liste déroulante dans le coin supérieur droit, le site nouveau ouvre une session dans ce client pour obtenir les jetons pour ce client.

2.  Ensuite, le site apprend à partir d’Azure les abonnements que vous avez associé au client sélectionné. Vous voyez les abonnements disponibles lorsque vous créez une nouvelle solution préconfigurée.

3.  Enfin, le site récupère toutes les ressources dans les abonnements et les groupes de ressources automatiquement marqués comme solutions préconfigurées et remplit les vignettes dans la page d’accueil.

Les sections suivantes décrivent les rôles contrôlent l’accès aux solutions préconfigurées.

## <a name="aad-roles"></a>Rôles AAD

Les rôles AAD contrôlent les solutions de mise en service préconfiguré possibilité et gérer les utilisateurs dans une solution préconfigurée.

Vous trouverez plus d’informations sur les rôles d’administrateur dans AAD dans [attribution de rôles administrateur dans Azure AD][lnk-aad-admin], mais cet article se concentre principalement sur l' **Administrateur Global** et les rôles **d’Utilisateur/membre du domaine** utilisé par les solutions préconfigurées.

**Administrateur général :** Il est possible de nombreux administrateurs globaux par client AAD. Lorsque vous créez un client AAD, vous êtes par défaut l’administrateur global de ce client. L’administrateur global peut fournir une solution préconfigurée et est affectée à un rôle **d’administrateur** pour l’application à l’intérieur de leur client AAD. Toutefois, si un autre utilisateur dans un seul client AAD crée une application, le rôle par défaut de que l’administrateur global est accordé est **Implicite en lecture seule**. Les administrateurs généraux peuvent attribuer des rôles pour les applications à l’aide du [portail classique Azure][lnk-classic-portal].

**Utilisateur/membre de domaine :** Il peut être nombreux utilisateurs/membres du domaine par client AAD. Un utilisateur de domaine pouvez configurer une solution préconfigurée via [azureiotsuite.com] [ lnk-azureiotsuite] site. Le rôle par défaut qu’ils sont accordées pour l’application de que leur mise en service est **administrateur**. Leur permettre de créer une application en utilisant le script build.cmd dans [azure-iot-de surveillance à distance] [ lnk-rm-github-repo] ou [azure iot-prédictive-maintenance] [ lnk-pm-github-repo] référentiel, mais le rôle par défaut ils bénéficient est **Implicite en lecture seule**, car ils n’ont pas autorisé à affecter des rôles. Si un autre utilisateur dans le client AAD crée une application, ils sont affectés le rôle **En lecture seule implicite** par défaut pour cette application. Ils n’ont pas la possibilité d’attribuer des rôles pour les applications ; Par conséquent ne peut pas ajouter des utilisateurs ou rôles pour les utilisateurs d’une application même si elles mis en service.

**Invité utilisateur/invité :** Il peut y avoir plusieurs utilisateurs/invités invité par client AAD. Les utilisateurs invités ont un ensemble limité de droits dans le client AAD. Par conséquent, les utilisateurs invités Impossible de fournir une solution préconfigurée dans le client AAD.

Pour plus d’informations, consultez les ressources suivantes :

- [Créer ou modifier des utilisateurs dans Azure Active Directory][lnk-create-edit-users]
- [Attribuer des rôles d’application dans AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Rôles d’administrateur abonnement Azure

Les rôles d’administrateur Azure contrôlent la fonctionnalité permettant d’établir une correspondance entre un abonnement Azure un client AD.

Vous pouvez en savoir plus sur les rôles d’administrateur Co-création Azure, administrateur de Service et administrateur de compte dans l’article [comment ajouter ou modifier Azure co-création administrateur, administrateur de Service et d’administrateur de compte][lnk-admin-roles].

## <a name="application-roles"></a>Rôles d’application

Les rôles d’application contrôlent l’accès aux appareils dans votre solution préconfigurée.

Il existe deux définis et un rôle implicite défini dans l’application est créée lorsque vous configurez une solution préconfigurée.

-   **Administrateur :** Dispose du contrôle total à ajouter, gérer et supprimer des appareils mobiles

-   **En lecture seule :** A la possibilité d’afficher les appareils mobiles

-   **Uniquement lecture implicite :** Ceci est le même en lecture seule, mais est accordé à tous les utilisateurs de votre client AAD. Ceci permet convenance pendant le développement. Vous pouvez supprimer ce rôle en modifiant la [RolePermissions.cs] [ lnk-resource-cs] fichier source.

### <a name="changing-application-roles-for-a-user"></a>Modification des rôles d’application pour un utilisateur

Vous pouvez utiliser la procédure suivante pour rendre un utilisateur dans Active Directory un administrateur de votre solution préconfiguré.

Vous devez être un administrateur global AAD pour modifier les rôles d’un utilisateur :

1. Accédez au [portail classique Azure][lnk-classic-portal].

2. Sélectionnez **Active Directory**.

3. Cliquez sur le nom de votre client AAD (c’est le répertoire choisi sur azureiotsuite.com lorsque vous sa mise en service de votre solution).

4. Cliquez sur **Applications**.

5. Cliquez sur le nom de l’application qui correspond au nom de votre solution préconfiguré. Si vous ne voyez pas votre application dans la liste, basculer la liste déroulante **Afficher** pour **Applications propriétaire de mon entreprise** , puis cliquez sur la coche.

7. Cliquez sur **utilisateurs**.

8. Sélectionnez l’utilisateur que vous voulez changer de rôle.

9. Cliquez sur **attribuer** , puis sélectionnez le rôle (par exemple, **administrateur**) que vous voulez attribuer à l’utilisateur, cliquez sur la coche.

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Je suis un administrateur de service et je souhaite modifier le mappage d’annuaire entre mon abonnement et un client AAD spécifique. Comment faire cela ?

1. Accédez au [portail classique Azure][lnk-classic-portal], cliquez sur **paramètres** dans la liste des services sur le côté gauche.

2. Sélectionnez l’abonnement que vous voulez modifier le mappage d’annuaire à.

3. Cliquez sur **Modifier l’annuaire**.

4. Sélectionnez le **répertoire** que vous voulez utiliser dans la liste déroulante. Cliquez sur la flèche droite.

5. Confirmer le mappage répertoire et affectées coadministrateurs. Notez que si vous migrez à partir d’un autre répertoire, toutes les coadministrateurs à partir du répertoire d’origine sont supprimés.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Je suis utilisateur/membre d’un domaine sur le client AAD et que j’ai créé une solution préconfigurée. Comment que j’ai obtenir attribués un rôle pour mon application ?

Demandez à un administrateur global pour vous attribuer en tant qu’un administrateur global dans le client AAD pour obtenir les autorisations à attribuer des rôles aux utilisateurs vous-même, ou demandez à un administrateur global pour vous attribuer un rôle. Si vous voulez modifier le client AAD votre solution préconfigurée a été déployé pour voir la question suivante.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Comment basculer le client AAD à que mes solution préconfigurée contrôle à distance et application affectés ?

Vous pouvez exécuter un déploiement cloud à partir de <https://github.com/Azure/azure-iot-remote-monitoring> et redéployez avec un client AAD nouvellement créé. Dans la mesure où vous êtes par défaut un administrateur global lorsque vous créez un nouveau client AAD, vous aurez accès permettant d’ajouter des utilisateurs et attribution de rôles à ces utilisateurs.

1. Créer un nouveau répertoire AAD dans le [portail de gestion Azure][lnk-classic-portal].

2. Accédez à <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Exécuter `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (par exemple, `build.cmd cloud debug myRMSolution`)

4. Lorsque vous y êtes invité, définissez la **tenantid** à votre client nouvellement créé à la place de votre client précédent.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Je souhaite modifier un administrateur de Service ou un collègue administrateur lorsque vous êtes connecté avec un compte d’organisation

Consultez l’article du support [administrateur du Service de modification et de co-création administrateur lorsque vous êtes connecté avec un compte d’organisation][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Pourquoi est-ce que je vois cette erreur ? « Votre compte n’a pas les autorisations nécessaires pour créer une solution. Veuillez contacter votre administrateur de compte ou essayez avec un autre compte. »

Jetez un coup de œil à l’illustration ci-dessous :

![][img-flowchart]

> [AZURE.NOTE] Si vous continuez d’obtenir l’erreur après validation vous sont un administrateur global dans le client DAS et administrateur de co-création sur l’abonnement, demandez à votre administrateur de compte supprimer l’utilisateur et ré-attribuer des autorisations nécessaires dans l’ordre suivant : ajouter l’utilisateur en tant qu’un administrateur général, puis ajoutez un utilisateur comme administrateur de co-création sur l’abonnement Azure. Si les problèmes persistent, veuillez contacter [aide et du Support][lnk-help-support].

**Pourquoi est-ce que je vois cette erreur lorsque j’ai un abonnement Azure ?** *Un abonnement Azure est nécessaire pour créer des solutions préconfigurées. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes.*

Si vous êtes sûr que vous avez un abonnement Azure, valider le client mappage pour votre abonnement et vérifiez que le client correct est sélectionné dans la liste déroulante. Si vous avez validé le client souhaité est correct, suivez le schéma ci-dessus et valider le mappage de votre abonnement et ce client AAD.

## <a name="next-steps"></a>Étapes suivantes

Pour continuer à apprendre à IoT Suite, voir comment vous pouvez [Personnaliser une solution préconfigurée][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

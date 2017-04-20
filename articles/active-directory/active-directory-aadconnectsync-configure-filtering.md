<properties
    pageTitle="Azure AD Connect synchronisation : configurer le filtrage | Microsoft Azure"
    description="Explique comment configurer le filtrage de synchronisation Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect synchronisation : configuration du filtrage
Grâce au filtrage, vous pouvez contrôler les objets qui doivent apparaître dans Azure AD à partir de votre annuaire local. La configuration par défaut prend tous les objets dans tous les domaines des forêts configurés. En règle générale, il s’agit de la configuration recommandée. Les utilisateurs finaux à l’aide de charges de travail Office 365, tels que Exchange Online et Skype pour les entreprises, facilitera à partir d’une liste d’adresses globale complète afin de pouvoir envoyer des messages électroniques et rappeler tout le monde. La configuration par défaut, ils se seraient l’expérience même que s’il s’agissait une implémentation locale d’Exchange ou Lync.

Dans certains cas, il est nécessaire pour apporter des modifications à la configuration par défaut. Voici quelques exemples :

- Vous envisagez d’utiliser la [topologie AD-directory multiple Azure](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Vous devez appliquer un filtre pour contrôler l’objet qui doit être synchronisé à un emplacement donné répertoire Azure AD.
- Vous exécutez un pilote Azure ou Office 365 et vous souhaitez uniquement un sous-ensemble d’utilisateurs dans Azure Active Directory. Dans le projet pilote petit, il n’est pas important de disposer d’une liste d’adresses globale complète pour montrer les fonctionnalités.
- Vous avez plusieurs comptes de service et d’autres comptes non personnelles que vous ne souhaitez pas inclure dans Azure AD.
- Pour des raisons de conformité vous ne supprimez tout utilisateur comptes locaux. Vous seulement les désactiver. Mais dans Azure AD vous voulez uniquement les comptes actifs doivent présenter.

Cet article explique comment configurer les différentes méthodes de filtrage.

> [AZURE.IMPORTANT]Microsoft ne prend pas en charge modification ou une opération de synchronisation Azure AD Connect en dehors de ces actions anciennement présentées. Une des actions suivantes, peut entraîner un état n’est pas cohérente ou non prises en charge de synchronisation Azure AD Connect et par conséquent, Microsoft ne peut pas fournir le support technique pour ces déploiements.

## <a name="basics-and-important-notes"></a>Concepts de base et les notes importantes
Dans Azure AD Connect synchroniser, vous pouvez activer le filtrage à tout moment. Si vous commencez par une configuration par défaut de la synchronisation d’annuaires et configurez le filtrage, les objets qui sont filtrés sont ne sont plus synchronisés avec Azure AD. À la suite de cette modification, tous les objets dans Azure AD qui ont été synchronisés précédemment mais qui ont été filtrés puis sont supprimés dans Azure Active Directory.

Avant de commencer à apporter des modifications de filtrage, assurez-vous que vous [désactiver la tâche planifiée](#disable-scheduled-task) afin que vous ne pas accidentellement exportez les modifications que vous n’avez pas encore vérifié pour être correcte.

Dans la mesure où le filtrage peut supprimer plusieurs objets en même temps, souhaité pour vous assurer que vos nouveaux filtres sont corrects avant de commencer l’exportation de toutes les modifications à Azure Active Directory. Lorsque vous avez terminé les étapes de configuration, il est recommandé de suivre les [étapes de vérification](#apply-and-verify-changes) avant d’exporter et apporter des modifications à Azure AD.

Pour vous protéger contre la suppression de nombreux objets par inadvertance, la fonctionnalité [pas de suppression accidentelle](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) est activée par défaut. Si vous supprimez le nombre d’objets en raison de filtrage (500 par défaut), vous devez suivre les étapes décrites dans cet article pour autoriser les suppressions traitée à Azure Active Directory.

Si vous utilisez une version avant novembre 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), modifiez la configuration du filtre et que vous utilisez la synchronisation de mot de passe, vous devez déclencher une synchronisation complète de tous les mots de passe une fois que vous avez terminé la configuration. Pour savoir comment déclencher une synchronisation complète de mot de passe voir [déclencher une synchronisation complète de tous les mots de passe](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Si vous êtes sur 1.0.9125 ou version ultérieure, puis l’action régulières **synchronisation complète** calcule également si les mots de passe doivent être synchronisés et cette étape supplémentaire n’est plus nécessaire.

Si les objets **utilisateur** ont été supprimés par inadvertance dans Azure AD en raison d’une erreur de filtre, vous pouvez recréer les objets utilisateur dans Azure AD en supprimant vos configurations de filtrage et puis resynchroniser vos répertoires. Cette action restaure les utilisateurs à partir de la Corbeille dans Azure Active Directory. Toutefois, vous ne pouvez pas restaurer d’autres types d’objets. Par exemple, si vous supprimez accidentellement un groupe de sécurité et qu’il a été utilisé pour et une ressource, le groupe et ses utilisateurs ne peuvent pas être récupérés.

Azure AD Connect supprime uniquement les objets qu'il s’agisse d’une seule fois à la portée. S’il existe des objets dans Azure AD qui ont été créés par un autre moteur de synchronisation et ces objets ne sont pas dans la portée, ajoutant un filtrage ne supprimez pas les. Par exemple, si vous commencez avec un serveur de synchronisation d’annuaire et il crée une copie complète de votre répertoire entier dans Azure AD et vous installez un nouveau serveur de synchronisation Azure AD Connect en parallèle avec filtre activé à partir du début, il ne supprime pas les objets supplémentaires créés par la synchronisation d’annuaire.

La configuration du filtrage est conservée lorsque vous installez ou mettre à niveau vers une version plus récente d’Azure AD Connect. Il n’est toujours une meilleure pratique pour vérifier que la configuration a été pas été modifiée par inadvertance après une mise à niveau vers une version plus récente avant d’exécuter la première synchronisation successivement.

Si vous avez plus d’une forêt, les configurations de filtrage décrites dans cette rubrique doivent être appliquées à chaque forêt (en supposant que vous souhaitez que la configuration même pour chacun d'entre eux).

### <a name="disable-scheduled-task"></a>Désactiver une tâche planifiée
Pour désactiver le Planificateur intégré déclenchant un cycle de synchronisation toutes les 30 minutes, procédez comme suit :

1. Atteindre un PowerShell invite.
2. Exécuter `Set-ADSyncScheduler -SyncCycleEnabled $False` pour désactiver le planificateur.
3. Apportez les modifications en procédant comme décrit dans cette rubrique.
4. Exécuter `Set-ADSyncScheduler -SyncCycleEnabled $True` d’activer à nouveau le planificateur.

**Si vous utilisez une version Azure AD Connect avant 1.1.105.0**  
Pour désactiver la tâche planifiée déclenchant un cycle de synchronisation toutes les 3 heures, procédez comme suit :

1. Démarrer **Le Planificateur de tâches** à partir du menu Démarrer.
2. Directement sous **Bibliothèque du Planificateur de tâches**, recherchez la tâche nommée **Scheduler Azure AD Sync**, avec le bouton droit, puis sélectionnez **désactiver**.  
![Planificateur de tâches](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Vous pouvez maintenant apporter des modifications de configuration et exécuter le moteur de synchronisation manuellement à partir de la console du **Gestionnaire de service de synchronisation** .

Une fois que vous avez terminé toutes vos modifications filtrage, n’oubliez pas à venir précédent et **Activer** à nouveau la tâche.

## <a name="filtering-options"></a>Options de filtrage
Les types de configuration de filtrage suivants peuvent être appliqués à l’outil de synchronisation d’annuaire :

- [**Basées sur les groupes**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtrage basé sur un seul groupe peut uniquement être configuré lors de l’installation initiale à l’aide de l’Assistant installation. Il n’est pas davantage couvert dans cette rubrique.

- [**Domaine**](#domain-based-filtering): cette option vous permet de sélectionner les domaines qui se synchronisent à Azure Active Directory. Il permet également d’ajouter et supprimer des domaines de la configuration de moteur de synchronisation si vous apportez des modifications à votre infrastructure en local une fois que vous avez installé synchronisation Azure AD Connect.

- [**Basée sur unité d’organisation**](#organizational-unitbased-filtering): cette option de filtrage permet de sélectionner les unités d’organisation synchroniser à Azure Active Directory. Cette option s’applique à tous les types d’objets dans les unités d’organisation sélectionnées.

- [**Basée sur les attributs**](#attribute-based-filtering): cette option permet de filtrer les objets en fonction des valeurs d’attribut sur les objets. Vous pouvez également avoir différents filtres pour différents types d’objets.

Vous pouvez utiliser plusieurs options de filtre en même temps. Par exemple, vous pouvez utiliser le filtrage par unité d’organisation pour inclure uniquement les objets dans une unité d’organisation et l’heure du même basée sur les attributs filtrage pour filtrer davantage les objets. Lorsque vous utilisez plusieurs méthodes de filtrage, les filtres utilisent un et logique entre les filtres.

## <a name="domain-based-filtering"></a>Filtrage basé sur un domaine
Cette section fournit les étapes pour configurer le filtre de votre domaine. Si vous avez ajouté ou supprimé des domaines dans votre forêt après avoir installé Azure AD Connect, vous devez également mettre à jour de la configuration du filtrage.

La meilleure façon de modifier le filtrage basé sur le domaine est en exécutant l’installation Assistant et modifier le [domaine et unités d’organisation filtrage](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L’Assistant installation de l’automatisation de toutes les tâches décrites dans cette rubrique.

Vous devez uniquement suivez ces étapes si vous pour une raison quelconque ne pouvez pas exécuter l’Assistant installation.

Configuration de filtrage domaine se compose des étapes suivantes :

- [Sélectionnez les domaines](#select-domains-to-be-synchronized) qui doivent être inclus dans la synchronisation.
- Pour chaque domaine ajouté et supprimé, ajustez les [profils d’exécution](#update-run-profiles).
- [Appliquer et vérifier les modifications](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Sélectionner les domaines à synchroniser
**Pour définir le filtre de domaine, procédez comme suit :**

1. Se connecter au serveur qui exécute synchronisation Azure AD Connect en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrer le **Service de synchronisation** dans le menu Démarrer.
3. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien avec le type de **Services de domaine Active Directory**. À partir **Actions**, sélectionnez **Propriétés**.  
![Propriétés du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Cliquez sur **configurer des Partitions de l’annuaire**.
5. Dans la liste **Sélectionner des partitions d’annuaire** , sélectionnez et désélectionner les domaines selon vos besoins. Vérifiez que seuls les partitions que vous voulez synchroniser sont activées.  
![Partitions](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Si vous avez modifié votre locaux AD infrastructure et domaines ajoutés ou supprimés à partir de la forêt, puis cliquez sur le bouton **Actualiser** pour obtenir une liste à jour. Lorsque vous actualisez, vous êtes invité vos informations d’identification. Fournir des informations d’identification avec accès en lecture à Active Directory local. Il n’a pas comme étant l’utilisateur qui est renseignée au préalable dans la boîte de dialogue.  
![Actualisation requise](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Lorsque vous avez terminé, fermez la boîte de dialogue **Propriétés** en cliquant sur **OK**. Si vous avez supprimé des domaines de la forêt, un menu contextuel de message indiquant qu'un domaine a été supprimé et que la configuration seront nettoyés.
7. Continuer à ajuster les [profils d’exécution](#update-run-profiles).

### <a name="update-run-profiles"></a>Mettre à jour les profils d’exécution
Si vous avez mis à jour votre filtre de domaine, vous devez également mettre à jour les profils d’exécution.

1. Dans la liste des **connecteurs** , vérifiez que le lien vous avez modifié dans l’étape précédente est sélectionné. À partir **Actions**, sélectionnez **Configurer les profils exécuter**.  
![Connecteur profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Vous devez régler les profils suivants :

- Importation complète
- Synchronisation complète
- Importation delta
- Synchronisation delta
- Exporter

Pour chacun des cinq profils, procédez comme suit pour chaque domaine **ajouté** :

1. Sélectionnez le profil d’exécution, puis cliquez sur **Nouvelle étape**.
2. Dans la page **Configurer étape** , dans le menu déroulant **Type** , sélectionnez le type d’étape portant le même nom que le profil que vous configurez. Cliquez sur **suivant**.  
![Connecteur profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Dans la page **Configuration du connecteur** , dans le menu déroulant **Partition** , sélectionnez le nom du domaine que vous avez ajouté à votre filtre de domaine.  
![Profils d’exécution connecteur](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Pour fermer la boîte de dialogue **Configurer le profil de s’exécuter** , cliquez sur **Terminer**.

Pour chacun des cinq profils, procédez comme suit pour chaque domaine **supprimé** :

1. Sélectionnez le profil d’exécution.
2. Si la **valeur** de l’attribut **Partition** est un GUID, sélectionnez l’étape Exécuter, puis cliquez sur **Supprimer l’étape**.  
![Connecteur profils d’exécution](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

Le résultat doit être que chaque domaine que vous voulez synchroniser doit être répertorié comme une étape dans chaque profil d’exécution.

Pour fermer la boîte de dialogue **Configuration des profils de s’exécuter** , cliquez sur **OK**.

- Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrage basé sur une unité d’organisation
La meilleure façon de modifier le filtrage par unité d’organisation est en exécutant l’installation Assistant et modifier le [domaine et unités d’organisation filtrage](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L’Assistant installation de l’automatisation de toutes les tâches décrites dans cette rubrique.

Vous devez uniquement suivez ces étapes si vous pour une raison quelconque ne pouvez pas exécuter l’Assistant installation.

**Pour configurer d’organisation unité – basée sur le filtrage, procédez comme suit :**

1. Se connecter au serveur qui exécute synchronisation Azure AD Connect en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrer le **Service de synchronisation** dans le menu Démarrer.
3. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien avec le type de **Services de domaine Active Directory**. À partir **Actions**, sélectionnez **Propriétés**.  
![Propriétés du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Cliquez sur la **Configuration des Partitions du répertoire**, sélectionnez le domaine que vous voulez configurer, puis cliquez sur **conteneurs**.
5. Lorsque vous y êtes invité, fournissent des informations d’identification avec accès en lecture à Active Directory local. Il n’a pas comme étant l’utilisateur qui est renseignée au préalable dans la boîte de dialogue.
6. Dans la boîte de dialogue **Sélectionnez les conteneurs** , désactivez les unités d’organisation que vous ne souhaitez pas synchroniser avec l’annuaire cloud, puis cliquez sur **OK**.  
![UNITÉ D’ORGANISATION](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - Le conteneur **ordinateurs** doit déjà être sélectionné pour vos ordinateurs Windows 10 pour être synchronisé avec succès avec Azure AD. Si votre domaine rejoint ordinateurs se trouvent dans d’autres unités, vérifiez que ceux-ci sont sélectionnés.
  - Le conteneur **ForeignSecurityPrincipals** doit déjà être sélectionné si vous avez plusieurs forêts approbation. Ce conteneur permet d’appartenance au groupe de sécurité forêt croisée être résolus.
  - L’unité d’organisation **RegisteredDevices** doit déjà être sélectionnée si vous avez activé la fonctionnalité d’écriture différée appareil. Si vous utilisez une autre fonctionnalité d’écriture différée, par exemple d’écriture différée du groupe, assurez-vous que ces emplacements sont sélectionnés.
  - Sélectionnez une autre unité d’organisation où se trouvent les utilisateurs, iNetOrgPersons, groupes, Contacts et ordinateurs. Dans l’image, tous les ils sont situés dans le ManagedObjects OU.
7. Lorsque vous avez terminé, fermez la boîte de dialogue **Propriétés** en cliquant sur **OK**.
8. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Filtrage basé sur un attribut
Vérifiez que vous êtes le 2015 novembre ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou plus tard générez pour ces étapes pour l’utiliser.

Attribut en fonction de filtrage est la façon la plus flexible filtrer les objets. Vous pouvez utiliser la puissance de [mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md) pour contrôler presque chaque aspect de lorsqu’un objet doit être synchronisé sur Azure AD.

Filtrage peut être appliqué à la fois sur [entrante](#inbound-filtering) d’Active Directory vers le métaverse et [sortant](#outbound-filtering) du métaverse à Azure Active Directory. Il est recommandé d’appliquer un filtrage sur entrant car il s’agit plus facile de mettre à jour. Filtrage sortant doit être utilisé uniquement si cela est nécessaire pour participer à des objets à partir de plusieurs forêt que l’évaluation puisse avoir lieu.

### <a name="inbound-filtering"></a>Filtrage entrant
Le filtrage en fonction entrant utilise la configuration par défaut dans lequel les objets accédant à Azure AD doivent avoir la cloudFiltered attribut métaverse non définie sur une valeur à synchroniser. Si la valeur de cet attribut est définie sur **True**, l’objet n’est pas synchronisé. Il ne doit pas être défini sur **False** par la conception. Pour vous assurer que d’autres règles ont la possibilité de contribuer une valeur, cet attribut est censée uniquement les valeurs **Vrai** ou **NULL** (absent).

Dans le filtrage entrant, vous utilisez la puissance **d’étendue** pour déterminer quels objets doivent ou ne doivent pas être synchronisées. Il s’agit de l’endroit où vous apporter des modifications aux besoins de votre organisation. Le module étendue possède **regrouper** et **clause** pour déterminer si une règle de synchronisation doit être dans l’étendue. Un **groupe** contient un ou plusieurs **clause**. Il existe un et logique entre plusieurs clauses et une logique ou entre plusieurs groupes.

Examinons un exemple :  
![Étendue](./media/active-directory-aadconnectsync-configure-filtering/scope.png) cela doit être lu comme **(service = informatique) ou (département = ventes et c = US)**.

Dans les exemples et la procédure ci-dessous, vous utilisez l’objet utilisateur par exemple, mais vous pouvez l’utiliser pour tous les types d’objets.

Dans les exemples ci-dessous, la valeur de priorité précédez 500. Cette valeur permet de que gérer les règles une fois les règles de prédéfinies (une priorité élevée, valeur numérique plus élevée).

#### <a name="negative-filtering-do-not-sync-these"></a>Négative filtrage, « pas synchroniser ces »
Dans l’exemple suivant, vous filtrez (pas synchroniser) tous les utilisateurs où **extensionAttribute15** ont la valeur **NoSync**.

1. Se connecter au serveur qui exécute synchronisation Azure AD Connect en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrez **l’Éditeur de règles de synchronisation** dans le menu Démarrer.
3. Vérifiez que **entrant** est sélectionné, puis cliquez sur **Ajouter une nouvelle règle**.
4. Attribuer à la règle un nom descriptif, tel que «*dans depuis Active Directory – utilisateur DoNotSyncFilter*». Sélectionnez la forêt appropriée, **utilisateur** en tant que le **type d’objet SC**et **personne** en tant que le **type d’objet MV**. Comme **Type de liaison**, cliquez sur **joindre** et selon la priorité, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple 500), puis sur **suivant**.  
![Entrant 1 description](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. Dans le **filtre Scoping**, cliquez sur **Ajouter un groupe**, cliquez sur **Ajouter une Clause**, puis dans l’attribut sélectionnez **ExtensionAttribute15**. Vérifiez que l’opérateur est défini sur **égal** , puis tapez la valeur **NoSync** dans la zone valeur. Cliquez sur **suivant**.  
![Entrant étendue 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Laissez les règles **rejoindre** vide, puis cliquez sur **suivant**.
7. Cliquez sur **Ajouter la Transformation**, sélectionnez **TauxChange** à **constante**, sélectionnez l' attribut cible **cloudFiltered** et dans la zone de texte Source, tapez **Vrai**. Cliquez sur **Ajouter** pour enregistrer la règle.  
![Entrant transformation 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positif filtrage, « synchroniser uniquement ces »
Expression de filtrage positif peut être plus complexe dans la mesure où vous devez également tenir compte objets qui ne sont pas visibles à synchroniser, tels que des salles de conférence.

L’option de filtrage positive nécessite deux règles de synchronisation. Une (ou plusieurs) à la portée correcte des objets à synchroniser et le second la synchronisation collecte règle ce filtre à tous les objets qui n’ont pas encore été identifiés en tant qu’objet qui doit être synchronisé.

Dans l’exemple suivant, vous ne synchronisez les objets utilisateur où l’attribut service a la valeur **ventes**.

1. Se connecter au serveur qui exécute synchronisation Azure AD Connect en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrez **l’Éditeur de règles de synchronisation** dans le menu Démarrer.
3. Vérifiez que **entrant** est sélectionné, puis cliquez sur **Ajouter une nouvelle règle**.
4. Donnez un nom descriptif, tel que «*dans depuis Active Directory – utilisateur ventes synchroniser*» à la règle. Sélectionnez la forêt appropriée, **utilisateur** en tant que le **type d’objet SC**et **personne** en tant que le **type d’objet MV**. Comme **Type de liaison**, cliquez sur **joindre** et selon la priorité, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple 501), puis sur **suivant**.  
![Entrant description 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. Dans **Scoping filtre**, cliquez sur **Ajouter un groupe**, cliquez sur **Ajouter une Clause**, puis dans l’attribut sélectionnez **service**. Vérifiez que l’opérateur est défini sur **égal** , puis tapez la valeur **ventes** dans la zone valeur. Cliquez sur **suivant**.  
![Entrant étendue 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Laissez les règles **rejoindre** vide, puis cliquez sur **suivant**.
7. Cliquez sur **Ajouter la Transformation**, sélectionnez **TauxChange** à **constante**, sélectionnez l' attribut cible **cloudFiltered** et dans la zone de texte Source, tapez **False**. Cliquez sur **Ajouter** pour enregistrer la règle.  
![Entrant transformation 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Il s’agit d’un cas spécial dans lequel vous définissez cloudFiltered explicitement sur False.

    Nous avons maintenant créer la règle de synchronisation pour tout.

8. Attribuer à la règle un nom descriptif, tel que «*dans depuis Active Directory – filtre pour tout utilisateur*». Sélectionnez la forêt appropriée, **utilisateur** en tant que le **type d’objet SC**et **personne** en tant que le **type d’objet MV**. En tant que **Type de liaison**, cliquez sur **joindre** et selon la priorité, tapez une valeur actuellement ne pas utilisée par une autre règle de synchronisation (par exemple 600). Vous avez sélectionné une priorité valeur supérieure (priorité moindre) que la règle de synchronisation précédente mais également gauche espace afin que nous puissions ajouter d’autres règles synchronisation filtrage ultérieurement lorsque vous voulez commencer la synchronisation des services supplémentaires. Cliquez sur **suivant**.  
![Entrant description 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Laissez **Scoping filtre** vide, puis cliquez sur **suivant**. Un filtre vide indique la règle doit être appliquée à tous les objets.
10. Laissez les règles **rejoindre** vide, puis cliquez sur **suivant**.
11. Cliquez sur **Ajouter la Transformation**, sélectionnez **TauxChange** à **constante**, sélectionnez l' attribut cible **cloudFiltered** et dans la zone de texte Source, tapez **Vrai**. Cliquez sur **Ajouter** pour enregistrer la règle.  
![Entrant transformation 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

Si vous le souhaitez, vous pouvez créer plus de règles du premier type où vous incluez plusieurs objets dans notre synchronisation.

### <a name="outbound-filtering"></a>Filtrage sortant
Dans certains cas, il est nécessaire d’effectuer le filtrage qu’après que les objets ont rejoint dans le méta-verse. Cela peut, par exemple, se requis pour examiner l’attribut de messagerie à partir de la forêt ressource et de l’attribut userPrincipalName à partir de la forêt du compte pour déterminer si un objet doit être synchronisé. Dans ce cas, vous créez le filtrage sur la règle sortante.

Dans cet exemple, vous modifiez les utilisateurs conséquent que filtrage où mail et userPrincipalName ne se terminent par @contoso.com sont synchronisés :

1. Se connecter au serveur qui exécute synchronisation Azure AD Connect en utilisant un compte qui est membre du groupe de sécurité **ADSyncAdmins** .
2. Démarrez **l’Éditeur de règles de synchronisation** dans le menu Démarrer.
3. Sous **Type de règles**, cliquez sur **sortant**.
4. Recherchez la règle nommée **les AAD – SOAInAD de participer à des utilisateurs**. Cliquez sur **Modifier**.
5. Dans la fenêtre contextuelle, répondez **Oui** pour créer une copie de la règle.
6. Dans la page **Description** , modifier le niveau de priorité sur une valeur non utilisée, par exemple 50.
7. Dans la barre de navigation gauche, cliquez sur **filtre Scoping** . Cliquez sur **Ajouter la clause**, attribut, sélectionnez **courrier**, sélectionnez opérateur **ENDSWITH**et type de valeur **@contoso.com**. Cliquez sur **Ajouter la clause**, sélectionnez attribut **userPrincipalName**, sélectionnez opérateur **ENDSWITH**et type de valeur **@contoso.com**.
8. Cliquez sur **Enregistrer**.
9. Pour terminer la configuration, [appliquer et vérifier les modifications](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Appliquer et vérifier les modifications
Une fois que vous avez apporté vos modifications de configuration, celles-ci doivent être appliquées aux objets déjà présents dans le système. Il pourrait également être qu’objets n’est pas actuellement dans le moteur de synchronisation doivent être traités et le moteur de synchronisation a besoin de lire le système source pour vérifier que son contenu.

Si vous avez modifié la configuration à l’aide de **domaine** ou **l’unité d’organisation** filtrage, vous devez procéder à **l’importation intégrale** suivie de **synchronisation Delta**.

Si vous avez modifié la configuration à l’aide de filtrage d' **attribut** , vous devez effectuer **une synchronisation complète**.

Procédez comme suit :

1. Démarrer le **Service de synchronisation** dans le menu Démarrer.
2. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien où vous avez apporté une configuration modifier plus haut. À partir **Actions**, sélectionnez **exécuter**.  
![Connecteur exécuter](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. Dans les **profils d’exécution**, sélectionnez l’opération mentionnée dans la section précédente. Si vous devez effectuer deux actions, exécutez le second issue de la première partie (la colonne **état** est **inactif** pour le lien sélectionné).

Après la synchronisation, toutes les modifications sont transférées pour être exportés. Avant des modifications dans Azure Active Directory, que vous souhaitez vérifier que toutes ces modifications sont correctes.

1. Démarrer une invite de commandes et accédez à`%Program Files%\Microsoft Azure AD Sync\bin`
2. Exécuter :`csexport "Name of Connector" %temp%\export.xml /f:x`  
Le nom du connecteur sont accessibles dans le Service de synchronisation. Il comporte un nom similaire à « contoso.com – AAD » pour Azure AD.
3. Exécuter :`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Vous avez maintenant un fichier dans % temp% nommé export.csv qui peut être examiné dans Microsoft Excel. Ce fichier contient toutes les modifications qui doivent être exportés.
5. Apportez les modifications nécessaires à la configuration ou de données et exécuter ces étapes à nouveau (importation, synchroniser et vérifier) jusqu'à ce que les modifications qui doivent être exportés sont prévues.

Lorsque vous êtes satisfait, exporter les modifications apportées à Azure Active Directory.

1. Sélectionnez les **connecteurs** et dans la liste de **liens** , sélectionnez le lien de AD Azure. À partir **Actions**, sélectionnez **exécuter**.
2. Dans les **profils d’exécution**, sélectionnez **Exporter**.
3. Si vos modifications de configuration supprimer un grand nombre d’objets, puis vous voyez un message d’erreur concernant l’exportation lorsque le nombre est supérieur au seuil configuré (par défaut 500). Si vous voyez cette erreur, vous devez désactiver temporairement la fonctionnalité [pas de suppression accidentelle](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Il est temps de réactiver le planificateur.

1. Démarrer **Le Planificateur de tâches** à partir du menu Démarrer.
2. Directement sous **Bibliothèque du Planificateur de tâches**, recherchez la tâche nommée **Scheduler Azure AD Sync**, avec le bouton droit, puis sélectionnez **Activer**.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

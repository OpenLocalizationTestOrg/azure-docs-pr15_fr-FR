<properties
   pageTitle="Azure AD Connect : Version historique | Microsoft Azure"
   description="Cette rubrique répertorie toutes les versions d’Azure AD Connect et Azure AD Sync"
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
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect : Version historique des versions

L’équipe Azure Active Directory met à jour régulièrement Azure AD Connect avec les nouvelles fonctionnalités. Pas tous les ajouts sont appliquent à toutes les audiences.

Cet article est conçu pour vous aider à effectuer le suivi des versions qui ont été publiées et à comprendre si vous devez mettre à jour vers la version la plus récente ou non.

Il s’agit de la liste des rubriques connexes :

Rubrique |  
--------- | --------- |
Étapes à suivre pour mettre à niveau à partir d’Azure AD Connect | Différentes méthodes vers la version [mise à niveau depuis une version antérieure vers la dernière](active-directory-aadconnect-upgrade-previous-version.md) Azure AD Connect.
Autorisations requises | Pour les autorisations requises pour appliquer une mise à jour, voir [comptes et des autorisations](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Télécharger| [Téléchargement Azure AD se connecter](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Publié : 2016 août

**Problèmes résolus :**

- Modifications apportées à synchroniser intervalle n'a pas lieu jusqu'à après la prochaine synchronisation.
- Azure AD Connect Assistant n’accepte pas de compte Azure AD dont nom d’utilisateur commence par un trait de soulignement (\_).
- Azure AD Connect Assistant ne parvient pas à s’authentifier compte Azure AD fourni si le mot de passe de compte contient trop de caractères spéciaux. Message d’erreur « Impossible de valider les informations d’identification. Une erreur inattendue s’est produite. » est renvoyée.
- La désinstallation serveur intermédiaire désactive la synchronisation de mot de passe dans le client Azure AD et entraîne la synchronisation de mot de passe à échouer en raison de serveur actif.
- Synchronisation de mot de passe échoue dans les cas rares lorsqu’il n’y a aucun hachage de mot de passe qui se trouve sur l’utilisateur.
- Lorsque serveur Azure AD Connect est activé pour le mode de mise en attente, l’écriture différée de mot de passe n’est pas désactivée temporairement.
- Azure AD Connect Assistant n’affiche pas la synchronisation de mot de passe réel et configuration d’écriture différée de mot de passe lorsque serveur est en mode de mise en attente. Il leur apparaît toujours comme étant désactivée.
- Modifications de la configuration pour la synchronisation de mot de passe et mot de passe d’écriture différée ne sont pas conservées par l’Assistant Azure AD Connect lorsque serveur est en mode de mise en attente.

**Améliorations apportées :**

- Cmdlet démarrer ADSyncSyncCycle mis à jour pour indiquer si elle est en mesure de démarrer correctement un nouveau cycle de synchronisation ou non.
- Cmdlet Stop ADSyncSyncCycle ajouté pour terminer le cycle de synchronisation et opération qui sont actuellement en cours.
- Cmdlet Stop ADSyncScheduler mis à jour pour mettre fin à cycle de synchronisation et opération qui sont actuellement en cours.
- Lorsque vous configurez [Extensions Directory](active-directory-aadconnectsync-feature-directory-extensions.md) dans l’Assistant Azure AD Connect, attribut Active Directory de type « Chaîne télétexte » pouvant être sélectionné maintenant.

## <a name="111890"></a>1.1.189.0
Publié : 2016 juin

**Problèmes résolus et améliorations :**

- Azure AD Connect peut désormais être installé sur un serveur compatible FIPS.
    - Pour la synchronisation de mot de passe, voir [synchronisation de mot de passe et FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Correction d’un problème où un nom NetBIOS ne peut pas être résolu en nom de domaine complet dans le connecteur Active Directory.

## <a name="111800"></a>1.1.180.0
De publication : 2016 mai

**Nouvelles fonctionnalités :**

- Vous avertit et vous permet de vérification de domaines si vous n’avez pas le faire avant d’exécuter Azure AD Connect.
- Prise en charge pour [l’Allemagne Cloud Microsoft](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Prise en charge pour l’infrastructure [cloud de Microsoft Azure pour le gouvernement](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) dernière à nouveau aux exigences des URL.

**Problèmes résolus et améliorations :**

- Un filtrage supplémentaire afin de simplifier connaître les règles de synchronisation à l’éditeur de règle de synchronisation.
- Amélioration des performances lors de la suppression d’un espace de connecteur.
- Corrigé un problèmes lors du même objet a été supprimé et ajouté dans la même exécuter (appelés supprimer/ajouter).
- Une règle de synchronisation désactivée ne seront plus réactiver inclus des objets et actualiser les attributs de mise à niveau ou répertoire du schéma.

## <a name="111300"></a>1.1.130.0
Publié : 2016 d’avril :

**Nouvelles fonctionnalités :**

- Prise en charge des attributs à valeurs multiples [Répertoire Extensions](active-directory-aadconnectsync-feature-directory-extensions.md).
- Prise en charge pour plusieurs variations de configuration à la [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) vers être considéré comme pouvant être mise à niveau.
- Ajouter des applets de commande pour [planificateur personnalisé](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
De publication : 2016 mars

**Problèmes résolus :**

- Apportées que Express Installer ne peuvent pas être utilisé sur Windows Server 2008 (pré-R2) depuis la synchronisation de mot de passe n'est pas pris en charge sur ce système d’exploitation.
- Mise à niveau à partir de la synchronisation d’annuaire avec une configuration filtre personnalisé ne fonctionnent pas comme prévu.
- Lorsque la mise à niveau vers une version plus récente et aucune modification à la configuration, une importation/synchronisation complète ne doit pas être planifiée.

## <a name="111100"></a>1.1.110.0
Publié : 2016 février

**Problèmes résolus :**

- Mise à niveau à partir de versions précédentes ne fonctionne pas si l’installation n’est pas dans le dossier **C:\Program Files** par défaut.
- Si vous installez et annuler la sélection de **Démarrer le processus de synchronisation...** à la fin de l’Assistant installation, relançant l’Assistant installation ne permettra pas le planificateur.
- Le planificateur ne fonctionnera pas comme prévu sur des serveurs où le format de date/heure n’est pas en-US. Il se bloque également `Get-ADSyncScheduler` pour retourner heures correctes.
- Si vous avez installé une version antérieure d’Azure AD Connect avec les services ADFS dans l’option de connexion et la mise à niveau, vous ne pouvez pas exécuter l’Assistant installation à nouveau.

## <a name="111050"></a>1.1.105.0
Publié : 2016 février

**Nouvelles fonctionnalités :**

- Fonctionnalité de [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) pour les clients de paramètres Express.
- Prise en charge de l’administrateur global à l’aide de l’authentification Multifacteur et PIM dans l’Assistant installation.
    - Vous devez autoriser votre serveur proxy pour également autoriser le trafic vers https://secure.aadcdn.microsoftonline-p.com si vous utilisez l’authentification Multifacteur.
    - Vous devez ajouter https://secure.aadcdn.microsoftonline-p.com à votre liste sites de confiance pour l’authentification Multifacteur fonctionnent correctement.
- Autoriser la modification des méthode de connexion de l’utilisateur après l’installation initiale.
- Autoriser le [filtrage de domaine et unité d’organisation](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) dans l’Assistant installation. Ceci permet également de connexion à des forêts où pas tous les domaines sont disponibles.
- [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) est intégré au moteur de synchronisation.

**Fonctionnalités de l’aperçu avant la promotion disponible :**

- [Appareil écriture différée non validées](active-directory-aadconnect-feature-device-writeback.md).
- [Extensions de répertoire](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nouvelles fonctionnalités d’aperçu :**

- La nouvelle valeur par défaut synchroniser cycle intervalle est de 30 minutes. Utilisé pour être 3 heures pour toutes les versions antérieures. Prise en charge pour modifier le comportement de [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) .

**Problèmes résolus :**

- La page domaines vérifier le DNS n’a pas reconnus toujours les domaines.
- Informations d’identification d’administration de domaine lors de la configuration des services ADFS est demandé.
- Les locaux comptes AD ne sont pas reconnus par l’Assistant installation se situés dans un domaine avec une autre arborescence DNS du domaine racine.

## <a name="1091310"></a>1.0.9131.0
Publié : le mois de décembre 2015

**Problèmes résolus :**

- Synchronisation de mot de passe peut-être ne pas fonctionner lorsque vous modifiez les mots de passe dans les services AD DS, mais fonctionne lorsque vous configurez votre mot de passe.
- Lorsque vous avez un serveur proxy, l’authentification Azure AD peut échouer pendant l’installation ou de désactiver la mise à niveau dans la page de configuration.
- Mise à jour d’une version précédente d’Azure AD Connect intégral SQL Server échoue si vous n’êtes pas administrateur système dans SQL.
- Mise à jour d’une version précédente d’Azure AD Connect avec une télécommande SQL Server affiche l’erreur « Impossible d’accéder à la base de données ADSync SQL ».

## <a name="1091250"></a>1.0.9125.0
Publié : 2015 novembre

**Nouvelles fonctionnalités :**

- Pouvez reconfigurer la ADFS d’approbation Azure AD.
- Pouvoir actualiser le schéma Active Directory et régénérer les règles de synchronisation.
- Pouvez désactiver une règle de synchronisation.
- Pouvez définir « AuthoritativeNull » en tant que nouveau littéral dans une règle de synchronisation.

**Nouvelles fonctionnalités d’aperçu :**

- [Azure AD se connecter au niveau pour la synchronisation](active-directory-aadconnect-health-sync.md).
- Prise en charge pour la synchronisation de mot de passe [Azure Active Directory Domain Services](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nouveau scénario pris en charge :**

- Prend en charge plusieurs organisations Exchange en local. Pour plus d’informations, voir [déploiements hybrides avec plusieurs forêts Active Directory](https://technet.microsoft.com/library/jj873754.aspx) .

**Problèmes résolus :**

- Problèmes de synchronisation de mot de passe :
    - Un objet déplacé de hors de portée à dans la portée n’ont pas de son mot de passe synchronisé. Cette comprend unité d’organisation et le filtrage de l’attribut.
    - Sélection d’une nouvelle unité d’organisation pour inclure synchronisés ne nécessite pas une synchronisation de mot de passe.
    - Lorsqu’un utilisateur désactivé est activé le mot de passe n’est pas synchronisée.
    - La file d’attente de nouvelle tentative de mot de passe est illimité et la limite de 5 000 objets à être supprimée précédente a été supprimée.
    - [Amélioration de dépannage](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Impossible de se connecter à Active Directory à niveau forêt fonctionnelle Windows Server 2016.
- Impossible de modifier le groupe utilisé pour le filtrage de groupe après l’installation initiale.
- N’est plus créera un profil utilisateur sur le serveur Azure AD Connect pour chaque utilisateur effectuant un changement de mot de passe avec mot de passe d’écriture différée activée.
- Pas en mesure d’utiliser les valeurs entier Long synchronisés étendues de règles.
- La case à cocher « périphérique d’écriture différée » reste désactivé si contrôleur de domaine inaccessible.

## <a name="1086670"></a>1.0.8667.0
Publié : 2015 août

**Nouvelles fonctionnalités :**

- L’Assistant installation Azure AD Connect est désormais localisé à toutes les langues de Windows Server.
- Prise en charge pour le compte déverrouiller lors de l’utilisation de gestion des mots de passe Azure AD.

**Problèmes résolus :**

- Assistant d’installation Azure AD Connect se bloque si un autre utilisateur persiste installation plutôt que la personne qui a démarré tout d’abord l’installation.
- Si une désinstallation précédente d’Azure AD Connect échoue désinstaller synchronisation Azure AD Connect proprement, il n’est pas possible de réinstaller.
- Impossible d’installer Azure AD Connect à l’aide d’installation Express si l’utilisateur n’est pas dans le domaine racine de la forêt ou si une version localisée d’Active Directory est utilisée.
- Si le nom de domaine complet du compte d’utilisateur Active Directory ne peut pas être résolu, un message d’erreur « Impossible de valider le schéma » confusion s’affiche.
- Si le compte utilisé sur le connecteur Active Directory est modifié en dehors de l’Assistant, l’Assistant échoue lors des exécutions suivantes.
- Azure AD Connect parfois ne parvient pas à installer sur un contrôleur de domaine.
- Ne peut pas activer et désactiver « En mode mise en attente » si attributs d’extension ont été ajoutés.
- Écriture différée de mot de passe ne fonctionne pas dans une configuration en raison d’un mot de passe erroné sur le connecteur Active Directory.
- Synchronisation d’annuaire ne peut pas être mis à niveau si le nom de domaine est utilisée dans l’attribut filtrage.
- Excessive de l’UC lors de l’utilisation de réinitialisation du mot de passe.

**Fonctionnalités de supprimées :**

- La fonctionnalité d’aperçu [en écriture différée de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) a été supprimé temporairement en fonction des commentaires de nos clients Aperçu. Il sera ré-ajouté ultérieurement quand nous avons résolu les commentaires fourni.

## <a name="1086410"></a>1.0.8641.0
Publié : 2015 juin

**Version initiale de Azure AD Connect.**

Renommée à partir d’Azure AD Sync à Azure AD Connect.

**Nouvelles fonctionnalités :**

- Installation [Express paramètres](./connect/active-directory-aadconnect-get-started-express.md)
- Peut [configurer ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Peut [mettre à niveau à partir de la synchronisation d’annuaire](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Pas de suppression accidentelle](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Ainsi que [le mode de mise en attente](active-directory-aadconnectsync-operations.md#staging-mode)

**Nouvelles fonctionnalités d’aperçu :**

- [Écriture différée utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Groupe écriture différée non validée](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Écriture différée appareil](active-directory-aadconnect-feature-device-writeback.md)
- [Extensions d’annuaire](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
De publication : 2015 mai

**Nouvelle exigence :**

- La synchronisation Active Directory Azure requiert désormais .net framework, version 4.5.1 doit être installé.

**Problèmes résolus :**

- Mot de passe en écriture différée à partir d’Azure AD ignore présentant un problème de connectivité servicebus.

## <a name="104910413"></a>1.0.491.0413
De publication : 2015 avril

**Problèmes résolus et améliorations :**

- Le connecteur Active Directory ne traite pas correctement les suppressions si la Corbeille est activée et qu’il existe plusieurs domaines de la forêt.
- Les performances des opérations d’importation a été améliorée pour le connecteur Azure Active Directory.
- Lorsqu’un groupe a dépassé la limite d’appartenance (par défaut, la limite est définie à 50 Ko objets), le groupe a été supprimé dans Azure Active Directory. Le nouveau comportement est que le groupe restera, une erreur est levée et aucune nouvelle modification de l’appartenance aux n’est exportées.
- Un nouvel objet ne peut pas être mis en service si une suppression intermédiaire avec le même nom de domaine est déjà présente dans l’espace de connecteur.
- Certains objets sont marqués pour synchronisée pendant une synchronisation delta bien qu’aucune modification n’est mis en place sur l’objet.
- Forcer une synchronisation de mot de passe supprime également la liste contrôleur de domaine par défaut.
- CSExportAnalyzer rencontre des problèmes avec certains États d’objets.

**Nouvelles fonctionnalités :**

- Une jointure peut désormais être connecté au type d’objet « ANY » dans la MV.

## <a name="104850222"></a>1.0.485.0222
Publié : les février 2015

**Améliorations apportées :**

- Performances améliorées.

**Problèmes résolus :**

- Synchronisation de mot de passe respecte l’attribut cloudFiltered utilisé en filtrant attribut. Les objets filtrés ne sera plus dans l’étendue pour la synchronisation de mot de passe.
- Synchronisation de mot de passe ne fonctionne pas dans les rares situations où la topologie avait très nombreux superflus.
- « Arrêté-serveur » lors de l’importation à partir du connecteur AD Azure après la gestion des appareils a été activée dans Azure AD/Intune.
- Participation à étrangère principaux de sécurité (FSP) à partir de plusieurs domaines dans la même forêt génère une erreur de jointure ambiguës.

## <a name="104751202"></a>1.0.475.1202
Publié : le mois de décembre 2014

**Nouvelles fonctionnalités :**

- Il est désormais pris en charge pour effectuer la synchronisation de mot de passe avec attribut en fonction de filtrage. Pour plus d’informations, voir [synchronisation de mot de passe avec filtrage](active-directory-aadconnectsync-configure-filtering.md).
- L’attribut msDS-ExternalDirectoryObjectID est écrites à Active Directory. Cette prise en charge pour les applications Office 365 à l’aide d’oauth2 ne pour accéder à la fois, en ligne et des boîtes aux lettres dans un déploiement Exchange hybride local.

**Problèmes de mise à niveau fixes :**

- Une version plus récente de l’assistant de connexion est disponible sur le serveur.
- Un chemin d’accès d’installation personnalisée a été utilisé pour installer la synchronisation Azure Active Directory.
- Un critère de jointure personnalisé non valide empêche la mise à niveau.

**Autres correctifs :**

- Fixer les modèles pour Office Pro Plus.
- Problèmes d’installation fixe dus au fait des noms d’utilisateur qui commencent par un tiret.
- Fixe perdre le paramètre sourceAnchor lors de l’exécution de l’Assistant installation une seconde fois.
- Suivi ETW fixe pour la synchronisation de mot de passe

## <a name="104701023"></a>1.0.470.1023
Publié : les octobre 2014

**Nouvelles fonctionnalités :**

- Synchronisation de mot de passe à partir de plusieurs locaux AD à Azure Active Directory.
- Installation localisée UI à toutes les langues de Windows Server.

**La mise à niveau à partir de disponibilité générale AADSync 1.0**

Si vous avez déjà installé Azure AD Sync, il est une étape supplémentaire, que vous devez effectuer au cas où vous avez modifié une des règles prédéfinies de la synchronisation. Une fois que vous avez mis à niveau vers la 1.0.470.1023 relâchez, la synchronisation de règles que vous avez modifiés sont dupliqués. Pour chaque règle de synchronisation modifiée, procédez comme suit :

- Recherchez la règle de synchronisation que vous avez modifié et notez les modifications.
- Supprimer la règle de synchronisation.
- Recherchez la nouvelle règle de synchronisation créés par la synchronisation Azure Active Directory et réappliquer les modifications.

**Autorisations pour le compte Active Directory**

Le compte Active Directory doit disposer des autorisations supplémentaires pour pouvoir lire les valeurs de hachage de mot de passe d’Active Directory. Les autorisations accordées à sont appelées « Réplication des changements de répertoire » et « Réplication Directory toutes les modifications ». Les deux autorisations sont nécessaires pour pouvoir lire les valeurs de hachage de mot de passe

## <a name="104190911"></a>1.0.419.0911
Publié : les septembre 2014

**Version initiale de la synchronisation Azure Active Directory.**

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

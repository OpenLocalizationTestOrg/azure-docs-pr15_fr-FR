<properties
   pageTitle="Azure AD Connect : Mettre à niveau depuis une version antérieure | Microsoft Azure"
   description="Explique les différentes méthodes de mise à niveau vers la dernière version d’Azure Active Directory se connecter, y compris la mise à niveau et la migration de basculement."
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
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect : Mise à niveau depuis une version antérieure vers la dernière
Cette rubrique décrit les différentes méthodes que vous pouvez utiliser pour mettre à niveau votre installation Azure AD Connect vers la dernière version. Nous vous conseillons de conserver vous-même à jour avec les versions de Azure AD Connect. La procédure décrite dans [faire pivoter migration](#swing-migration) est également utilisée lorsque vous effectuez une configuration significative modification.

Si vous souhaitez mettre à niveau à partir de la synchronisation d’annuaire, reportez-vous à la place à [mettre à niveau à partir de l’outil de synchronisation Azure Active Directory (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Il existe quelques différentes stratégies pour mettre à niveau Azure AD Connect.

Méthode | Description
--- | ---
[Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) | Pour les clients avec une installation rapide, il s’agit de la méthode la plus simple.
[Mise à niveau](#in-place-upgrade) | Si vous avez un serveur unique, mettez à niveau l’installation place sur le même serveur.
[Faire pivoter de migration](#swing-migration) | Avec deux serveurs, vous pouvez préparer un des serveurs avec la nouvelle version ou la configuration et modifier serveur actif lorsque vous êtes prêt.

Pour les autorisations requises, voir [autorisations requises pour la mise à niveau](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Mise à niveau
Une mise à niveau en place fonctionne pour le déplacement de la synchronisation Azure Active Directory ou Azure AD Connect. Il ne fonctionnera pas pour la synchronisation d’annuaire ou pour une solution avec FIM + le connecteur Azure Active Directory.

Cette méthode est préférable lorsque vous avez un serveur unique et inférieure à environ 100 000 objets. Si des modifications sont apportées aux règles prédéfinies de synchronisation, une importation complète et synchronisation complète se produisent après la mise à niveau. Ainsi que la nouvelle configuration est appliquée à tous les objets existants dans le système. Cela peut prendre quelques heures selon le nombre d’objets dans l’étendue du moteur de synchronisation. Le Planificateur de synchronisation delta normal, par défaut toutes les 30 minutes est suspendu mais continue de synchronisation de mot de passe. Vous pouvez effectuer la mise à niveau en place un week-end. S’il n’y a aucune modification à la configuration de prédéfinies avec la nouvelle version Azure AD Connect, puis une importation delta normal/synchronisation démarre à la place.  
![Mise à niveau](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si vous avez apporté des modifications aux règles de la synchronisation de prédéfinies, il seront ramené à la configuration par défaut sur la mise à niveau. Pour vous assurer que votre configuration est conservée entre les mises à niveau, vérifiez que les modifications sont apportées comme décrit dans les [meilleures pratiques pour la modification de la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Faire pivoter de migration
Si vous avez un déploiement complexe ou très grand nombre d’objets, il peut être difficile d’effectuer une mise à niveau en place sur le système actif. Cela peut prendre pour certains clients plusieurs jours et pendant cette période aucune modification delta n’est traitées. Cette méthode est également utilisée lorsque vous souhaitez apporter des modifications substantielles à votre configuration et que vous voulez les essayer avant que celles-ci sont déplacées vers le cloud.

La méthode recommandée pour ces scénarios consiste à utiliser une migration élan. Vous avez besoin de deux serveurs (minimum), un actif et un serveur de test. Le serveur actif (traits pleins bleus dans l’illustration ci-dessous) est responsable de la charge de production active. Le serveur intermédiaire (lignes en pointillés violets dans l’illustration ci-dessous) est préparé avec la nouvelle version ou la configuration et lorsqu’il est entièrement prêt, ce serveur est rendu actif. Le serveur actif précédente, maintenant avec l’ancienne version ou la configuration installé, est effectué le serveur intermédiaire et mis à niveau.

Les deux serveurs peuvent utiliser différentes versions. Par exemple, le serveur actif que vous souhaitez retirer peut utiliser la synchronisation Azure Active Directory et le nouveau serveur intermédiaire utilisations Azure AD Connect. Si vous utilisez élan migration pour développer une nouvelle configuration, il est conseillé de disposer des mêmes versions sur les deux serveurs.  
![Serveur intermédiaire](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Remarque : Il a été noté que certains clients préfèrent disposer trois ou quatre serveurs pour ce scénario. Lorsque le serveur intermédiaire est mis à niveau, vous n’avez pas un serveur de sauvegarde dans le cas d’un [sinistre](active-directory-aadconnectsync-operations.md#disaster-recovery). Avec trois ou quatre serveurs, un ensemble de serveurs principal/secondaire avec la nouvelle version peut être préparé, s’assurer qu’il n’y a toujours un serveur intermédiaire prêt à prendre le contrôle.

Ces étapes fonctionne également pour déplacer d’Azure AD Sync ou d’une solution avec FIM + le connecteur Azure Active Directory. Ces étapes ne fonctionnent pas pour la synchronisation d’annuaire, mais la même méthode (également appelée déploiement en parallèle) de migration à basculement étapes pour la synchronisation d’annuaire, vous pouvez trouver dans [Mettre à niveau Azure Active Directory sync (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Faire pivoter des étapes de la migration

1. Si vous utilisez Azure AD Connect sur les deux serveurs et que vous prévoyez d’effectuer uniquement un changement de configuration, vérifiez que votre serveur actif et serveur intermédiaire sont tous deux à l’aide de la même version. Qui facilite la comparaison des différences ultérieurement. Si vous mettez à niveau à partir de la synchronisation Azure Active Directory, ces serveurs ont des versions différentes. Si vous mettez à niveau à partir d’une version plus ancienne Azure AD Connect, il est recommandé de commencer avec les deux serveurs à l’aide de la même version, mais n’est pas obligatoire.
2. Si vous avez apporté une configuration personnalisée et votre serveur de test n’a pas il, suivez les étapes sous [déplacer configuration personnalisée d’actif à serveur intermédiaire](#move-custom-configuration-from-active-to-staging-server).
3. Si vous mettez à niveau à partir d’une version antérieure d’Azure AD Connect, mettre à niveau du serveur intermédiaire vers la dernière version. Si vous migrez à partir d’Azure AD Sync, puis installer Azure AD Connect sur votre serveur de test.
4. Laissez le moteur de synchronisation exécuter importation complète et synchronisation complète sur votre serveur de test.
5. Vérifiez que la nouvelle configuration n’a pas déclenchés les modifications inattendues à l’aide de la procédure indiquée sous **Vérifier** dans la rubrique [vérifier la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si un élément n’est pas synchroniser et importer comme prévu, correcte, exécuter et vérifiez jusqu'à ce que les données de test vous convient. Vous trouverez ces étapes dans la rubrique liée.
6. Basculer le serveur intermédiaire pour être le serveur actif. Il s’agit de la dernière étape pour **Basculer serveur actif** dans la rubrique [vérifier la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si vous mettez à niveau Azure AD Connect, mettez à niveau le serveur maintenant les intermédiaires mode vers la dernière version. Suivez les mêmes étapes qu’avant d’accéder aux données et configuration mis à niveau. Si vous mis à niveau à partir d’Azure AD Sync, vous pouvez maintenant désactiver et désaffecter votre ancien serveur.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Atteindre serveur intermédiaire de configuration personnalisée d’actif
Si vous avez apporté des modifications à la configuration au serveur actif, vous avez besoin pour vous assurer que les mêmes modifications sont appliquées sur le serveur intermédiaire.

Les règles de synchronisation personnalisé que vous avez créé peuvent être déplacés avec PowerShell. Autres modifications doivent être appliquées de la même façon sur les deux systèmes et ne peuvent pas être migrées.

Chose que vous devez vous assurer qu’est configuré de la même façon sur les deux serveurs :

- Connexion aux mêmes forêts.
- Un domaine quelconque et unité d’organisation filtrage.
- Fonctionnalités facultatives communes, telles que la synchronisation de mot de passe et mot de passe d’écriture différée.

**Déplacer les règles de synchronisation**  
Pour déplacer une règle personnalisé de la synchronisation, procédez comme suit :

1. Ouvrez **l’Éditeur de règles de synchronisation** sur votre serveur actif.
2. Sélectionnez votre règle personnalisée. Cliquez sur **Exporter**. Cela affiche une fenêtre du bloc-notes. Enregistrer le fichier temporaire avec une extension PS1. Il est donc un script PowerShell. Copiez le fichier ps1 sur le serveur intermédiaire.  
![Exportation des règles de synchronisation](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. GUID de connecteur est différent sur le serveur intermédiaire et doivent être modifié. Pour obtenir le GUID, démarrez **l’Éditeur de règles de synchronisation**, sélectionnez une des règles de prédéfinies qui représente le même système connecté, puis cliquez sur **Exporter**. Remplacez le GUID dans votre fichier PS1 par le GUID à partir du serveur intermédiaire.
4. Une invite PowerShell, exécutez le fichier PS1. Cela créera la règle de synchronisation personnalisé sur le serveur intermédiaire.
5. Si vous avez plusieurs règles personnalisées, répétez pour toutes les règles personnalisées.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

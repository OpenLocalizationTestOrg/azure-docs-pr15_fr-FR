<properties
    pageTitle="Azure AD Connect : L’activation d’écriture différée appareil | Microsoft Azure"
    description="Ce document explique en détail comment activer l’écriture différée appareil à l’aide d’Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect : L’activation d’écriture différée appareil

>[AZURE.NOTE] Un abonnement à Azure AD Premium est requis pour l’écriture différée appareil.

La documentation suivante fournit des informations sur la façon d’activer la fonctionnalité d’écriture différée appareil dans Azure AD Connect. Écriture différée appareil est utilisée dans les scénarios suivants :

- Activer l’accès conditionnelle basée sur les périphériques pour ADFS (2012 R2 ou une version ultérieure) applications (confiance fête approbations) protégées.

Cela fournit une sécurité supplémentaire et assurance aux applications l’accès uniquement aux périphériques approuvés. Pour plus d’informations sur l’accès conditionnelle, voir [Gestion des risques avec accès conditionnel](active-directory-conditional-access.md) et la [configuration d’accès au conditionnelle locales avec Azure Active Directory d’inscription pour appareils](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Appareils doivent se trouver dans la même forêt que les utilisateurs. Dans la mesure où les appareils doivent être signés revenir à une seule forêt, cette fonctionnalité ne prend pas actuellement en charge un déploiement avec plusieurs forêts d’utilisateurs.</li>
<li>Objet de configuration d’inscription qu’un seul périphérique peut être ajouté à la forêt Active Directory local. Cette fonctionnalité n’est pas compatible avec une topologie où Active Directory local est synchronisé avec plusieurs annuaires Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Partie 1 : Installer Azure AD se connecter
1. Installer Azure AD Connect à l’aide personnalisée ou Express paramètres. Microsoft recommande tout d’abord tous les utilisateurs et groupes correctement synchronisé avant d’activer le périphérique d’écriture différée.

## <a name="part-2-prepare-active-directory"></a>Partie 2 : Préparation d’Active Directory
Procédez comme suit pour préparer à l’aide d’écriture différée de l’appareil.

1.  À partir de l’ordinateur sur lequel Azure AD Connect est installé, lancez PowerShell en mode élevé.

2.  Si le module Active Directory PowerShell n’est pas installé, installez-la à l’aide de la commande suivante :

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Si le module Azure Active Directory PowerShell n’est pas installé, puis télécharger et installer à partir [d’Azure Active Directory Module pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Ce composant comporte une dépendance dans l’assistant de connexion, qui est installé avec Azure AD Connect.

4.  Avec les informations d’identification d’entreprise d’administration, exécutez les commandes suivantes, puis quittez PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Informations d’identification d’administrateur d’entreprise sont requises dans la mesure où les modifications apportées à l’espace de noms de configuration sont nécessaires. Un administrateur de domaine n’ont pas de suffisamment d’autorisations.

![PowerShell pour l’activation d’écriture différée appareil](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Description :

- S’ils n’existent pas déjà, créent et configure les nouveaux conteneurs et des objets sous CN = Configuration d’enregistrement de l’appareil, CN = Services, CN = Configuration, [forêt-nom de domaine].
- S’ils n’existent pas déjà, créent et configure les nouveaux conteneurs et des objets sous CN = RegisteredDevices, [nom de domaine-domaine]. APPAREIL objets seront créés dans ce conteneur.
- Définit les autorisations nécessaires sur le compte Azure AD connecteur, pour gérer les appareils mobiles sur votre Active Directory.
- Seulement doit s’exécuter sur une forêt, même si Azure AD Connect est installé sur plusieurs forêts.

Paramètres :

- Nom_domaine : Le domaine Active Directory où les objets du périphérique seront créés. Remarque : Tous les périphériques pour une forêt Active Directory donnée seront créés dans un domaine unique.
- AdConnectorAccount : Compte Active Directory qui servira à Azure AD Connect pour gérer des objets dans l’annuaire. Il s’agit le compte utilisé par synchronisation Azure AD Connect pour vous connecter à Active Directory. Si vous avez installé à l’aide de paramètres express, il est le compte le préfixe MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>3e partie : Activer appareil écriture différée dans Azure AD Connect
Utilisez la procédure suivante pour activer l’écriture différée appareil dans Azure AD Connect.

1.  Réexécutez l’Assistant installation. Sélectionnez **Personnaliser les options de synchronisation** à partir de la page tâches supplémentaires, puis cliquez sur **suivant**.
![Installation personnalisée personnaliser les options de synchronisation](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  Dans la page fonctionnalités facultatives, écriture différée appareil sera n’est plus grisée. Veuillez Remarque que si la Azure AD Connect étapes de préparation ne sont pas terminées appareil écriture différée est grisé arrière dans la page fonctionnalités facultatives. Cochez la case pour l’écriture différée appareil, puis cliquez sur **suivant**. Si la case à cocher est toujours désactivé, voir la [section Dépannage](#the-writeback-checkbox-is-still-disabled).
![Fonctionnalités facultatives d’écriture différée appareil installation personnalisée](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  Dans la page d’écriture différée, vous verrez le domaine fourni sous forme de la forêt d’écriture différée de périphérique par défaut.
![Personnalisé installer appareil d’écriture différée cible une forêt](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Terminer l’installation de l’Assistant avec aucun changement de configuration supplémentaires. Si nécessaire, reportez-vous aux [installation personnalisée de Azure AD Connect.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Activer l’accès conditionnel
Obtenir des instructions détaillées pour activer ce scénario sont disponibles dans [la configuration accès conditionnelle au locales avec Azure Active Directory d’inscription pour appareils](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Vérifier que les périphériques sont synchronisées avec Active Directory
Écriture différée appareil doit maintenant être fonctionne correctement. N’oubliez pas que cela peut prendre jusqu'à 3 heures pour les objets appareil soit précédent écrit à Active Directory.  Pour vérifier que vos périphériques sont correctement synchronisées, procédez comme suit après que les règles de synchronisation terminé :

1.  Lancer le centre d’administration Active Directory.
2.  Développez RegisteredDevices, au sein du domaine en cours fédérée.
![Centre d’administration Active Directory inscrit appareils](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Les appareils inscrits en cours seront répertoriées.
![Centre d’administration Active Directory enregistré la liste des périphériques](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="the-writeback-checkbox-is-still-disabled"></a>La case à cocher d’écriture différée est toujours désactivé
Si la case à cocher pour l’écriture différée appareil n’est pas activé même si vous avez suivi les étapes ci-dessus, les étapes suivantes vous guidera que l’Assistant installation consiste à vérifier avant que la case à cocher est activée.

Premières choses première :

- Vérifiez qu’au moins une forêt a 2012R2 Windows Server. Le type d’objet périphérique doit être présent.
- Si l’Assistant installation est en cours d’exécution, toute modification ne sera pas détectée. Dans ce cas, exécutez l’Assistant installation et exécutez à nouveau.
- Vérifiez que le compte que vous avez défini dans le script d’initialisation est en réalité l’utilisateur correct utilisé par le connecteur Active Directory. Pour ce faire, procédez comme suit :
    - Dans le menu Démarrer, ouvrez **Le Service de synchronisation**.
    - Ouvrez l’onglet **connecteurs** .
    - Trouver le connecteur à type de Services de domaine Active Directory et sélectionnez-le.
    - Sous **Actions**, sélectionnez **Propriétés**.
    - Accédez à **se connecter à la forêt Active Directory**. Vérifiez que le nom de domaine et d’utilisateur spécifié dans cette correspondance écran le compte fourni pour le script.
![Compte de connecteur dans le Gestionnaire de services de synchronisation](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Vérifier la configuration dans Active Directory :
- Vérifiez que le Service d’inscription appareil se trouve à l’emplacement ci-dessous (CN = DeviceRegistrationService, CN = Services d’enregistrement de l’appareil, CN = Configuration d’enregistrement de l’appareil, CN = Services, CN = Configuration) sous contexte d’appellation de configuration.

![Résoudre les problèmes, DeviceRegistrationService dans l’espace de noms de configuration](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Vérifiez qu’un seul objet de configuration est en recherchant dans l’espace de noms de configuration. S’il existe plusieurs, supprimer les doublons.

![Résoudre les problèmes, recherchez les objets en double](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- Sur l’objet de Service d’inscription appareils, vérifiez que l’attribut msDS-DeviceLocation est présente et comporte une valeur. Recherche cet emplacement et assurez-vous qu’il est présent avec le type d’objet msDS-DeviceContainer.

![Résoudre les problèmes, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Résoudre les problèmes, classe d’objet RegisteredDevices](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Vérifiez que le compte utilisé par le connecteur Active Directory dispose des autorisations nécessaires sur le conteneur de périphériques inscrits trouvé à l’étape précédente. Voici les autorisations attendues sur ce conteneur :

![Résoudre les problèmes, vérifiez les autorisations sur le conteneur](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Vérifiez le compte Active Directory dispose des autorisations sur CN = Configuration d’enregistrement de l’appareil, CN = Services, CN = objet de Configuration.

![Résoudre les problèmes, vérifiez les autorisations de Configuration d’appareil d’enregistrement](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informations supplémentaires
- [Gestion des risques avec accès conditionnel](active-directory-conditional-access.md)
- [La configuration d’accès au conditionnelle locales avec Azure Active Directory d’inscription pour appareils](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

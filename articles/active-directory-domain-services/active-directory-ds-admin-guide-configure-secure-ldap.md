<properties
    pageTitle="Configurer le serveur LDAP sécurisé (LDAPS) dans les Services de domaine Active Directory Azure | Microsoft Azure"
    description="Configurer LDAP sécurisé (LDAPS) pour un domaine géré Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurer LDAP sécurisé (LDAPS) pour un domaine géré Azure Active Directory Domain Services
Cet article vous explique comment vous pouvez activer le protocole Lightweight Directory Access sécurisé (LDAPS) pour votre domaine géré Azure Active Directory Domain Services. LDAP sécurisé est également connue sous le nom « accès protocole LDAP (Lightweight Directory) sur couche SSL (Secure Sockets) / sécurité TLS (Transport Layer) ».

## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches mentionnées dans cet article, vous devez :

1. Un **abonnement Azure**valide.

2. Un **répertoire Azure AD** - soit synchronisé avec un répertoire local ou un répertoire exclusivement le nuage.

3. **Services de domaine Active Directory Azure** doit être activé pour le répertoire Azure AD. Si vous n’avez pas fait, suivez toutes les tâches décrites dans le [guide de mise en route](./active-directory-ds-getting-started.md).

4. Un **certificat devant être utilisé pour activer le protocole LDAP sécurisé**.
    - **Recommandé** - obtenir un certificat de votre autorité de certification ou d’une autorité de certification publique. Cette option de configuration est plus sécurisée.
    - Alternativement, vous pouvez également choisir pour [créer un certificat auto-signé](#task-1---obtain-a-certificate-for-secure-ldap) comme illustré plus loin dans cet article.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Configuration requise pour le certificat LDAP sécurisé
Obtenir un certificat valide par les instructions suivantes avant d’activer le protocole LDAP sécurisé. Vous rencontrez des échecs si vous essayez d’activer le protocole LDAP sécurisé pour votre domaine géré avec un certificat incorrect / n’est pas valide.

1. **L’émetteur de confiance** - le certificat doit être émis par une autorité approuvée par les ordinateurs qui doivent se connecter au domaine à l’aide du protocole LDAP sécurisé. Cette autorité peut être autorité de certification d’entreprise de votre organisation ou d’une autorité de certification approuvée par ces ordinateurs.

2. **Durée de vie** - le certificat doit être valide pour au moins les prochains mois 3 à 6. Un accès sécurisé LDAP à votre domaine géré est interrompu lorsque l’expiration du certificat.

3. **Nom de sujet** - le nom de sujet sur le certificat doit être un caractère générique pour votre domaine géré. Par exemple, si votre domaine est nommé « contoso100.com », nom de sujet du certificat doit être ' *. contoso100.com ». Définissez le nom DNS (autre nom de sujet) sur ce nom génériques.

3. **Utilisation de la clé** - le certificat doit être configuré pour l’exemple suivant utilise - signatures numériques et le chiffrement de la clé.

4. **Utilisation du certificat** - le certificat doit être valide pour l’authentification de serveur SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tâche 1 - obtenir un certificat pour sécuriser LDAP
La première tâche, vous devez obtenir un certificat utilisé pour un accès sécurisé LDAP pour le domaine géré. Vous avez deux possibilités :

- Obtenir un certificat d’une autorité de certification. L’autorité peut être autorité de certification d’entreprise de votre organisation ou d’une autorité de certification.

- Créer un certificat auto-signé.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Option une (recommandé) - obtenir un certificat LDAP sécurisé à partir d’une autorité de certification
Si votre organisation déploie une infrastructure à clé publique enterprise (infrastructure de clé publique), vous devez obtenir un certificat entreprise l’autorité de certification (CA) pour votre organisation. Si votre organisation obtient ses certificats d’une autorité de certification, vous devez obtenir le certificat LDAP sécurisé à partir de cette autorité de certification publique.

Lorsque vous demandez un certificat, vérifiez que vous appliquez critères décrits dans la [configuration minimale requise pour le certificat LDAP sécurisé](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] Les ordinateurs client que vous devez vous connecter pour le domaine géré à l’aide de sécuriser LDAP doivent approuver l’émetteur du certificat LDAPS.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Option B - créer un certificat auto-signé pour sécuriser LDAP
Vous pouvez choisir de créer un certificat auto-signé pour sécuriser LDAP, si :

- certificats de votre organisation ne sont pas émis par une autorité de certification d’entreprise ou
- Vous ne prévoyez pas à utiliser un certificat émis par une autorité de certification.

**Créer un certificat auto-signé à l’aide de PowerShell**

Sur votre ordinateur Windows, ouvrez une nouvelle fenêtre PowerShell en tant **qu’administrateur** , puis tapez les commandes suivantes, pour créer un nouveau certificat auto-signé.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

Dans l’exemple précédent, remplacez « contoso100.com » avec le nom de domaine DNS de votre domaine géré Azure Active Directory Domain Services.

![Sélectionnez Azure AD répertoire](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Le certificat auto-signé nouvellement créé est placé dans le magasin de certificats de l’ordinateur local.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tâche 2 : exporter le protocole LDAP sécurisé de certificats pour un. Fichier PFX
Avant de commencer cette tâche, vérifiez que vous avez obtenu le certificat LDAP sécurisé à partir de votre autorité de certification d’entreprise ou d’une autorité de certification ou que vous avez créé un certificat auto-signé.

Procédez comme suit pour exporter le certificat LDAPS à un. Fichier PFX.

1. Appuyez sur le bouton **Démarrer** et tapez **R**. Dans la boîte de dialogue **exécuter** , tapez **mmc** et cliquez sur **OK**.

    ![Lancer la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. À l’invite de **Contrôle de compte d’utilisateur** , cliquez sur **Oui** pour lancer la console MMC (Microsoft Management Console) en tant qu’administrateur.

3. Dans le menu **fichier** , cliquez sur **composant logiciel enfichable ajouter/supprimer-pouces..**.

    ![Ajoutez enfichable à la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. Dans la boîte de dialogue **Ajouter ou supprimer des composants** , sélectionnez le composant logiciel enfichable **certificats** , puis cliquez sur le **Ajouter >** bouton.

    ![Ajouter le composant logiciel enfichable Certificats à la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. Dans l’Assistant **composant logiciel enfichable Certificats** , sélectionnez le **compte d’ordinateur** et cliquez sur **suivant**.

    ![Ajouter le composant logiciel enfichable Certificats pour un compte d’ordinateur](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. Dans la page **Sélectionner un ordinateur** , sélectionnez **ordinateur Local : (l’ordinateur cette console s’exécute)** et cliquez sur **Terminer**.

    ![Ajouter enfichable Certificats - sélectionner un ordinateur](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. Dans la boîte de dialogue **Ajouter ou supprimer des composants** , cliquez sur **OK** pour ajouter le composant logiciel enfichable Certificats à la console MMC.

    ![Ajouter le composant logiciel enfichable Certificats à la console MMC - terminé](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. Dans la fenêtre de la console MMC, cliquez pour développer la **Racine de la Console**. Vous devriez voir le composant logiciel enfichable Certificats chargé. Cliquez sur **certificats (ordinateur Local)** pour le développer. Cliquez pour développer le nœud **personnel** , suivi par le nœud **certificats** .

    ![Magasin de certificats personnels ouvert](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Vous devriez voir le certificat auto-signé que nous avons créé. Vous pouvez examiner les propriétés du certificat pour garantir l’empreinte numérique correspond à celui signalée sur le windows PowerShell lorsque vous avez créé le certificat.

10. Sélectionnez le certificat auto-signé et **cliquez avec le bouton droit sur**. Dans le menu contextuel, sélectionnez **Toutes les tâches** , puis sélectionnez **Exporter**.

    ![Certificat d’exportation](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. Dans l' **Assistant Exportation de certificat**, cliquez sur **suivant**.

    ![Assistant Exportation de certificat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. Dans la page **Exportation de clé privée** , sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **suivant**.

    ![Exporter clé privée.](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] Vous devez exporter la clé privée ainsi que le certificat. Si vous indiquez un PFX qui ne contient-elle pas la clé privée pour le certificat, l’activation de sécuriser LDAP pour votre domaine géré échoue.

13. Dans la page **Format de fichier d’exportation** , sélectionnez **échange d’informations personnelles - PKCS #12 (. PFX)** en tant que format de fichier pour le certificat exporté.

    ![Format de fichier de certificat d’exportation ?](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Uniquement la. Format de fichier PFX est pris en charge. Ne pas exporter le certificat à la. Format de fichier limitée.

14. Dans la page de **sécurité** , sélectionnez l’option de **mot de passe** et tapez un mot de passe pour protéger la. Fichier PFX. N’oubliez pas ce mot de passe dans la mesure où il faudra dans la tâche suivante. Cliquez sur **suivant** pour continuer.

    ![Mot de passe pour l’exportation du certificat ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Prenez note de ce mot de passe. Vous devez tout en permettant de sécuriser LDAP pour ce domaine géré dans la [tâche 3 - activer le protocole LDAP sécurisé pour le domaine géré](#task-3---enable-secure-ldap-for-the-managed-domain)

15. Dans la page **fichier à exporter** , spécifiez le nom de fichier et l’emplacement où vous voulez exporter le certificat.

    ![Chemin d’accès pour l’exportation du certificat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. Dans la page suivante, cliquez sur **Terminer** pour exporter le certificat vers un fichier PFX. Vous devriez voir boîte de dialogue de confirmation lorsque ce certificat a été exporté.

    ![Certificat d’exportation terminé](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tâche 3 : activer LDAP sécurisé pour le domaine géré
Pour activer le protocole LDAP sécurisé, effectuez les étapes de configuration suivantes :

1. Accédez au **[portail classique Azure](https://manage.windowsazure.com)**.

2. Sélectionnez le nœud **Active Directory** dans le volet gauche.

3. Sélectionnez le répertoire Azure AD (également appelé « client »), dont vous avez activé Azure Active Directory Domain Services.

    ![Sélectionnez Azure AD répertoire](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **configurer** .

    ![Configurer l’onglet du répertoire](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Faites défiler jusqu'à la section intitulée **les services de domaine**. Vous devriez voir une option intitulée **LDAP sécurisé (LDAPS)** comme indiqué dans la capture d’écran suivante :

    ![Section de configuration de Services de domaine](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Cliquez sur le bouton **certificat configurer...** pour faire apparaître la boîte de dialogue **Configurer un certificat pour sécuriser LDAP** .

    ![Configurer un certificat pour sécuriser LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Cliquez sur l’icône de dossier sous **Fichier PFX avec certificat** pour spécifier le fichier PFX qui contient le certificat que vous souhaitez utiliser pour un accès sécurisé LDAP pour le domaine géré. Également entrer le mot de passe que vous avez spécifié lors de l’exportation du certificat vers le fichier PFX. Ensuite, cliquez sur le bouton terminé en bas.

    ![Spécifier sécurisé LDAP PFX fichier et un mot de passe](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. La section **services de domaine** de l’onglet **configurer** obtenir grisée et se trouve dans le **en attente...** état pendant quelques minutes. Pendant cette période, le certificat LDAPS est vérifié pour précision et sécuriser LDAP est configuré pour votre domaine géré.

    ![Sécuriser LDAP - état d’attente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Activer le protocole LDAP sécurisé pour votre domaine géré prend environ 10 à 15 minutes. Si le certificat LDAP sécurisé fourni ne correspond pas les critères requis, LDAP sécurisé n’est pas activée pour votre annuaire et une erreur apparaît. Par exemple, le nom de domaine est incorrect, le certificat est expiré ou expire bientôt etc..

9. Lorsque LDAP sécurisé est correctement activé pour votre domaine géré, le **en attente...** message doit disparaître. Vous devriez voir l’empreinte numérique du certificat affiché.

    ![Sécuriser LDAP activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tâche 4 - activer l’accès LDAP sécurisé via internet
**Tâche facultative** - si vous ne prévoyez pas d’accéder au domaine géré à l’aide de LDAPS sur internet, ignorez cette tâche de configuration.

Avant de commencer cette tâche, assurez-vous que vous avez terminé les étapes décrites dans la [tâche 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Vous devriez voir une option pour **Activer la banque d’informations sécurisé LDAP ACCESS sur INTERNET** dans la section **services de domaine** de la page **configurer** . Cette option est définie sur **non** par défaut dans la mesure où l’accès internet pour le domaine géré sur LDAP sécurisé est désactivé par défaut.

    ![Sécuriser LDAP activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Activer/désactiver **Activer l’accès LDAP sécurisé par le biais d’INTERNET** sur **Oui**. Cliquez sur le bouton **Enregistrer** dans le volet inférieur.
    ![Sécuriser LDAP activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. La section **services de domaine** de l’onglet **configurer** obtenir grisée et se trouve dans le **en attente...** état pendant quelques minutes. Après un certain temps, accès à internet pour votre domaine géré sur LDAP sécurisé est activée.

    ![Sécuriser LDAP - état d’attente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Il prend environ 10 minutes pour activer l’accès internet sur LDAP sécurisé pour votre domaine géré.

4. Lorsqu’un accès sécurisé LDAP à votre domaine géré par le biais d’internet est activé, la **en attente...** message doit disparaître. Vous devez voir l’adresse IP externe pouvant être utilisées pour accéder à votre répertoire par LDAPS dans le champ **Adresse IP externe pour LDAPS ACCESS**.

    ![Sécuriser LDAP activé](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tâche 5 - configurer le système DNS pour accéder au domaine géré à partir d’internet
**Tâche facultative** - si vous ne prévoyez pas d’accéder au domaine géré à l’aide de LDAPS via internet, ignorez cette tâche de configuration.

Avant de commencer cette tâche, assurez-vous que vous avez terminé les étapes décrites dans la [tâche 4](#task-4---enable-secure-ldap-access-over-the-internet).

Une fois que vous avez activé un accès sécurisé LDAP via internet pour votre domaine géré, vous devez mettre à jour DNS afin que les ordinateurs client puissent le trouver ce domaine géré. À la fin de la tâche 4, une adresse IP externe s’affiche sous l’onglet **configurer** dans **Adresse IP externe pour LDAPS ACCESS**.

Configurer votre fournisseur DNS externe afin que le nom DNS du domaine géré (par exemple, « contoso100.com ») pointe vers cette adresse IP externe. Dans notre exemple, nous avons besoin de créer l’entrée DNS suivante :

    contoso100.com  -> 52.165.38.113

C’est tout - vous êtes maintenant prêt à vous connecter pour le domaine géré à l’aide de LDAP sécurisé via internet.

> [AZURE.WARNING] N’oubliez pas que les ordinateurs client doivent approuver l’émetteur du certificat LDAPS pour pouvoir se connecter pour le domaine géré à l’aide de LDAPS. Si vous utilisez une autorité de certification d’entreprise ou d’une autorité de certification de confiance publique, vous n’avez pas besoin rien à faire dans la mesure où les ordinateurs client approuvent ces personnes certificat. Si vous utilisez un certificat auto-signé, vous devez installer le composant public du certificat auto-signé dans le magasin de certificats approuvé sur l’ordinateur client.

<br>

## <a name="related-content"></a>Contenu associé

- [Administrer un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)

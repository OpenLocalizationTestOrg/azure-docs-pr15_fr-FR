<properties
    pageTitle="Utiliser l’authentification Multifacteur Azure Server avec AD FS 2.0 | Microsoft Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui explique comment commencer à utiliser l’authentification Multifacteur Azure et AD FS 2.0."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Sécuriser les ressources de cloud et en local à l’aide de serveur de l’authentification multifacteur Azure avec AD FS 2.0

Cet article est pour les organisations fédérées avec Azure Active Directory et souhaitez sécuriser les ressources qui se trouvent en local ou dans le cloud. Protéger vos ressources à l’aide du serveur de l’authentification multifacteur Azure et configurez-le pour fonctionner avec AD FS afin que la vérification est déclenchée pour les points de terminaison de valeur élevée.

Cette documentation décrit l’utilisation du serveur de l’authentification multifacteur Azure avec AD FS 2.0.  Obtenir plus d’informations sur les [ressources sécurisation cloud et en local à l’aide de serveur de l’authentification multifacteur Azure avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Sécuriser AD FS 2.0 avec un serveur proxy
Pour sécuriser AD FS 2.0 avec un proxy, installez le serveur de l’authentification multifacteur Azure sur le serveur proxy AD FS et configurer le serveur.

### <a name="configure-iis-authentication"></a>Configurer l’authentification IIS

1. Au sein du serveur de l’authentification multifacteur Azure, cliquez sur l’icône de **L’authentification IIS** dans le menu de gauche.
2. Cliquez sur l’onglet **Basé sur formulaire** .
3. Cliquez sur l' **Ajouter...** bouton.
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Pour détecter automatiquement les variables de domaine, nom d’utilisateur et mot de passe, entrez l’URL de connexion (par exemple, https://sso.contoso.com/adfs/ls) dans la boîte de dialogue Auto-Configure Form-Based site Web, puis cliquez sur OK.
5. Cochez la case **nécessite l’authentification multifacteur Azure utilisateur correspond à** si tous les utilisateurs ont été ou importées sur le serveur et soumis à la vérification. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de la vérification, laissez la case désactivée. Pour plus d’informations sur cette fonctionnalité, voir le fichier d’aide.
6. Si les variables de page ne peut pas être détectés automatiquement, cliquez sur la **Spécifier manuellement...** bouton dans la boîte de dialogue Auto-Configure Form-Based site Web.
7. Dans la boîte de dialogue Add Form-Based site Web, entrez l’URL de la page de connexion ADFS dans le champ URL soumettre (par exemple, https://sso.contoso.com/adfs/ls) et entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports de l’authentification multifacteur Azure et peut être affiché dans les messages d’authentification SMS ou une application Mobile. Voir le fichier d’aide pour plus d’informations sur l’URL soumettre.
8. Définir le format de demande « Publier ou obtenir ».
9. Entrez le nom d’utilisateur (ctl00 ContentPlaceHolder1$ $UsernameTextBox) et la variable mot de passe (ctl00 ContentPlaceHolder1$ $PasswordTextBox). Si votre page de connexion basé sur un formulaire affiche une zone de texte domaine, entrez ainsi que la variable de domaine. Vous devrez peut-être accéder à la page de connexion dans un navigateur web, avec le bouton droit sur la page et sélectionnez **Afficher la Source** pour rechercher les noms des zones de saisie dans la page de connexion.
10. Cochez la case **nécessite l’authentification multifacteur Azure utilisateur correspond à** si tous les utilisateurs ont été ou importées sur le serveur et soumis à la vérification. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de la vérification, laissez la case désactivée.
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Cliquez sur **avancés...** bouton passer en revue les paramètres avancés. Vous pouvez configurer les paramètres, y compris la possibilité de sélectionner un fichier de page de refus personnalisé, pour mettre en cache des authentifications réussies sur le site Web à l’aide de cookies, pour sélectionner la méthode d’authentification les informations d’identification primaires.
12. Étant donné que le serveur proxy AD FS n’est pas susceptible d’être joint au domaine, vous pouvez utiliser LDAP pour vous connecter à votre contrôleur de domaine pour l’importation de l’utilisateur et l’authentification préalable. Dans la boîte de dialogue Advanced Form-Based site Web, cliquez sur l’onglet **D’authentification principal** et sélectionnez **Liaison LDAP** pour le type d’authentification de l’authentification préalable.
13. Lorsque vous avez terminé, cliquez sur le bouton **OK** pour revenir à la boîte de dialogue Add Form-Based site Web. Voir le fichier d’aide pour plus d’informations sur les paramètres avancés.
14. Cliquez sur le bouton **OK** pour fermer la boîte de dialogue.
15. Une fois que les variables d’URL et page ont été détectés ou entrés, les données de site Web s’affiche dans le panneau basé sur formulaire.
16. Cliquez sur l’onglet **Module natif** et sélectionnez le serveur, le site Web du proxy AD FS exécutant sous (comme « Site Web par défaut ») ou l’application de proxy AD FS (comme « ls » sous « adfs ») pour activer le plug-in IIS au niveau souhaité.
17. Cliquez sur la zone de **l’authentification IIS activer** dans la partie supérieure de l’écran.
18. L’authentification IIS est activée.

### <a name="configure-directory-integration"></a>Configurer l’intégration d’annuaire

Vous avez activé l’authentification IIS, mais pour effectuer l’authentification préalable à votre Active Directory (AD) via LDAP, vous devez configurer la connexion LDAP au contrôleur de domaine.

1. Cliquez sur l’icône **d’Intégration d’annuaire** .
2. Sous l’onglet Paramètres, sélectionnez la case **utiliser la configuration LDAP spécifique** .
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Cliquez sur la **Modifier...** bouton.
4. Dans la boîte de dialogue Modifier la Configuration LDAP, remplissez les champs avec les informations requises pour vous connecter au contrôleur de domaine Active Directory. Descriptions des champs sont incluses dans le tableau ci-dessous. Ces informations sont également incluses dans le fichier d’aide serveur de l’authentification multifacteur Azure.
5. Tester la connexion LDAP en cliquant sur le bouton **Test** .
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Si le test de connexion LDAP a réussi, cliquez sur le bouton **OK** .

### <a name="configure-company-settings"></a>Configurer les paramètres de la société

1. Ensuite, cliquez sur l’icône **Paramètres de la société** et sélectionnez l’onglet **Résolution du nom d’utilisateur** .
2. Sélectionnez le bouton d’option **utiliser LDAP des attributs d’identificateur unique pour les noms d’utilisateur correspondants** .
3. Si les utilisateurs entrent leur nom d’utilisateur au format « DOMAINE\nom_utilisateur », le serveur doit être en mesure de supprimer le domaine désactiver le nom d’utilisateur lorsqu’il crée la requête LDAP. Qui peut être effectuée via un paramètre du Registre.
4. Ouvrez l’Éditeur du Registre et accédez à HKEY_LOCAL_MACHINE/logiciel/Wow6432Node/positif réseaux/PhoneFactor sur un serveur 64 bits. Dans le cas d’un serveur 32 bits, prendre la Wow6432Node » « déconnecter le chemin d’accès. Créer une clé de Registre DWORD appelée « UsernameCxz_stripPrefixDomain » et définissez la valeur 1. L’authentification multifacteur Azure la sécurisation est maintenant le proxy AD FS.

Assurez-vous que les utilisateurs ont été importés à partir d’Active Directory sur le serveur. Consultez la [section adresses IP approuvés](#trusted-ips) si vous souhaitez que les adresses IP internes d’autorisation afin que la vérification n’est pas nécessaire lors de la connexion au site Web à partir de ces emplacements.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 directe sans proxy

Vous pouvez sécuriser AD FS lorsque le proxy AD FS n’est pas utilisé. Installer le serveur de l’authentification multifacteur Azure sur le serveur AD FS et configurer le serveur par les étapes suivantes :

1. Au sein du serveur de l’authentification multifacteur Azure, cliquez sur l’icône de **L’authentification IIS** dans le menu de gauche.
2. Cliquez sur l’onglet **HTTP** .
3. Cliquez sur l' **Ajouter...** bouton.
4. Dans la boîte de dialogue Ajouter une URL de Base, entrez l’URL pour le site Web ADFS où l’authentification HTTP est exécutée (par exemple, https://sso.domain.com/adfs/ls/auth/integrated) dans le champ URL de Base. Ensuite, entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports de l’authentification multifacteur Azure et peut être affiché dans les messages d’authentification SMS ou une application Mobile.
5. Si vous le souhaitez, ajustez le délai d’inactivité et Maximum des durées de session.
6. Cochez la case **nécessite l’authentification multifacteur Azure utilisateur correspond à** si tous les utilisateurs ont été ou importées sur le serveur et soumis à la vérification. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de la vérification, laissez la case désactivée. Pour plus d’informations sur cette fonctionnalité, voir le fichier d’aide.
7. Cochez la case de cache des cookies si vous le souhaitez.
<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Cliquez sur le bouton **OK** .
9. Cliquez sur l’onglet **Module natif** et sélectionnez le serveur, le site Web (telle que « Site Web par défaut ») ou l’application ADFS (par exemple, « ls » sous « adfs ») pour activer le plug-in IIS au niveau souhaité.
10. Cliquez sur la zone de **l’authentification IIS activer** dans la partie supérieure de l’écran. L’authentification multifacteur Azure la sécurisation est maintenant ADFS.

Assurez-vous que les utilisateurs ont été importés à partir d’Active Directory sur le serveur. Consultez la section adresses IP approuvés si vous souhaitez que les adresses IP internes d’autorisation afin que la vérification n’est pas nécessaire lors de la connexion au site Web à partir de ces emplacements.


## <a name="trusted-ips"></a>Adresses IP autorisées
Adresses IP autorisées permettent aux utilisateurs d’ignorer l’authentification multifacteur Azure pour les demandes de site Web provenant d’adresses IP ou sous-réseaux spécifique. Par exemple, vous voudrez peut-être agents utilisateurs à partir de la vérification lorsqu’ils se connecter à partir du bureau. Pour ce faire, vous spécifiez le sous-réseau office comme une entrée d’adresses IP approuvés.

### <a name="to-configure-trusted-ips"></a>Pour configurer les adresses IP autorisées


1. Dans la section authentification IIS, cliquez sur l’onglet **Adresses IP approuvés** .
1. Cliquez sur l' **Ajouter...** bouton.
1. Lorsque la boîte de dialogue Ajouter des adresses IP de confiance s’affiche, sélectionnez une des cases **IP unique**, **plage d’adresses IP**ou **sous-réseau** .
1. Entrez l’adresse IP, plage d’adresses IP ou sous-réseau qui doit être autorisés. Si vous entrez un sous-réseau, sélectionnez le masque de réseau appropriée et cliquez sur le bouton **OK** . L’adresse IP approuvé maintenant a été ajouté.


<center>![Programme d’installation](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<properties 
    pageTitle="Serveur de l’authentification multifacteur l’authentification IIS et Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui facilitent le déploiement d’authentification IIS et serveur de l’authentification multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>Authentification IIS

La section authentification IIS du serveur de l’authentification multifacteur Azure permet d’activer et configurer l’authentification IIS pour l’intégration avec les applications web IIS Microsoft. Le serveur de l’authentification multifacteur Azure installe un plug-in qui peuvent filtrer les requêtes effectuées pour le serveur web IIS afin d’ajouter l’authentification multifacteur Azure. Le plug-in IIS prend en charge l’authentification basée sur le formulaire et l’authentification Windows HTTP intégrée. Adresses IP peut également être configuré pour les adresses IP internes agents d’authentification à deux facteurs de confiance.


![Authentification IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>À l’aide de l’authentification basée sur le formulaire IIS avec l’authentification multifacteur Azure serveur

Pour sécuriser une application web IIS qui utilise l’authentification basée sur le formulaire, installez le serveur de l’authentification multifacteur Azure sur le serveur web IIS et configurer le serveur par la procédure suivante.

1. Au sein du serveur de l’authentification multifacteur Azure cliquez sur l’icône de l’authentification IIS dans le menu de gauche.
2. Cliquez sur l’onglet basé sur formulaire.
3. Cliquez sur Ajouter... bouton.
4. Pour détecter les nom d’utilisateur, les variables de mot de passe et le domaine automatiquement, entrez l’URL de connexion (par exemple, https://localhost/contoso/auth/login.aspx) dans la boîte de dialogue Auto-Configure Form-Based site Web et cliquez sur OK.
5. Cochez la case de correspondance d’utilisateur nécessite l’authentification multifacteur si tous les utilisateurs ont été ou importées sur le serveur et soumis à l’authentification multifacteur. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de l’authentification multifacteur, laissez la case désactivée.
6. Si les variables de page ne peut pas être détectés automatiquement, cliquez sur la spécifier manuellement... bouton dans la boîte de dialogue Auto-Configure Form-Based site Web.
7. Dans la boîte de dialogue Add Form-Based site Web, entrez l’URL de la page de connexion dans le champ URL soumettre et entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports de l’authentification multifacteur Azure et peut être affiché dans les messages d’authentification SMS ou une application Mobile. Voir le fichier d’aide pour plus d’informations sur l’URL soumettre.
8. Sélectionnez le format correct de la demande. Ceci est défini sur « Billet ou obtenir » pour la plupart des applications web.
9. Entrez la variable de nom d’utilisateur, la variable de mot de passe et la variable de domaine (si celle-ci apparaît sur la page de connexion). Vous devrez peut-être accéder à la page de connexion dans un navigateur web, avec le bouton droit sur la page et sélectionner « Afficher la Source » pour rechercher les noms des zones d’entrée dans la page.
10. Cochez la case de correspondance d’utilisateur nécessite l’authentification multifacteur Azure si tous les utilisateurs ont été ou importées sur le serveur et soumis à l’authentification multifacteur. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de l’authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.
11.  Cliquez sur avancées... bouton passer en revue les paramètres avancés, y compris la possibilité de sélectionner un fichier de page personnalisé refus, en cache authentifications réussies sur le site Web pour une période de temps à l’aide de cookies et indiquez si vous souhaitez authentifier les informations d’identification primaires contre le domaine Windows, un annuaire LDAP ou un serveur RADIUS. Lorsque vous avez terminé, cliquez sur le bouton OK pour revenir à la boîte de dialogue Add Form-Based site Web. Voir le fichier d’aide pour plus d’informations sur les paramètres avancés.
12. Cliquez sur le bouton OK.
13. Une fois que les variables d’URL et page ont été détectés ou entrés, les données de site Web seront affichent dans le panneau de configuration basé sur le formulaire.
14. Voir les activer IIS Plug-ins pour la section serveur de l’authentification multifacteur Azure juste en dessous pour terminer la configuration de l’authentification IIS.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>À l’aide de l’authentification Windows intégrée avec l’authentification multifacteur Azure serveur

Pour sécuriser une application web IIS qui utilise l’authentification intégrée Windows HTTP, installez le serveur de l’authentification multifacteur Azure sur le serveur web IIS et configurer le serveur par la procédure suivante.

1. Au sein du serveur de l’authentification multifacteur Azure cliquez sur l’icône de l’authentification IIS dans le menu de gauche.
2. Cliquez sur l’onglet HTTP. Cliquez sur l’onglet basé sur formulaire.
3. Cliquez sur Ajouter... bouton.
4. Dans la boîte de dialogue Ajouter une URL de Base, entrez l’URL pour le site Web où l’authentification HTTP est effectué (par exemple, http://localhost/owa) dans le champ URL de Base et entrez un nom de l’Application (facultatif). Le nom de l’Application apparaît dans les rapports de l’authentification multifacteur Azure et peut être affiché dans les messages d’authentification SMS ou une application Mobile.
5. Ajuster le délai d’inactivité et Maximum des durées de session si la valeur par défaut n’est pas suffisant.
6. Cochez la case de correspondance d’utilisateur nécessite l’authentification multifacteur si tous les utilisateurs ont été ou importées sur le serveur et soumis à l’authentification multifacteur. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de l’authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.
7. Cochez la case de cache des cookies si vous le souhaitez.
8. Cliquez sur le bouton OK.
9. Consultez la section [Activer IIS Plug-ins pour serveur de l’authentification multifacteur Azure](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) juste en dessous pour terminer la configuration de l’authentification IIS.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Activer le Plug-ins IIS pour l’authentification multifacteur Azure serveur

Une fois que vous avez configuré basée sur le formulaire ou l’URL de l’authentification HTTP et paramètres, vous devez sélectionner les emplacements où les plug-ins IIS de l’authentification multifacteur Azure doit être chargés et activés dans IIS. Procédez comme suit :

1. Si en cours d’exécution sur IIS 6, cliquez sur l’onglet ISAPI et sélectionnez le site Web de l’application web exécutant sous (par exemple, Site Web par défaut) pour activer le filtre ISAPI de l’authentification multifacteur Azure plug-in pour ce site.
2. Si en cours d’exécution sur IIS 7 ou version ultérieure, cliquez sur l’onglet Module natif et sélectionnez le serveur, les sites Web ou les applications pour activer le plug-in IIS au niveau de votre choix.
3. Cliquez sur la zone d’authentification IIS activer dans la partie supérieure de l’écran. L’authentification multifacteur Azure est maintenant sécurisation de l’application IIS sélectionnée. Assurez-vous que les utilisateurs ont été importés dans le serveur. Consultez la section adresses IP approuvés ci-dessous si vous voulez d’autorisation que les adresses IP internes afin que l’authentification à deux facteurs n’est pas nécessaire lors de la connexion au site Web à partir de ces emplacements.


## <a name="trusted-ips"></a>Adresses IP autorisées

Les adresses IP approuvés permet aux utilisateurs d’ignorer l’authentification multifacteur Azure pour les demandes de site Web provenant d’adresses IP spécifiques ou sous-réseaux. Par exemple, vous voudrez peut-être agents utilisateurs à partir de l’authentification multifacteur Azure pendant la connexion à partir du bureau. Pour ce faire, vous devez spécifier le sous-réseau office comme entrée d’adresses IP approuvés. Pour configurer les adresses IP approuvés procédez comme suit :

1. Dans la section authentification IIS, cliquez sur l’onglet adresses IP approuvés.
2. Cliquez sur Ajouter... bouton.
3. Lorsque la boîte de dialogue Ajouter des adresses IP de confiance s’affiche, sélectionnez l’adresse IP unique, la plage d’adresses IP ou la case d’option sous-réseau.
4. Entrez l’adresse IP, plage d’adresses IP ou sous-réseau qui doit être autorisés. Si vous entrez un sous-réseau, sélectionnez le masque de réseau appropriée et cliquez sur le bouton OK. La liste d’autorisation maintenant a été ajouté.

<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Evidence.com | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Evidence.com."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Didacticiel : Intégration d’Azure Active Directory avec Evidence.com

L’objectif de ce didacticiel consiste à afficher la configuration de l’authentification unique entre Azure Active Directory (DAS) et Evidence.com. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :
    
* Un abonnement à Microsoft Azure valide
* Un abonnement Evidence.com avec authentification unique activée (messagerie earlyaccess@evidence.com si basée sur SAML authentification unique n’est pas activée)

Au terme de ce didacticiel, les utilisateurs AAD auquel vous avez attribué un accès Evidence.com sera en mesure d’authentification unique dans l’application en utilisant le panneau d’accès AAD.

## <a name="add-evidencecom-to-your-directory"></a>Ajoutez Evidence.com à votre répertoire

Cette section explique comment ajouter Evidence.com comme une application intégrée dans Azure Active Directory.

**Pour activer l’intégration des applications pour preuve :**

1.  Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

4.  Pour ouvrir la galerie d’applications, cliquez sur **Ajouter**, puis cliquez sur **Ajouter une application à partir de la galerie**.

5.  Dans la zone Rechercher, tapez **Evidence.com**.

6.  Dans le volet résultats, sélectionnez **Evidence.com**, puis cliquez sur **Terminer** pour ajouter l’application.


## <a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier à Evidence.com avec leur propre compte dans Azure Active Directory, à l’aide de la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1.  Après avoir ajouté Evidence.com dans le portail Azure classique, cliquez sur **Configuration de l’authentification unique**. 
 
2.  Dans l’écran suivant, sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

3.  Dans l’écran configurer l’application URL, entrez l’URL où les utilisateurs seront vous connecter à l’URL de votre client Evidence.com (exemple : https://yourtenant.evidence.com, puis cliquez sur **suivant**. 

4.  Cliquez sur le lien **Télécharger le certificat** et enregistrez-le sur votre disque local. Ce certificat et l’URL de métadonnées (ID de l’entité, l’authentification unique URL de connexion et se déconnecter URL) servira à configurer l’authentification unique sur site Evidence.com. 

5.  Dans une fenêtre de navigateur web distinctes, connectez-vous à votre Evidence.com du client en tant qu’administrateur et accédez à l’onglet **Admin**
      
6.  Cliquez sur **authentification unique agence**
 
7.  Sélectionnez **SAML selon l’authentification unique**
 
8.  Copiez l' **URL de l’émetteur**, **Authentification** et **Déconnexion unique** valeurs indiquées dans le portail classique Azure et dans les champs correspondants dans Evidence.com.

9.  Ouvrir le certificat téléchargé à l’étape 4 à l’aide d’un éditeur de texte tel que Notepad.exe et copier et coller le contenu dans la zone **Certificat de sécurité** . 

10. Enregistrer la configuration dans Evidence.com.
 
11. Dans le portail classique Azure, cochez **confirmer que vous avez configuré de l’authentification unique comme décrit ci-dessus**. Activez cette option permettra le certificat actuel commencer à utiliser pour cette case à cocher de l’application.
 
12. Dans la page de confirmation d’authentification unique, cliquez sur **Terminer**.  


## <a name="creating-an-evidencecom-test-user"></a>Création d’un utilisateur de test Evidence.com

Pour les utilisateurs Azure AD pouvoir se connecter, ils doivent être configurées pour l’accès à l’intérieur de l’application Evidence.com. Cette section décrit comment créer des comptes d’utilisateur Azure AD à l’intérieur Evidence.com

**Pour configurer un compte d’utilisateur dans Evidence.com :**

1.  Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise Evidence.com en tant qu’administrateur.

2.  Accédez à l’onglet **Admin** .

3.  Cliquez sur **Ajouter un utilisateur**.

4.  Cliquez sur le bouton **Ajouter** .

5.  L' **Adresse de messagerie** de l’utilisateur ajouté doit correspondre le nom d’utilisateur des utilisateurs dans Azure AD qui vous souhaitez accorder l’accès. Si le nom d’utilisateur et votre adresse électronique n’est pas la même valeur dans votre organisation, vous pouvez utiliser la **Evidence.com > attributs > authentification unique** section du portail classique Azure pour modifier la nameidenitifer envoyée à Evidence.com pour être l’adresse de messagerie.


## <a name="assigning-users-to-evidencecom"></a>Affectation d’utilisateurs à Evidence.com

Pour les utilisateurs AAD générés puissent voir Evidence.com sur leur panneau d’accès, ils doivent être affectées à accéder à l’intérieur du portail classique Azure.

**Pour attribuer aux utilisateurs Evidence.com :**

1.  Dans la page de démarrage rapide de Evidence.com dans le portail Azure classique, cliquez sur **affecter des utilisateurs à Evidence.com**.
 
2.  Dans le menu **Afficher** , indiquez si vous voulez affecter un utilisateur ou un groupe Evidence.com, puis cliquez sur le bouton de coche.
 
3.  Dans la liste des **utilisateurs** , sélectionnez les utilisateurs à grouper dans laquelle vous voulez affecter Evidence.com.
 
4.  Dans le pied de page, cliquez sur le bouton **affecter** .


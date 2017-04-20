<properties
    pageTitle="Azure B2C annuaire Active : Outil d’assistance de personnalisation Page UI | Microsoft Azure"
    description="Un outil d’assistance permet de montrer la fonctionnalité de personnalisation de l’interface utilisateur de page dans Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure B2C annuaire Active : Un outil d’assistance permet de montrer la fonctionnalité de personnalisation page utilisateur interface (IU)

Cet article est associée à l' [article principal de la personnalisation de l’interface utilisateur](active-directory-b2c-reference-ui-customization.md) de B2C Azure Active Directory (AD Azure). Les étapes suivantes décrivent comment la fonctionnalité de personnalisation de l’interface utilisateur de page à l’aide de contenu HTML, CSS exemple que nous avons fourni.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtenir un client Azure AD B2C

Avant de pouvoir personnaliser rien, vous devrez [obtenir un client Azure AD B2C](active-directory-b2c-get-started.md) si vous n’en avez pas déjà.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Créer une stratégie d’abonnement ou se connecter

L’exemple de contenu que nous avons fournies peut servir à customze deux pages dans une [stratégie d’abonnement ou de connexion](active-directory-b2c-reference-policies.md): la [page de connexion unifiée](active-directory-b2c-reference-ui-customization.md) et la [page attributs d’identification automatique](active-directory-b2c-reference-ui-customization.md). Lors de la [Création de votre stratégie d’abonnement ou se connecter](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), ajouter des comptes locaux (adresse de messagerie), Facebook, Google et Microsoft en tant que **fournisseurs d’identité**. Il s’agit des seules IDPs acceptant à notre exemple de contenu HTML.  Vous pouvez également ajouter un sous-ensemble de ces IDPs si vous le souhaitez.

## <a name="register-an-application"></a>Enregistrer une application

Vous devrez [Enregistrer une application](active-directory-b2c-app-registration.md) de votre client B2C qui peut être utilisé pour exécuter votre stratégie. Après avoir enregistré votre application, vous avez plusieurs options qui vous permet d’exécuter votre stratégie d’abonnement :

- Créer une de la AD B2C Azure applications de démarrage rapide répertoriées dans la section « Prise en main » de [se vers le haut et se connecter consommateurs dans vos applications](active-directory-b2c-overview.md#getting-started).
- Utiliser l’application [Azure AD B2C laboratoire](https://aadb2cplayground.azurewebsites.net) prédéfinie. Si vous choisissez d’utiliser le laboratoire, vous devez enregistrer une application dans votre client B2C à l’aide de la **Rediriger URI** `https://aadb2cplayground.azurewebsites.net/`.
- Utiliser le bouton **Exécuter** sur votre stratégie dans le [portail Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personnaliser votre stratégie

Pour personnaliser l’apparence de votre stratégie, vous devez tout d’abord créer des fichiers CSS et HTML en utilisant les conventions d’Azure AD B2C spécifiques. Vous pouvez ensuite télécharger votre contenu statique vers un emplacement accessible au public afin que Azure AD B2C d’y accéder. Ceci peut être votre propre serveur web dédié, stockage d’objets Blob Azure, réseau de distribution de contenu Azure ou tout autre statique d’hébergement ressource fournisseur. La configuration requise uniquement est que votre contenu est disponible sur HTTPS et sont accessibles à l’aide de CORS. Une fois que vous avez exposé votre contenu statique sur le web, vous pouvez modifier votre stratégie pour désigner cet emplacement et présentent ce contenu à vos clients. L' [article de personnalisation de l’interface utilisateur principale](active-directory-b2c-reference-ui-customization.md) décrit en détail comment fonctionne la fonctionnalité de personnalisation Azure AD B2C.

Aux fins de ce didacticiel, nous avons déjà créé quelques exemples de contenu et hébergé sur stockage d’objets Blob Azure. L’exemple de contenu est une personnalisation très simple dans le thème de notre société fictive, « Décide ». Pour tester dans votre propre stratégie, procédez comme suit :

1. Se connecter à votre client dans le [portail Azure](https://portal.azure.com/) et accédez à la carte de fonctionnalités B2C.
2. **Stratégies d’abonnement ou se connecter** , puis cliquez sur votre stratégie (par exemple, « b2c\_1\_se\_des\_se\_dans »).
3. Cliquez sur **Personnalisation de l’interface utilisateur de la Page** , puis sur **page d’abonnement ou connexion unifiée**.
4. Activer ou désactiver le commutateur de **page personnalisée utiliser** sur **Oui**. Dans le champ **URI de la page personnalisée** , entrez `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Cliquez sur **OK**.
5. Cliquez sur **la page d’abonnement à un compte Local**. Activer ou désactiver le commutateur **utiliser un modèle personnalisé** sur **Oui**. Dans le champ **URI de la page personnalisée** , entrez `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Répétez l’étape même pour la **page d’abonnement à un compte de mise en réseau**.
 Cliquez sur **OK** à deux reprises pour fermer les cartes de personnalisation de l’interface utilisateur.
6. Cliquez sur **Enregistrer**.

Vous pouvez désormais essayer votre stratégie personnalisée. Vous pouvez utiliser votre propre application ou le laboratoire Azure AD B2C si vous le souhaitez, mais vous pouvez également cliquer sur la commande **Exécuter maintenant** dans la carte de stratégie. Sélectionnez votre application dans la zone de liste déroulante et choisissez la redirection URI appropriée. Cliquez sur le bouton **Exécuter maintenant** . Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter via l’expérience utilisateur de l’inscription de votre application avec le nouveau contenu en place !

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Télécharger l’exemple de contenu à Azure Blob Storage

Si vous voulez utiliser le stockage d’objets Blob Azure pour héberger votre contenu de la page, vous pouvez créer votre propre compte de stockage et utilisez notre outil d’assistance B2C pour Téléchargez vos fichiers.

### <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** > **données + stockage** > **compte de stockage**. Vous avez besoin d’un abonnement Azure pour créer un compte de stockage d’objets Blob Azure. Vous pouvez vous inscrire une version d’évaluation gratuite sur le [site Web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Indiquez un **nom** pour le compte de stockage (par exemple, « contoso ») et choisissez les sélections appropriées pour le **niveau de tarification**, **groupe de ressources** et **d’abonnement**. Vérifiez que vous avez la possibilité de **code confidentiel pour Startboard** cochée. Cliquez sur **créer**.
4. Revenez à la Startboard et cliquez sur le compte de stockage que vous venez de créer.
5. Dans la section **Résumé** , cliquez sur **conteneurs**, puis cliquez sur **+ Ajouter**.
6. Indiquez un **nom** pour le conteneur (par exemple, « b2c ») et sélectionnez **Blob** comme le **type d’accès**. Cliquez sur **OK**.
7. Le conteneur dans lequel vous avez créé s’affichent dans la liste sur la carte **des objets BLOB** . Notez l’URL du conteneur ; par exemple, il doit ressembler à celui `https://contoso.blob.core.windows.net/b2c`. Fermer la carte **des objets BLOB** .
8. Dans la carte de compte de stockage, cliquez sur **les clés** et notez les valeurs des champs **Nom de compte de stockage** et **Primaire touche d’accès rapide** .

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** > **données + stockage** > **compte de stockage**. Vous avez besoin d’un abonnement Azure pour créer un compte de stockage d’objets Blob Azure. Vous pouvez vous inscrire une version d’évaluation gratuite sur le [site Web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Sélectionnez **Stockage Blob** sous **Type de compte**et laisser les autres valeurs par défaut.  Si vous le souhaitez, vous pouvez modifier le groupe de ressources et un emplacement.  Cliquez sur **créer**.
4. Revenez à la Startboard et cliquez sur le compte de stockage que vous venez de créer.
5. Dans la section **Résumé** , cliquez sur **+ conteneur**.
6. Indiquez un **nom** pour le conteneur (par exemple, « b2c ») et sélectionnez **Blob** comme le **type d’accès**. Cliquez sur **OK**.
7. Ouvrez le conteneur **Propriétés**et notez l’URL du conteneur ; par exemple, il doit ressembler à celui `https://contoso.blob.core.windows.net/b2c`. Fermer la carte conteneur.
8. Dans la carte de compte de stockage, cliquez sur l' **Icône de clé** et notez les valeurs des champs **Nom de compte de stockage** et **Primaire touche d’accès rapide** .

> [AZURE.NOTE]
    **Touche d’accès principal** est une information d’identification de sécurité importants.

### <a name="download-the-helper-tool-and-sample-files"></a>Télécharger les fichiers d’outil et d’exemples d’assistance

Vous pouvez télécharger le [stockage d’objets Blob Azure d’assistance outil et exemple les fichiers sous forme de fichier .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloner GitHub :

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Ce référentiel contient un `sample_templates\wingtip` répertoire, qui contient l’exemple HTML, CSS et les images. Pour ces modèles faire référence à votre compte de stockage d’objets Blob Azure, vous devez modifier les fichiers HTML. Ouvrir `unified.html` et `selfasserted.html` et remplacer toutes les instances de `https://localhost` avec l’URL de votre propre conteneur dont vous avez noté des étapes précédentes. Vous devez utiliser le chemin d’accès absolu des fichiers HTML car dans ce cas, le code HTML sera servi par Azure AD, sous le domaine `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Télécharger les fichiers d’exemples

Dans le même référentiel, décompressez `B2CAzureStorageClient.zip` et exécuter la `B2CAzureStorageClient.exe` au sein de fichiers. Ce programme sera simplement télécharger tous les fichiers dans le répertoire que vous spécifiez à votre compte de stockage et activer l’accès CORS pour ces fichiers. Si vous avez suivi les étapes ci-dessus, les fichiers HTML, CSS seront maintenant pointer vers votre compte de stockage. Notez que le nom de votre compte de stockage est le composant qui précède `blob.core.windows.net`; par exemple, `contoso`. Vous pouvez vérifier que le contenu a été téléchargé correctement lorsque vous essayez d’accéder à `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` dans un navigateur. Également utiliser [http://test-cors.org/](http://test-cors.org/) pour vous assurer que le contenu est maintenant CORS activé. (Recherchez « état XHR : 200 » dans le résultat.)

### <a name="customize-your-policy-again"></a>Personnaliser votre stratégie, nouveau

À présent que vous avez téléchargé l’exemple de contenu à votre compte de stockage, vous devez modifier votre stratégie d’inscription pour faire référence à celui-ci. Répétez les étapes présentées dans la section [« Personnaliser votre stratégie »](#customize-your-policy) ci-dessus, cette fois à l’aide URL de votre propre compte de stockage. Par exemple, l’emplacement de votre `unified.html` fichier serait `<url-of-your-container>/wingtip/unified.html`.

Maintenant, vous pouvez utiliser votre propre application ou le bouton **Exécuter** pour exécuter à nouveau votre stratégie. Le résultat doit ressembler presque exactement identiques : vous avez utilisé le même exemple HTML, CSS dans les deux cas. Toutefois, vos stratégies référencez maintenant votre propre instance de stockage d’objets Blob Azure, et vous êtes gratuit modifier et télécharger les fichiers à nouveau en tant que vous consultez. Pour plus d’informations sur la personnalisation du HTML, CSS, consultez l' [article de personnalisation de l’interface utilisateur principale](active-directory-b2c-reference-ui-customization.md).

<properties
    pageTitle="Gérer l’archivage sécurisé clé à l’aide d’infrastructure du langage commun | Microsoft Azure"
    description="Utilisez ce didacticiel pour automatiser les tâches courantes dans l’archivage sécurisé clé à l’aide de l’infrastructure du langage commun"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Gérer l’archivage sécurisé clé à l’aide d’infrastructure du langage commun #
L’archivage sécurisé clé Azure est disponible dans la plupart des régions. Pour plus d’informations, voir la [clé de l’archivage sécurisé tarifs page](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction  
Utilisez ce didacticiel pour vous aider à commencer à utiliser l’archivage sécurisé de clé Azure pour créer un conteneur renforcé (un archivage sécurisé) dans Azure, stocker et gérer des clés de chiffrement et secrets dans Azure. Il vous guide dans le processus d’utilisation Azure disponibilité sur plusieurs plateformes de ligne de commande Interface pour créer un archivage sécurisé qui contient une clé ou un mot de passe que vous pouvez ensuite utiliser avec une application Azure. Il puis vous montre comment une application puis utiliser cette touche ou le mot de passe.

**Durée estimée d’exécution :** 20 minutes

>[AZURE.NOTE]  Ce didacticiel n’inclut pas d’obtenir des instructions sur l’écriture de l’application Azure que l’une des étapes comprend, qui montre comment autoriser une application à utiliser une clé ou un code secret dans l’archivage sécurisé clé.
>
>Pour l’instant, vous ne pouvez pas configurer l’archivage sécurisé de clé Azure dans le portail Azure. Au lieu de cela, suivez ces instructions Interface de ligne de commande de disponibilité sur plusieurs plateformes. Ou, pour obtenir des instructions Azure PowerShell, consultez [ce didacticiel équivalent](key-vault-get-started.md).

Pour plus d’informations générales sur l’archivage sécurisé de clé Azure, consultez [Quel est l’archivage sécurisé de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer ce didacticiel, vous devez disposer des éléments suivants :

- Un abonnement à Microsoft Azure. Si vous n’avez pas une, vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial).
- Interface de ligne de commande version 0.9.1 ou version ultérieure. Pour installer la dernière version et vous connecter à votre abonnement Azure, voir [installer et configurer l’Interface de ligne de commande de disponibilité sur plusieurs plateformes Azure](../xplat-cli-install.md).
- Une application qui sera configurée pour utiliser la clé ou le mot de passe que vous créez dans ce didacticiel. Un exemple d’application est disponible à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Pour plus d’informations, voir le fichier Lisez-moi accompagne.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtenir de l’aide avec Azure disponibilité sur plusieurs plateformes de ligne de commande Interface

Ce didacticiel suppose que vous êtes habitué à l’interface de ligne de commande (Bash, terminaux, invite)

L’aide ou h - paramètre peut être utilisé pour afficher l’aide des commandes spécifiques. Vous pouvez également l’aide azure [commande] format [options] peut également être utilisée pour renvoyer les mêmes informations. Par exemple, toutes les commandes suivantes renvoient les mêmes informations :

    azure account set --help

    azure account set -h

    azure help account set

En cas de doute concernant les paramètres requis par une commande, voir l’aide à l’aide de--aide, -h ou azure [commande].

Vous pouvez également consulter les didacticiels suivants pour vous familiariser avec le Gestionnaire de ressources Azure dans Azure disponibilité sur plusieurs plateformes de ligne de commande Interface :

- [Découvrez comment installer et configurer l’Interface de ligne de commande de disponibilité sur plusieurs plateformes Azure](../xplat-cli-install.md)
- [À l’aide de Azure Interface de ligne de commande de disponibilité sur plusieurs plateformes avec le Gestionnaire de ressources Azure](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Se connecter à vos abonnements

Pour vous connecter à l’aide d’un compte professionnel, utilisez la commande suivante :

    azure login -u username -p password

ou si vous voulez vous connecter en tapant de façon interactive

    azure login

>[AZURE.NOTE]  La méthode de connexion fonctionne uniquement avec un compte professionnel. Compte d’organisation est un utilisateur qui est géré par votre organisation et défini dans client Azure Active Directory de votre organisation.


Si vous n’avez pas actuellement un compte d’organisation et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez facilement créer un à l’aide de la procédure suivante.

1.  Connectez-vous à la connexion au [Portail de gestion Azure](https://manage.windowsazure.com/)et cliquez sur Active Directory.
2.  Si aucun répertoire n’existe, sélectionnez Créer votre annuaire et fournissez les informations requises.
3.  Sélectionnez votre répertoire et ajouter un nouvel utilisateur. Ce nouvel utilisateur est un compte professionnel. Lors de la création de l’utilisateur, vous seront fournies avec une adresse de messagerie de l’utilisateur et un mot de passe temporaire. Enregistrer ces informations qu’il est utilisé dans une autre étape.
4.  À partir du portail, sélectionnez Paramètres, puis sélectionnez les administrateurs. Sélectionnez Ajouter, puis ajoutez le nouvel utilisateur comme administrateur de co-création. Ainsi, le compte d’organisation gérer vos abonnements Azure.
5.  Enfin, vous déconnectez du portail Azure et puis reconnectez-vous en utilisant le nouveau compte d’organisation. S’il s’agit de la première connexion à l’aide de ce compte, vous devrez modifier le mot de passe.

Pour plus d’informations sur l’utilisation d’un compte professionnel avec Microsoft Azure, voir [s’inscrire à Microsoft Azure comme une organisation](../active-directory/sign-up-organization.md).

Si vous avez plusieurs abonnements et que vous souhaitez spécifier un spécifique à utiliser pour l’archivage sécurisé de clé Azure, tapez ce qui suit pour afficher les abonnements pour votre compte :

    azure account list

Ensuite, pour indiquer l’abonnement à utiliser, tapez :

    azure account set <subscription name>

Pour plus d’informations sur la configuration de l’Interface de ligne de commande de disponibilité sur plusieurs plateformes Azure, Découvrez [comment installer et configurer Azure disponibilité sur plusieurs plateformes de ligne de commande Interface](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Passer à l’aide du Gestionnaire de ressources Azure

L’archivage sécurisé clé requiert Azure le Gestionnaire de ressources, donc tapez ce qui suit pour passer en mode Azure le Gestionnaire de ressources :

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Créer un nouveau groupe de ressources

Lorsque vous utilisez le Gestionnaire de ressources Azure, toutes les ressources associées sont créés à l’intérieur d’un groupe de ressources. Nous allons créer un nouveau groupe de ressources « ContosoResourceGroup » pour ce didacticiel.

    azure group create 'ContosoResourceGroup' 'East Asia'

Le premier paramètre est le nom de groupe de ressources et le second paramètre est l’emplacement. Pour un emplacement, utilisez la commande `azure location list` pour savoir comment spécifier un autre emplacement à celui de cet exemple. Si vous avez besoin de plus d’informations, tapez :`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Enregistrer le fournisseur de ressources de l’archivage sécurisé clé
Vérifiez que fournisseur de ressources de l’archivage sécurisé clé est enregistré dans votre abonnement :

`azure provider register Microsoft.KeyVault`

Cette opération ne doit être effectuée qu’une fois par abonnement.


## <a name="create-a-key-vault"></a>Créer un archivage sécurisé clé

Utiliser la `azure keyvault create` commande pour créer un archivage sécurisé clé. Ce script comporte trois paramètres obligatoires : un nom de groupe de ressources, un nom de l’archivage sécurisé clés et l’emplacement géographique.

Par exemple, si vous utilisez le nom de l’archivage sécurisé de ContosoKeyVault, le nom du groupe de ressources de ContosoResourceGroup et l’emplacement de l’Asie de l’est, tapez :

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

Le résultat de cette commande affiche les propriétés de l’archivage sécurisé clé que vous venez de créer. Les deux propriétés les plus importantes sont :

- **Nom**: dans l’exemple il s’agit de ContosoKeyVault. Vous allez utiliser ce nom pour d’autres applets de commande de l’archivage sécurisé clé.
- **vaultUri**: dans l’exemple il s’agit de https://contosokeyvault.vault.azure.net. Applications qui utilisent votre l’archivage sécurisé via son API REST doivent utiliser cet URI.

Votre compte Azure est maintenant autorisé à effectuer les opérations sur cette archivage sécurisé clé. Personne d’autre n’est encore.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Ajouter une clé ou un code secret à l’archivage sécurisé clé

Si vous souhaitez l’archivage sécurisé de clé Azure pour créer une clé de logiciel protégé à votre place, utilisez la `azure key create` commande, puis tapez les informations suivantes :

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Toutefois, si vous avez une clé existante dans un fichier .pem enregistré en tant que fichier local dans un fichier nommé softkey.pem que vous voulez télécharger dans l’archivage sécurisé de clé Azure, tapez les informations suivantes pour importer la clé à partir de la. Fichier PEM, qui permet de protéger la clé par le logiciel dans le service de l’archivage sécurisé clé :

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Vous pouvez désormais faire référence à la clé que vous avez créé ou téléchargé dans l’archivage sécurisé Azure clé, à l’aide de son URI. **Https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** permet de toujours télécharger la version actuelle, puis **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** permet d’obtenir cette version spécifique.

Pour ajouter un code secret pour l’archivage sécurisé, qui est un mot de passe nommé SQLPassword et qui a la valeur de Pa$ $w0rd pour l’archivage sécurisé de clé Azure, tapez ce qui suit :

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Vous pouvez désormais faire référence à ce mot de passe que vous avez ajouté à l’archivage sécurisé Azure clé, à l’aide de son URI. **Https://ContosoVault.vault.azure.net/secrets/SQLPassword** permet de toujours télécharger la version actuelle, puis **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** permet d’obtenir cette version spécifique.

Examinons la clé ou le mot de passe que vous venez de créer :

- Pour afficher votre clé, tapez :`azure keyvault key list --vault-name 'ContosoKeyVault'`
- Pour afficher votre code secret, tapez :`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Enregistrer une application avec Azure Active Directory

Cette étape est généralement être effectuée par un développeur, sur un autre ordinateur. Il n’est pas spécifique à l’archivage sécurisé de clé Azure mais est inclus dans ce cas, pour plus de précision.


>[AZURE.IMPORTANT] Pour terminer le didacticiel, votre compte, l’archivage sécurisé et l’application que vous inscrivez dans cette étape doivent être tous dans le même répertoire Azure.

Les applications qui utilisent un archivage sécurisé clé doivent s’authentifier à l’aide d’un jeton d’Azure Active Directory. Pour ce faire, le propriétaire de l’application doit d’abord inscrire l’application dans leur Azure Active Directory. À la fin de l’enregistrement, le propriétaire de l’application récupère les valeurs suivantes :


- Un **ID de l’Application** (également appelé ID de Client) et une **clé d’authentification** (également connu sous le secret partagé). L’application doit présenter ces deux valeurs à Azure Active Directory, afin d’obtenir un jeton. Comment l’application est configurée pour cela dépend de l’application. Pour l’application d’exemple de l’archivage sécurisé clé, le propriétaire de l’application définit les valeurs suivantes dans le fichier app.config.



Pour enregistrer l’application dans Azure Active Directory :

1. Connectez-vous au portail Azure.
2. Sur la gauche, cliquez sur **Active Directory**et sélectionnez le répertoire dans lequel enregistré votre application. <br> <br> Remarque : Vous devez sélectionner le même répertoire qui contient l’abonnement Azure avec lequel vous avez créé votre l’archivage sécurisé clé. Si vous ne savez pas quel répertoire cela est, cliquez sur **paramètres**, identifier l’abonnement auquel vous avez créé votre l’archivage sécurisé clé et notez le nom de l’annuaire affichée dans la dernière colonne.

3. Cliquez sur **APPLICATIONS**. Si aucun applications n’ont été ajoutées à votre répertoire, cette page affiche uniquement le lien **Ajouter une application** . Cliquez sur le lien, ou vous pouvez également cliquer, sur l' **Ajouter** dans la barre de commandes.
4.  Dans l’Assistant **Ajouter une APPLICATION** , sur le **que voulez-vous faire ?** page, cliquez sur **Ajouter une application de développe de mon organisation**.
5.  Dans la page **dites-nous relatives à votre application** , spécifiez un nom pour votre application et sélectionnez **WEB APPLICATION et/ou API WEB** (par défaut). Cliquez sur l’icône suivante.
6.  Dans la page **Propriétés de l’application** , spécifiez **l’URL de session** et **Application ID URI** pour votre application web. Si votre application n’a pas ces valeurs, vous pouvez les faire pour cette étape (par exemple, vous pouvez spécifier http://test1.contoso.com pour les deux zones). Il n’a pas importance s’il existe de ces sites ; important est que l’application URI ID pour chaque application est différente pour chaque application dans votre annuaire. Le répertoire utilise cette chaîne pour identifier votre application.
7.  Cliquez sur l’icône terminé pour enregistrer vos modifications dans l’Assistant.
8.  Dans la page de démarrage rapide, cliquez sur **configurer**.
9.  Faites défiler jusqu'à la section **clés** , sélectionnez la durée, puis cliquez sur **Enregistrer**. La page actualise et affiche désormais une valeur de clé. Vous devez configurer votre application avec cette valeur de clé et la valeur **Réf CLIENT** . (Instructions pour cette configuration sera spécifique à l’application).
10. Copiez la valeur d’ID client à partir de cette page, vous allez utiliser dans l’étape suivante pour définir les autorisations sur votre l’archivage sécurisé.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorisez l’application à utiliser la clé ou le code secret

Pour autoriser l’application pour accéder à la clé ou secret dans l’archivage sécurisé, utilisez la `azure keyvault set-policy` commande.

Par exemple, si votre nom de l’archivage sécurisé est ContosoKeyVault et l’application que vous souhaitez autoriser possède un ID de client de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, et que vous voulez autoriser l’application à déchiffrer et connectez-vous avec touches dans votre l’archivage sécurisé, puis exécutez la commande suivante :

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Si vous exécutez sur invite de commandes Windows, vous devez remplacer les apostrophes par des guillemets doubles et également d’échappement les guillemets doubles internes. Par exemple : « [\"déchiffrer\",\"se\"] ».

Si vous souhaitez autoriser cette application même à lire secrets dans votre l’archivage sécurisé, exécutez la commande suivante :

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si vous souhaitez utiliser un module de sécurité matériel (HSM) ##

Pour assurance ajouté, vous pouvez importer ou générer des clés dans les modules de sécurité matérielle (HSM) qui ne laissez jamais la limite HSM. Les modules HSM sont FIPS 140-2 niveau 2 validées. Si cette exigence ne s’applique pas à vous, ignorez cette section et accédez à [Supprimer l’archivage sécurisé clé et clés et secrets associés](#delete-the-key-vault-and-associated-keys-and-secrets).

Pour créer ces touches protégées par HSM, vous devez posséder un abonnement de l’archivage sécurisé qui prend en charge clés protégées par HSM.

Lorsque vous créez la keyvault, ajoutez le paramètre « sku » :

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Vous pouvez ajouter des clés protégées par un logiciel (comme indiqué précédemment) et protégées par HSM à cette archivage sécurisé. Pour créer une clé protégées par HSM, définissez le paramètre de Destination à « HSM » :

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un fichier .pem sur votre ordinateur. Cette commande permet d’importer la clé dans HSM dans le service de l’archivage sécurisé clé :

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

La commande suivante importe un « fournir votre propre clé » package (BYOK). Cela vous permet de générer votre clé dans votre HSM local et transférer vers HSM dans le service de l’archivage sécurisé clé, sans quitter la limite HSM clé :

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Pour obtenir des instructions plus détaillées sur la façon de générer ce package BYOK, voir [comment utiliser les touches HSM-Protected avec l’archivage sécurisé de clé Azure](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Supprimer l’archivage sécurisé clé et des clés associées et secrets

Si vous n’avez plus besoin l’archivage sécurisé clé et la clé ou le secret qu’il contient, vous pouvez supprimer l’archivage sécurisé clé à l’aide de la commande Supprimer keyvault azure :

    azure keyvault delete --vault-name 'ContosoKeyVault'

Ou bien, vous pouvez supprimer un groupe de ressources Azure entière, qui inclut l’archivage sécurisé clé et autres ressources que vous avez inclus dans ce groupe :

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Autres commandes de l’Interface de ligne de commande de disponibilité sur plusieurs plateformes Azure

Autres commandes que cela peut être utile pour la gestion de l’archivage sécurisé de clé Azure.

Cette commande répertorie un affichage de toutes les clés et propriétés sélectionnées tabulaire :

    azure keyvault key list --vault-name 'ContosoKeyVault'

Cette commande affiche une liste complète des propriétés de la clé spécifiée :

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Cette commande répertorie un affichage tabulaire de tous les noms secrets et propriétés sélectionnées :

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Voici un exemple de suppression d’une clé spécifique :

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Voici un exemple de suppression d’un code secret spécifique :

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Étapes suivantes

Pour les références de programmation, voir [guide du développeur de l’archivage sécurisé de clé Azure](key-vault-developers-guide.md).

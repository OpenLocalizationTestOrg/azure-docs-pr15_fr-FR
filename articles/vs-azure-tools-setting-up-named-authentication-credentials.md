<properties
   pageTitle="La configuration nommé informations d’authentification | Microsoft Azure"
   description="Apprenez à fournir des informations d’identification que Visual Studio utiliser pour authentifier les requêtes vers Azure pour publier une application à Azure à partir de Visual Studio ou pour surveiller un service cloud existant... "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="setting-up-named-authentication-credentials"></a>Configuration des informations d’authentification nommée

Pour publier une application à Azure à partir de Visual Studio ou pour surveiller un service cloud existant, vous devez fournir les informations d’identification Visual Studio peut utiliser pour authentifier les requêtes à Azure. Il existe plusieurs endroits dans Visual Studio dans laquelle vous pouvez vous connecter à fournir ces informations d’identification. Par exemple, à partir de l’Explorateur de serveurs, vous pouvez ouvrir le menu contextuel pour le nœud **Azure** et cliquez sur **se connecter à Azure**. Lorsque vous êtes connecté, les informations d’abonnement correspondant à votre compte Azure sont disponibles dans Visual Studio, et que vous devez effectuer tort.

Outils Azure prend également en charge un moyen plus ancien de fournir des informations d’identification, en utilisant le fichier d’abonnement (fichier .publishsettings). Cette rubrique décrit cette méthode, qui est toujours pris en charge dans Azure SDK 2.2.

Les éléments suivants sont nécessaires pour que l’authentification Azure.

- Votre ID de l’abonnement

- Un certificat X.509 v3 valide

>[AZURE.NOTE] La longueur de la clé de certificat X.509 v3 doit être au moins 2048 bits. Azure refuse n’importe quel certificat qui ne répond pas à cette exigence ou qui n’est pas valide.

Visual Studio utilise votre ID de l’abonnement avec les données du certificat comme informations d’identification. Les informations d’identification appropriées sont référencées dans le fichier d’abonnement (fichier .publishsettings), qui contient une clé publique pour le certificat. Le fichier d’abonnement peut contenir des informations d’identification pour plusieurs abonnements.

Vous pouvez modifier les informations d’abonnement à partir de la boîte de dialogue **Créer/modifier abonnement** , comme expliqué plus loin dans cette rubrique.

Si vous voulez créer un certificat vous-même, vous pouvez reportez-vous aux instructions dans [créer et télécharger un certificat de gestion de Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) et puis téléchargez le certificat manuellement sur le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Ces informations d’identification Visual Studio a besoin pour gérer vos services cloud ne sont pas les mêmes informations d’identification sont nécessaires pour authentifier une demande contre les services de stockage Azure.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modifier ou exporter des informations d’identification dans Visual Studio

Vous pouvez également configurer, modifier ou exportez vos informations d’authentification dans la boîte de dialogue **Nouvel abonnement** qui s’affiche si vous effectuez une des opérations suivantes :

- Dans l' **Explorateur de serveurs**, ouvrir le menu contextuel pour le nœud **Azure** , cliquez sur **Gérer les abonnements**, sélectionnez l’onglet **certificats** et cliquez sur le bouton **Nouveau** ou sur **Modifier** .

- Lorsque vous publiez un service cloud Azure à partir de l’Assistant **Application de Azure publier** , sélectionnez **Gérer** dans la liste **Choisir votre abonnement** , puis cliquez sur l’onglet certificats et cliquez sur le bouton **Nouveau** ou sur **Modifier** .

La procédure suivante suppose que la boîte de dialogue **Nouvel abonnement** est ouverte.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Pour définir les informations d’authentification dans Visual Studio

1. Dans **Sélectionner un certificat existant** pour la liste d’authentification, sélectionnez un certificat.

1. Cliquez sur le bouton **Copier le chemin d’accès complet** . Le chemin d’accès pour le certificat (fichier .cer) est copié dans le Presse-papiers.

    >[AZURE.IMPORTANT] Pour publier votre application Azure à partir de Visual Studio, vous devez télécharger ce certificat au [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Pour télécharger le certificat dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Cliquez sur le lien Azure portail.

         Le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885) s’ouvre.

    1. Connectez-vous au [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885), puis cliquez sur le bouton **Services Cloud** .

    1. Choisissez le service cloud qui vous intéresse.

        La page pour le service s’ouvre.

    1. Sous l’onglet **certificats** , cliquez sur le bouton **Télécharger** .

    1. Collez le chemin d’accès complet du fichier .cer que vous venez de créer, puis entrez le mot de passe que vous avez spécifié.

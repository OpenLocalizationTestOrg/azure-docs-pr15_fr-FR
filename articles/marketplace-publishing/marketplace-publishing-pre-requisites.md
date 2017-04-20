<properties
   pageTitle="Conditions préalables pour les techniques de création d’une offre pour l’Azure Marketplace | Microsoft Azure"
   description="Comprendre la configuration requise pour créer et déployer une offre à Azure Marketplace pour d’autres personnes à acheter."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Conditions préalables générales pour la création d’une offre de Azure Marketplace
Comprendre les conditions préalables générales, orientées entreprise-processus qui sont nécessaires pour avancer avec le processus de création d’une offre.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Vous assurer que vous êtes inscrit en tant que vendeur auprès de Microsoft
Pour obtenir des instructions détaillées sur l’enregistrement d’un compte vendeur auprès de Microsoft, accédez à [l’enregistrement et la création de comptes](marketplace-publishing-accounts-creation-registration.md).

- **Si votre société est déjà enregistrée en tant que vendeur dans le centre de développement et que vous voulez créer une nouvelle offre,** puis connectez-vous à la publication du portail avec le même id de messagerie avec le centre de développement inscription est effectuée. Cette étape est requise pour que le portail du centre de développement et à la publication sont liés entre eux.
- **Si votre société est déjà enregistrée en tant que vendeur dans le centre de développement et que vous voulez modifier une offre existante,** puis une connexion à la publication du portail avec le compte d’administrateur ou avec un compte qui est ajouté en tant qu’un administrateur de co-création dans la publication portail. Étapes à suivre pour ajouter un compte d’administrateur de la co-création est donné ci-dessous.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Étapes à suivre pour ajouter un administrateur de co-création dans le portail de publication
Les administrateurs de la publication portail peut ajouter d’autres membres de l’entreprise, qui travaillent sur l’application, en tant qu’un administrateur de co-création dans la publication portail. **qu’En supposant que vous êtes administrateur,** indiquées ci-dessous sont les étapes pour ajouter un administrateur de co-création-.

>[AZURE.NOTE] Pour les nouveaux utilisateurs, avant que vous ajoutez un administrateur de co-création dans la publication portail, assurez-vous que vous avez créé au moins une application dans la publication portail. Ceci est requis comme l’onglet **éditeurs** apparaissent uniquement après la création d’au moins une application dans la publication portail.

1. Vérifiez que l’id de messagerie d’administration de la co-création est un account(MSA) Microsoft. Dans le cas contraire, enregistrer en tant qu’un MSA à l’aide de ce [lien](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Vous assurer qu’il existe au moins une application sous le compte d’administrateur avant d’essayer d’ajouter un administrateur de co-création-.
3. Une fois les étapes ci-dessus sont exécuté, connectez-vous à la publication du portail avec l’id de courrier électronique d’administration de la co-création, puis se déconnecter.
4. Maintenant se connecter à la publication du portail de l’id de messagerie d’administration.
5. Accédez à éditeurs -> sélectionner votre compte -> administrateurs -> ajouter l’administrateur de co-création (capture d’écran ci-dessous)

    ![dessin](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Ce que les adresses de messagerie fournies aux différentes étapes de la publication (par exemple, centre de développement, portail de publication) sont contrôlées pour toutes les communications de Microsoft.
7. Pour l’enregistrement du centre de développement, évitez d’utiliser un compte associé à une seule personne. Cette opération est suggérée pour la suppression de dépendance à partir d’une seule personne.
8. Si vous rencontrez des problèmes avec l’enregistrement du centre de développement, Veuillez déclencher un tickets à l’aide de ce [lien](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Étapes à suivre pour supprimer un administrateur de co-création dans la publication portail
**qu’En supposant que vous êtes administrateur,** indiquées ci-dessous sont les étapes pour supprimer un administrateur de co-création-.

1. Se connecter à la publication du portail de l’id de messagerie d’administration.
2. Accédez à **éditeurs** -> sélectionner votre compte -> **administrateurs** -> **- Coadministrateurs**.
3. Cliquez sur le bouton **X** suivant à la co-création-administrateur que vous voulez supprimer assurances (capture d’écran ci-dessous).

    ![dessin](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] Vous n’avez pas à société taxe et la banque d’informations sur si vous envisagez de publier des offres gratuites uniquement (ou mettre votre propre licence).

> L’enregistrement de la société doit être terminé pour commencer. Toutefois, si votre société fonctionne sur les informations de taxe et banque dans le compte Microsoft Developer, les développeurs peuvent commencer à travailler sur la création de l’image machines virtuelles dans le [Portail de publication](https://publish.windowsazure.com), obtention certifié et le test dans l’environnement intermédiaire Azure. Vous avez besoin de l’approbation de compte vendeur complète uniquement pour la dernière étape de la publication de votre offre à l’Azure Marketplace.

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Acquérir un abonnement Azure « paiement »
Il s’agit de l’abonnement que vous utiliserez pour créer vos images machine virtuelle et transmettre les images à l' [Azure Marketplace](https://azure.microsoft.com/marketplace/). Si vous n’avez pas d’un abonnement existant, puis inscrivez-vous à https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>« Vendre de » pays
> [AZURE.WARNING]
Avant de pouvoir vendre vos services sur Azure Marketplace, vous devez vous assurer que votre entité inscrite est depuis un des pays agréés « vendre-de ». Cette restriction est pour des raisons de paiement et fiscal. Nous cherchons activement pour développer la liste des pays dans un futur proche, donc Restez connecté. Pour obtenir la liste complète, voir la section 1 b des [stratégies de participation Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## <a name="next-steps"></a>Étapes suivantes
Une fois que les conditions préalables vont sont remplies, suivant est l’offre conditions préalables techniques spécifiques. Cliquez sur le lien vers l’article pour le type d’offre correspondantes que vous voulez créer de Azure Marketplace.

- [Conditions préalables techniques machine virtuelle](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Conditions préalables modèle techniques solution](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)

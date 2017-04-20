<properties
    pageTitle="Résoudre les problèmes : Créer et vous connecter à un espace de travail d’apprentissage automatique | Microsoft Azure"
    description="Solutions aux problèmes courants dans la création et la connexion à un espace de travail d’apprentissage automatique Azure"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guide de dépannage : créer et vous connecter à un espace de travail d’apprentissage automatique

Ce guide fournit des solutions pour certains fréquemment rencontrés défis lorsque vous configurez des espaces de travail apprentissage automatique Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Propriétaire de l’espace de travail

Lorsque vous créez un nouvel espace de travail d’apprentissage automatique, l’ID que vous entrez dans le champ propriétaire de l’espace de travail doit être un compte Microsoft valide (anciennement Windows Live ID), par exemple, john-contoso@live.com ou john-contoso@hotmail.com. Il ne peut pas être un compte non Microsoft, tels que votre compte de messagerie d’entreprise. Pour créer un compte Microsoft gratuit, accédez à [www.live.com](http://www.live.com).

Notez que le compte que vous avez utilisé pour vous connecter au portail Azure pour créer l’espace de travail automatiquement ne pas autorisé à *Ouvrir* cet espace de travail, sauf si vous spécifiez ce compte en tant que le propriétaire. Pour ouvrir un espace de travail dans Machine apprentissage Studio, vous devez être connecté au Account Microsoft qui a été défini comme le propriétaire de l’espace de travail, ou vous devez recevoir une invitation de propriétaire pour participer à l’espace de travail. À partir du portail classique Azure, cependant, vous pouvez *Gérer* l’espace de travail, qui inclut la possibilité de modifier le propriétaire et configurer l’accès.

Pour plus d’informations sur la gestion d’un espace de travail, voir [gérer un espace de travail d’apprentissage automatique Azure].

[Gérer un espace de travail d’apprentissage automatique Azure]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Régions autorisées

Apprentissage automatique est actuellement disponible dans un nombre limité de régions. Si votre abonnement n’inclut pas une de ces régions, vous pouvez voir le message d’erreur, « Vous n’avez aucun abonnement dans les régions autorisées. »

Pour faire en sorte qu’une région ajoutés à votre abonnement, sélectionnez **Contacter le support technique Microsoft** à partir du portail classique Azure, choisissez comme type de problème de **facturation** , puis suivez les invites pour envoyer votre demande.

![Contacter le support technique Microsoft][screen1]

## <a name="storage-account"></a>Compte de stockage

Le service d’apprentissage automatique a besoin d’un compte de stockage pour stocker les données. Vous pouvez utiliser un compte de stockage existant, ou vous pouvez créer un nouveau compte de stockage lorsque vous créez le nouvel espace de travail d’apprentissage automatique (si vous avez quota pour créer un nouveau compte de stockage).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Créer l’espace de travail][screen2]

Après que le nouvel espace de travail d’apprentissage automatique est créé, vous pouvez vous connecter à Machine apprentissage Studio en utilisant le compte Microsoft que vous avez spécifié en tant que le propriétaire de l’espace de travail. Si vous rencontrez le message d’erreur « Espace de travail non trouvé » (semblable à la capture d’écran ci-dessous), utilisez les étapes suivantes pour supprimer les cookies de votre navigateur.

![Espace de travail non trouvé][screen3]

**Pour supprimer les cookies de navigateur**

Si vous utilisez Internet Explorer, cliquez sur le bouton **Outils** dans le coin supérieur droit et sélectionnez **options Internet**.  

![Options Internet][screen4]

Sous l’onglet **Général** , cliquez sur **Supprimer...**

![Onglet Général][screen5]

Dans la boîte de dialogue **Supprimer l’historique de navigation** , vérifiez que **les Cookies et données de site Web** est sélectionnée, puis cliquez sur **Supprimer**.

![Supprimer les cookies][screen6]

Une fois que les cookies sont supprimés, redémarrez le navigateur, puis accédez à la page [Microsoft Azure Machine Learning](https://studio.azureml.net) . Lorsque vous y êtes invité pour un nom d’utilisateur et mot de passe, entrez le même compte Microsoft que vous avez spécifié en tant que le propriétaire de l’espace de travail.

Notre objectif est de rendre l’expérience d’apprentissage automatique comme transparente que possible. Publiez des commentaires et des problèmes lors du [forum d’apprentissage automatique Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour nous aider à mieux vous.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

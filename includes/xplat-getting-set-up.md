<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>À l’aide d’Azure infrastructure du langage commun

Les étapes suivantes vous aident à utiliser Azure infrastructure du langage commun facilement avec la version la plus récente et l’abonnement approprié. Si vous avez besoin installer Azure infrastructure du langage commun et le connecter à votre compte tout d’abord, consultez l' [Interface de ligne Azure (Azure commande)](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Étape 1 : Version de mise à jour Azure infrastructure du langage commun

Pour utiliser Azure infrastructure du langage commun pour les commandes impératives avec le mode de gestion de service, vous devez posséder une version récente si possible. Pour vérifier votre version, tapez `azure --version`. Vous devriez voir quelque chose comme :

    $ azure --version
    0.8.17 (node: 0.10.25)

Si vous souhaitez mettre à jour votre version d’Azure infrastructure du langage commun, voir [Azure infrastructure du langage commun](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Étape 2 : Configurer le compte Azure et abonnement

Une fois que vous avez connecté votre infrastructure du langage commun Azure avec le compte que vous souhaitez utiliser, vous devrez plusieurs abonnements. Dans ce cas, vous devez examiner les abonnements disponibles pour votre compte en tapant `azure account list`, puis sélectionnez l’abonnement que vous souhaitez utiliser en tapant `azure account set <subscription id or name> true` où _id de l’abonnement ou le nom_ est l’id de l’abonnement ou le nom de l’abonnement que vous voulez utiliser dans la session active. Vous devez voir ce qui suit :

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Si vous n’avez pas déjà un compte Azure, mais vous avez un abonnement à un abonnement MSDN, vous pouvez obtenir gratuit Azure crédits en activant vos [avantages d’abonné MSDN ici](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) --ou vous peuvent utiliser le compte gratuit. Des options fonctionnent pour l’accès Azure.

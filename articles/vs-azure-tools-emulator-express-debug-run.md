<properties
   pageTitle="À l’aide d’émulateur Express pour exécuter et déboguer un service cloud sur un ordinateur local | Microsoft Azure"
   description="À l’aide d’émulateur Express pour exécuter et déboguer un service cloud sur un ordinateur local"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>À l’aide d’émulateur Express pour exécuter et déboguer un service cloud sur un ordinateur local

À l’aide d’émulateur Express, vous pouvez tester et déboguer un service cloud sans Visual Studio en cours d’exécution en tant qu’administrateur. Vous pouvez définir des paramètres de votre projet à utiliser émulateur Express ou l’émulateur complet, selon les besoins de votre service cloud. Pour plus d’informations sur l’émulateur complète, voir [exécuter une Application Azure dans l’émulateur de calcul](./storage/storage-use-emulator.md). Émulateur Express a été tout d’abord incluses dans Azure SDK 2.1, et à partir d’Azure SDK 2.3, il est l’émulateur par défaut.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>À l’aide d’émulateur Express dans l’IDE Visual Studio

Lorsque vous créez un projet dans Azure SDK 2.3 ou version ultérieure, émulateur Express est déjà sélectionné. Pour les projets existants qui ont été créées dans une version antérieure du Kit de développement, suivez ces étapes pour sélectionner émulateur Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Pour configurer un projet pour utiliser émulateur Express

1. Dans le menu contextuel pour le projet Azure, sélectionnez **Propriétés**, puis l’onglet **Web** .

1. Sous **Serveur de développement Local**, cliquez sur le bouton **d’option utiliser IIS Express** . Émulateur Express n’est pas compatible avec le serveur Web IIS.

1. Sous **émulateur**, sélectionnez la case d’option **Utiliser émulateur Express** .

    ![Émulateur Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Lancement d’émulateur Express à une invite de commandes

À l’invite de commandes, vous pouvez démarrer la version expresse de l’émulateur de calcul Azure, csrun.exe, à l’aide de l’option /useemulatorexpress.

## <a name="limitations"></a>Limitations

Avant d’utiliser émulateur Express, vous devez tenir compte des certaines limitations :

- Émulateur Express n’est pas compatible avec le serveur Web IIS.

- Votre service cloud peut contenir plusieurs rôles, mais chaque rôle est limité à une instance.

- Vous ne pouvez pas accéder à numéros de port inférieur à 1000. Par exemple, si vous utilisez un fournisseur d’authentification qui en règle générale, utilise un port inférieur à 1000, vous devrez peut-être modifier cette valeur en un numéro de port qui est supérieure à 1000.

- Les limitations qui s’appliquent à l’émulateur de calcul Azure s’appliquent également à émulateur Express. Par exemple, vous ne peut pas contenir plus de 50 instances de rôle par déploiement. Voir [exécuter une Application dans l’émulateur cluster Azure](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Étapes suivantes

[Services de Cloud débogage](https://msdn.microsoft.com/library/azure/ee405479.aspx)

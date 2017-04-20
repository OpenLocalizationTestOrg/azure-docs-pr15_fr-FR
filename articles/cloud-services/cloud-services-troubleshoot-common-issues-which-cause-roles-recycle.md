<properties
   pageTitle="Causes courantes des rôles de Service Cloud recyclage | Microsoft Azure"
   description="Un rôle de service de nuage soudainement réutilise peut entraîner des interruptions importantes. Voici quelques-uns des problèmes courants occasionnant rôles à recycler, qui peuvent vous aider à réduire les temps d’arrêt."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Problèmes occasionnant rôles de Corbeille

Cet article décrit quelques-unes des causes courantes des problèmes de déploiement et fournit des conseils de dépannage pour vous aider à résoudre ces problèmes. Indique qu’il existe un problème avec une application est lorsque l’instance de rôle ne démarre pas, ou il navigue entre les États lors de l’initialisation, occupé (e) et d’arrêt.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dépendances runtime manquantes

Si un rôle dans votre application repose sur n’importe quel assembly qui ne fait pas partie de .NET Framework ou l’Azure managé bibliothèque, vous devez inclure explicitement cet assembly dans le package d’application. N’oubliez pas que les autres structures Microsoft ne sont pas disponibles dans Azure par défaut. Si votre rôle repose sur un tel cadre, vous devez ajouter ces assemblys pour le package d’application.

Avant de générer et empaqueter votre application, vérifiez les points suivants :

- Si vous utilisez Visual studio, vérifiez que la propriété **Copie locale** est définie sur **True** pour chaque assembly référencé dans votre projet qui ne fait pas partie du Kit de développement Azure ou .NET Framework.

- Vérifiez que le fichier web.config ne fait pas référence d’assemblys inutilisées dans l’élément compilation.

- L' **Action de génération** de chaque fichier .cshtml est défini sur le **contenu**. Cela garantit que les fichiers s’affichent correctement dans le package et permet à d’autres fichiers référencés doivent apparaître dans le package.

## <a name="assembly-targets-wrong-platform"></a>Plate-forme incorrecte assemblage cibles

Azure est un environnement 64 bits. Par conséquent, assemblys compilés pour une cible 32 bits ne fonctionnent pas dans Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rôle lève une exception non gérée lors de l’initialisation ou l’arrêt

Les exceptions levées par les méthodes de la classe [RoleEntryPoint] , qui inclut la [OnStart], [OnStop]et les méthodes [d’exécution] , sont les exceptions non gérées. Si une exception non gérée se trouve dans une des méthodes suivantes, le rôle sera Corbeille. Si le rôle est recyclage à plusieurs reprises, il peut être lever une exception non gérée chaque fois qu’il tente de démarrer.

## <a name="role-returns-from-run-method"></a>Rôle renvoie à partir de la méthode Run

La méthode [Run] est destinée à s’exécuter indéfiniment. Si votre code substitue la méthode [exécuter] , il doit rester en veille indéfiniment. Si la méthode [Run] retourne, le rôle réutilise.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Paramètre DiagnosticsConnectionString incorrect

Si application utilise Diagnostics Azure, votre fichier de configuration de service doit indiquer la `DiagnosticsConnectionString` paramètre de configuration. Ce paramètre doit spécifier une connexion HTTPS à votre compte de stockage dans Azure.

Pour vous assurer que votre `DiagnosticsConnectionString` paramètre est correct avant de déployer votre package d’application sur Azure, vérifiez les points suivants :  

- La `DiagnosticsConnectionString` définir des points à un compte de stockage valide dans Azure.  
  Par défaut, ce paramètre pointe vers le compte de stockage émulée, afin que vous devez modifier ce paramètre explicitement avant de déployer votre package d’application. Si vous ne modifiez pas ce paramètre, une exception est levée lors de l’instance de rôle tente de démarrer le moniteur de diagnostic. Cela peut entraîner l’instance de rôle recycler indéfiniment.

- La chaîne de connexion est spécifiée dans le [format](../storage/storage-configure-connection-string.md)suivant. (Le protocole doit être spécifié en tant que HTTPS). Remplacez *MyAccountName* par le nom de votre compte de stockage et *MyAccountKey* avec votre clé d’accès :    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Si vous développez votre application à l’aide des outils Azure pour Microsoft Visual Studio, vous pouvez utiliser les [pages de propriétés](https://msdn.microsoft.com/library/ee405486) pour définir cette valeur.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificat exporté n’inclut pas de clé privée

Pour exécuter un rôle web sous SSL, vous devez vous assurer que votre certificat de gestion exporté inclut la clé privée. Si vous utilisez le *Gestionnaire de certificats Windows* pour exporter le certificat, veillez à sélectionner **Oui** pour que l’option **Exporter la clé privée** . Le certificat doit être exporté au format PFX, qui est le seul format actuellement pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Afficher d’autres [articles de résolution des problèmes](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pour les services en nuage.

Affichez plus rôle recyclage scénarios en [série de blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Exécuter]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

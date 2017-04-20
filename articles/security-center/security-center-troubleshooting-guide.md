<properties
   pageTitle="Centre de sécurité Azure Guide de dépannage | Microsoft Azure"
   description="Ce document vous aide à résoudre les problèmes dans le centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Guide de dépannage du centre de sécurité Azure
Ce guide est destiné professionnels de l’informatique (IT), les analystes de la sécurité des informations et les administrateurs de cloud dont l’organisation utilisez le centre de sécurité Azure et avez besoin résoudre les que problèmes liés aux centre de sécurité.

## <a name="troubleshooting-guide"></a>Guide de dépannage
Ce guide explique comment résoudre les problèmes liés aux centre de sécurité. La plupart de la résolution des problèmes dans le centre de sécurité exécutée en premier consultant les enregistrements du [Journal d’Audit](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) pour le composant qui a échoué. Journaux d’audit, vous pouvez déterminer :

- Les opérations qui ont été eu lieu
- Qui a initié l’opération
- Lorsque l’opération s’est produite
- L’état de l’opération
- Les valeurs d’autres propriétés qui peuvent vous aider à rechercher l’opération

Le journal d’audit contient toutes les opérations d’écriture (place, POST, DELETE) effectuées sur vos ressources, mais elle n’inclut pas les opérations de lecture (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Résolution des problèmes d’installation de l’agent de surveillance dans Windows

Le centre de sécurité l’agent de surveillance est utilisé pour effectuer la collecte de données. Une fois la collecte des données est activée et l’agent est correctement installée sur l’ordinateur cible, ces processus doivent être d’exécution :

- ASMAgentLauncher.exe - Azure l’Agent de surveillance 
- ASMMonitoringAgent.exe - extension de contrôle de la sécurité Azure
- ASMSoftwareScanner.exe – Gestionnaire analyse Azure

L’extension de contrôle de la sécurité Azure analyse pour une configuration pertinents sécurité différents et collecte des journaux de sécurité à partir de la machine virtuelle. Le Gestionnaire d’analyse est utilisé comme un scanneur correctif.

Si l’installation est correctement effectuée, vous devez voir une entrée similaire à celui ci-dessous dans les journaux d’Audit pour la cible machine virtuelle :

![Journaux d’audit](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Vous pouvez également obtenir plus d’informations sur le processus d’installation en lisant les journaux de l’agent situés à *%systemdrive%\windowsazure\logs* (exemple : C:\WindowsAzure\Logs).

> [AZURE.NOTE] Si l’Agent de centre de sécurité Azure ne fonctionne pas correctement, vous devrez redémarrer la cible machine virtuelle car il n’existe aucune commande pour arrêter et démarrer l’agent.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Résolution des problèmes d’installation de l’agent de surveillance dans Linux
Lors de la résolution des problèmes d’installation de l’Agent machine virtuelle dans un système Linux, vous devez vous assurer que l’extension a été téléchargée dans/var/bibliothèque/waagent /. Vous pouvez exécuter la commande ci-dessous pour vérifier si elle a été installé :

`cat /var/log/waagent.log` 

Les autres fichiers journaux que vous pouvez consulter pour but de résolution des problèmes sont : 

- /var/log/mdsd.Err
- / var/log/azure /

Dans un système opérationnel vous devriez voir une connexion au processus mdsd sur TCP 29130. Il s’agit de communiquer avec le processus mdsd journal système. Vous pouvez valider ce comportement en exécutant la commande suivante :

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Contacter le Support Microsoft

Certains problèmes peuvent être identifiés en suivant les instructions indiquées dans cet article, d’autres personnes que sont également disponibles présentées lors du [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)du centre de sécurité public. Cependant si vous avez besoin avec davantage de résolution des problèmes, vous pouvez ouvrir une nouvelle demande de support à l’aide du portail Azure comme indiqué ci-dessous : 

![Support technique Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment configurer des stratégies de sécurité dans le centre de sécurité Azure. Pour en savoir plus sur le centre de sécurité Azure, voir les rubriques suivantes :

- [Guide d’exploitation et de planification de la sécurité Azure centre](security-center-planning-and-operations-guide.md) , apprenez à planifier et comprendre les considérations de conception à adopter Azure le centre de sécurité.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md) : Découvrez comment contrôler l’état de vos ressources Azure
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité
- [Surveillance solutions des partenaires avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : rechercher des billets de blog sur la sécurité Azure et conformité

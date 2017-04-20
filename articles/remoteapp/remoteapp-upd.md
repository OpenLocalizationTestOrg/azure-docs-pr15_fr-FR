
<properties 
    pageTitle="Comment Azure RemoteApp enregistrer données et paramètres utilisateur ? | Microsoft Azure"
    description="Découvrez comment Azure RemoteApp enregistre les données de l’utilisateur à l’aide du disque de profil utilisateur."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Comment Azure RemoteApp enregistrer données et paramètres utilisateur ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure enregistre identité de l’utilisateur et les personnalisations sur les appareils mobiles et les sessions. Ces données utilisateur sont stockées dans un disque par collection de sites par utilisateur, appelé un disque de profil utilisateur (UDP). Le disque suit l’utilisateur et garantit que l’utilisateur dispose d’une expérience cohérente, quelle que soit l’endroit où ils se connecter. enregistre 

Disques de profil utilisateur sont complètement transparentes à l’utilisateur : utilisateurs enregistrent des documents dans leur dossier Documents (sur ce qui semble être un lecteur local) et modifier les paramètres de l’application normalement. En même temps, tous les paramètres personnels sont conservées lors de la connexion à Azure RemoteApp à partir de n’importe quel appareil. Tout l’utilisateur voit est leurs données au même endroit.

Chaque UDP présente 50 Go de stockage permanent, les deux paramètres d’application et de données utilisateur. 

Lisez la suite connaître détail des données de profil utilisateur.

>[AZURE.NOTE] Vous avez besoin pour désactiver l’UDP ? Vous pouvez faire que maintenant - extraction billet de blog du Pavithra, [Désactivez utilisateur profil disques (UPDs) dans Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), pour plus d’informations.


## <a name="how-can-an-admin-get-to-the-data"></a>Comment un administrateur peut récupérer les données ?

Si vous devez accéder aux données pour un de vos utilisateurs (pour reprise ou si l’utilisateur quitte l’entreprise), contactez le Support Azure et fournissez les informations d’abonnement pour la collection de sites et identité de l’utilisateur. L’équipe RemoteApp Azure fournit une URL vers le disque dur virtuel. Téléchargez ce disque dur virtuel et récupérer des documents ou des fichiers dont vous avez besoin. Notez que le disque dur virtuel est 50 Go, afin qu’il faudra un peu pour la télécharger.


## <a name="is-the-data-backed-up"></a>Les données sont sauvegardées ?

Oui, nous allons enregistrer une sauvegarde des données utilisateur par emplacement géographique. Les données sont en lecture seule et sont accessibles de la même façon que les données régulières seraient (contact Azure RemoteApp obtenir), si le centre de données principal est arrêté. Les données sont copiées en temps réel à l’emplacement de sauvegarde et nous ne pas conserver des copies de différentes versions. Par conséquent, sur altération des données, nous ne pourrez pas rétablir une bonne version connue précédemment, mais si le centre de données principal est vers le bas, vous ne pourrez pas obtenir des données utilisateur à l’autre emplacement.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>Comment les utilisateurs ont-ils accès l’UDP côté serveur ?

Chaque utilisateur a leur propre répertoire sur le serveur qui correspond à leur UDP : c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>Quelle est la meilleure façon d’utiliser Outlook et UDP ?

RemoteApp Azure enregistre l’état d’Outlook (boîtes aux lettres, les fichiers pst) entre les sessions. Pour ce faire, nous avons besoin du fichier PST stocké dans les données de profil utilisateur (c:\users\<nom d’utilisateur >). Il s’agit de l’emplacement par défaut pour les données, en tant que ne pas modifier l’emplacement, les données persiste entre les sessions.

Nous vous recommandons également que vous utilisez le mode « mis en cache » dans Outlook et utilisez le mode « serveur/online » pour la recherche.

Consultez [cet article](remoteapp-outlook.md) pour plus d’informations sur l’utilisation d’Outlook et Azure RemoteApp.

## <a name="what-about-redirection"></a>Qu’en est-il de la redirection ?
Vous pouvez configurer RemoteApp Azure pour permettre aux utilisateurs d’accéder aux périphériques locaux en définissant la [redirection](remoteapp-redirection.md). Périphériques locaux puis pourront accéder aux données sur l’UDP.

## <a name="can-i-use-my-upd-as-a-network-share"></a>Puis-je utiliser mes UDP comme un partage réseau ?
Non. UPDs ne peut pas être utilisés comme un partage réseau. Un UDP n’est disponible à l’utilisateur lorsque l’utilisateur est connecté activement à Azure RemoteApp.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>Si je supprime un utilisateur à partir d’une collection de sites, est supprimée leur UDP ?

Non, lorsque vous supprimez un utilisateur, nous ne pas supprimer automatiquement l’UDP - au lieu de cela, nous stocker les données jusqu'à ce que vous supprimiez la collection de sites. 90 jours après avoir supprimé la collection de sites, nous supprimer tous les UPDs. 

Si vous avez besoin de supprimer un UDP à partir d’une collection de sites, contactez Azure RemoteApp - nous pouvons supprimer UDP à partir de notre côté.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>Puis-je accéder à UPDs mes utilisateurs (utilisateurs actuelles ou supprimés) ?

Oui, si vous contactez [RemoteApp Azure](mailto:remoteappforum@microsoft.com), nous pouvons configurer vous présentant une URL pour accéder aux données. Vous devrez environ 10 heures télécharger les données ou les fichiers à partir de l’UDP avant l’expiration de l’accès.

## <a name="are-upds-available-offline"></a>Sont UPDs disponibles hors connexion ?

Maintenant que nous ne fournissons pas l’accès hors connexion pour UPDs, au-delà de la fenêtre access heure 10 ci-dessus. Cela signifie que nous n’avons pas actuellement une méthode pour vous permettre d’accéder pour long suffisamment pour effectuer les tâches plus complexes, par exemple, un logiciel antivirus en cours d’exécution sur l’UPDs ou l’accès aux données pour un audit.

## <a name="do-registry-key-settings-persist"></a>Paramètres de clé de Registre sont conservées ?
Oui, écriture dans HKEY_Current_User fait partie de l’UDP.

## <a name="can-i-disable-upds-for-a-collection"></a>Puis-je désactiver UPDs pour une collection de sites ?

Oui, vous pouvez demander RemoteApp Azure pour désactiver UPDs pour un abonnement, mais vous ne pouvez pas faire que vous-même. Cela signifie que UPDs seront désactivées pour toutes les collections de l’abonnement.

Vous souhaiterez peut-être désactiver UPDs dans les situations suivantes : 

- Vous devez terminer accès et le contrôle des données utilisateur (pour auditer et passez en revue à des fins telles que des établissements financiers).
- Vous avez 3e partie utilisateur du profil Gestion des solutions en local et voulez continuer à les utiliser dans votre déploiement d’Azure RemoteApp à un domaine. Cela nécessite l’agent de profil à charger dans l’image de marque. 
- Vous n’avez pas besoin de stockage de données locales ou que toutes les données dans la cloud ou partager des fichiers et voulez contrôle l’enregistrement des données localement à l’aide de RemoteApp Azure.

Pour plus d’informations, voir [Désactiver utilisateur profil disques (UPDs) dans Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) .

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>Puis-je empêcher les utilisateurs d’enregistrer les données sur le lecteur système ?

Oui, mais vous devez configurer que dans l’image du modèle avant de créer la collection de sites. Procédez comme suit pour bloquer l’accès au lecteur système :

1. Exécutez **gpedit.msc** sur l’image du modèle.
2. Accédez à **Configuration utilisateur > modèles d’administration > composants Windows > Explorer**.
3. Sélectionnez les options suivantes :
    - **Masquer les lecteurs spécifiques dans Poste de travail**
    - **Empêcher l’accès aux lecteurs à partir de mon ordinateur**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>Puis-je amorcer UPDs ? Je veux placer des données dans l’UDP est disponible la première fois que l’utilisateur se connecte.

Oui, lorsque vous créez l’image du modèle, vous pouvez ajouter des informations dans le profil par défaut. Ces informations sont ensuite ajoutées à l’UDP.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>Puis-je modifier la taille de l’UDP selon la quantité de données je souhaite stocker ?

Non, toutes les UPDs ont 50 Go de stockage. Si vous voulez stocker différentes quantités de données, procédez comme suit :

1. Désactiver UPDs pour la collection de sites.
2. Configurer un partage de fichiers pour les utilisateurs à accéder à.
3. Charger le partage de fichiers à l’aide d’un script de démarrage. Pour plus d’informations sur les scripts de démarrage dans Azure RemoteApp voir ci-dessous.
4. Demandez aux utilisateurs d’enregistrer toutes les données sur le partage de fichiers.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Comment exécuter un script de démarrage dans Azure RemoteApp ?

Si vous souhaitez exécuter un script de démarrage, commencez par créer une tâche planifiée dans l’image du modèle que vous allez utiliser pour la collection. (Cette *avant* d’exécuter sysprep.) 

![Créer une tâche système](./media/remoteapp-upd/upd1.png)

![Créer une tâche système qui s’exécute lorsqu’un utilisateur ouvre une session](./media/remoteapp-upd/upd2.png)

Sous l’onglet **Général** , n’oubliez pas de changer le **Compte d’utilisateur** sous sécurité à « BUILTIN\Users ».

![Modifier le compte d’utilisateur à un groupe](./media/remoteapp-upd/upd4.png)

La tâche planifiée s’exécutera votre script de démarrage, à l’aide des informations d’identification de l’utilisateur. Planifier la tâche pour exécuter chaque une fois qu’un utilisateur se connecte.

![Définissez le déclencheur pour la tâche « à la session »](./media/remoteapp-upd/upd3.png)

Vous pouvez également utiliser [les scripts de démarrage de stratégie de groupe](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>Qu’en est-il en plaçant un script de démarrage dans le menu Démarrer ? Feriez cela fonctionne ?

En d’autres termes, puis-je créer un fichier .bat qui s’exécute un script de configuration de la fenêtre et enregistrez-le dans le dossier Démarrer\Programmes\Démarrage c:\ProgramData\Microsoft\Windows\Start et puis ont ce script exécuter chaque fois qu’un utilisateur ouvre une session RemoteApp ?

Non, qui n'est pas pris en charge avec Azure RemoteApp, qui utilise RDSH, également ne pas prenant en charge les scripts de démarrage dans le menu Démarrer.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>Puis-je utiliser mstsc.exe (le programme de bureau à distance) pour configurer des scripts d’ouverture de session ?

Non, pas pris en charge par RemoteApp Azure.

## <a name="can-i-store-data-on-the-vm-locally"></a>Puis-je stocker des données sur la machine virtuelle localement ?

NON, les données stockées n’importe où sur la machine virtuelle différente dans l’UDP seront perdues. Il y a un risque élevé l’utilisateur ne recevrez pas de la même machine virtuelle la prochaine heure auxquelles ils vous connecter à Azure RemoteApp. Nous ne conservent pas persistance machine virtuelle de l’utilisateur, afin que l’utilisateur ne sera pas connectez-vous à la même machine virtuelle, et les données seront perdues. En outre, quand nous mettre à jour la collection de sites, les machines virtuelles existantes sont remplacées par un nouvel ensemble de machines virtuelles - ce qui signifie que toutes les données stockées sur l’ordinateur virtuel lui-même sont perdues. Il est recommandé pour stocker les données dans l’UDP, stockage partagé telles que les fichiers Azure, un serveur de fichiers à l’intérieur d’une VNET ou sur le nuage en utilisant un système de stockage cloud comme DropBox.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>Comment monter un partage de fichiers Azure sur un ordinateur virtuel, à l’aide de PowerShell ?

Vous pouvez utiliser l’applet de commande Net PSDrive pour monter le lecteur, comme suit :

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


Vous pouvez également enregistrer vos informations d’identification en exécutant la commande suivante :

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Qui vous permet d’ignorer le - paramètre d’informations d’identification de l’applet de commande New-PSDrive.

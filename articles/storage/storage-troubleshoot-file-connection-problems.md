<properties
    pageTitle="Résolution des problèmes de stockage de fichier Azure | Microsoft Azure"
    description="Résolution des problèmes de stockage de fichier Azure"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Résolution des problèmes de stockage de fichier Azure

Cet article répertorie les problèmes courants liés au stockage de Microsoft Azure fichier lorsque vous vous connectez à partir de clients Windows et Linux. Il fournit également les causes possibles et résolutions pour ces problèmes.

**Problèmes d’ordre général (se produire dans les clients Windows et Linux)**

- [Erreur de quota lorsque vous tentez d’ouvrir un fichier](#quotaerror)

- [Baisse des performances lorsque vous accédez à stockage de fichiers Azure à partir de Windows ou Linux](#slowboth)

**Problèmes de client de Windows**

- [Baisse des performances lorsque vous accédez à stockage de fichiers Azure à partir de Windows 8.1 ou Windows Server 2012 R2](#windowsslow)

- [Erreur 53 tentative de montage un partage de fichiers Azure](#error53)

- [Utiliser NET a réussi, mais je ne vois pas le fichier Azure partager chargée dans l’Explorateur Windows](#netuse)

- [Mon compte de stockage contient « / » et la net use Échec de la commande](#slashfails)

- [Mon application/service n’est pas accessible lecteurs fichiers Azure.](#accessfiledrive)

- [Recommandations supplémentaires pour optimiser les performances](#additional)

**Problèmes de client Linux**

- [Erreur « Vous effectuez la copie un fichier vers une destination qui ne prend pas en charge le chiffrement » lors de la télécharger/copie des fichiers sur des fichiers Azure](#encryption)

- [Partage de l’erreur « Hôte est vers le bas » sur un fichier existant, ou le shell se bloque lors de la liste des commandes sur le point de montage](#errorhold)

- [Erreur de montage 115 lorsque vous tentez d’Azure montage des fichiers sur le VM Linux](#error15)

- [Linux VM rencontre aléatoires délais commandes comme « ls »](#delayproblem)

## <a name="general-problems"></a>Problèmes d’ordre général
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Erreur de quota lorsque vous tentez d’ouvrir un fichier

Dans Windows, vous recevez des messages d’erreur semblables aux suivants :

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**Quota est insuffisant pour traiter cette commande**

**Valeur non valide poignée GetLastError : 53**

Sous Linux, vous recevez des messages d’erreur semblables aux suivants :

**<filename>[autorisation refusée]**

**Dépassé du quota de disque**

#### <a name="cause"></a>Cause

Le problème se produit car vous avez atteint la limite supérieure des poignées d’ouvre simultanées qui sont autorisés pour un fichier.

#### <a name="solution"></a>Solution

Réduisez le nombre de poignées ouvrir simultanées en fermant certaines poignées, puis recommencez. Pour plus d’informations, voir [Microsoft Azure stockage performances et extensibilité élevées aide-mémoire](storage-performance-checklist.md).

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Baisse des performances lorsqu’ils accèdent à stocker des fichiers à partir de Windows ou Linux

- Si vous n’avez pas une configuration de taille minimale e/s spécifique, nous vous recommandons d’utiliser 1 Mo en tant que la taille d’e pour optimiser les performances.

- Si vous connaissez la taille d’un fichier que vous étendez avec écritures finale et que votre logiciel n’a pas les problèmes de compatibilité lors de la queue n’ont pas encore étée dans le fichier contenant des zéros, puis définissez la taille du fichier à l’avance à la place de chaque écriture est une écriture d’extension.

## <a name="windows-client-problems"></a>Problèmes de client de Windows
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Baisse des performances lorsqu’ils accèdent au stockage de fichiers à partir de Windows 8.1 ou Windows Server 2012 R2

Pour les clients qui exécutent Windows 8.1 ou Windows Server 2012 R2, assurez-vous que le correctif [KB3114025](https://support.microsoft.com/kb/3114025) est installé. Ce correctif améliore la créer et fermez la poignée de performances.

Vous pouvez exécuter le script suivant pour vérifier si le correctif a été installé sur :

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si le correctif est installé, le résultat suivant s’affiche :

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0 x 1**

> [AZURE.NOTE]  Images de Windows Server 2012 R2 dans Azure Marketplace ont le correctif KB3114025 installé par défaut démarrage en décembre 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Recommandations supplémentaires pour optimiser les performances

Ne jamais créez ou ouvrez un fichier d’e/s mis en cache qui demande l’accès en écriture, mais pas l’accès en lecture. Autrement dit, lorsque vous appelez **CreateFile()**, spécifiez jamais uniquement **GENERIC_WRITE**, mais toujours spécifier **GENERIC_READ | GENERIC_WRITE**. Une poignée d’écriture seule ne peut pas mettre en cache écritures de petite taille localement, même lorsqu’elle est la poignée de cours uniquement pour le fichier. Cela impose une baisse de performances sur écritures de petite taille. Notez que le mode de « a » à CRT **fopen()** s’ouvre une poignée d’écriture uniquement.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>« Erreur 53 » lorsque vous essayez de charger ou décharger un partage de fichiers Azure

Ce problème peut être dû aux conditions suivantes :

#### <a name="cause-1"></a>Cause 1

« Erreur système 53 s’est produite. Accès refusé. » Pour des raisons de sécurité, les connexions à des partages de fichiers Azure sont bloquées si le canal de communication n’est pas chiffré et la tentative de connexion n’est pas effectuée à partir du centre de données même où se trouvent les partages de fichiers Azure. Chiffrement de canal de communication n’est pas disponible si le client de l’utilisateur du système d’exploitation non prises en charge le chiffrement PME. Ceci est indiqué par une « erreur système 53 s’est produite. Accès refusé » message d’erreur lorsqu’un utilisateur tente de monter un partage de fichiers à partir de locaux ou d’un centre de données différents. Windows 8, Windows Server 2012 et versions ultérieures de chaque demande négociation incluant PME 3.0, qui prend en charge le chiffrement.

#### <a name="solution-for-cause-1"></a>Solution de la Cause 1

Se connecter à partir d’un client qui répond aux exigences de Windows 8, Windows Server 2012 ou versions ultérieures, ou qui se connectent à partir d’un ordinateur virtuel qui se trouve sur le même centre de données en tant que le compte de stockage Azure qui est utilisé pour le partage de fichiers Azure.

#### <a name="cause-2"></a>Cause 2

« Erreur système 53 » lorsque vous montez un partage de fichiers Azure peut se produire si le Port 445 les communications sortantes du centre de données Azure fichiers sont bloquée. Cliquez [ici](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) pour afficher le résumé des fournisseurs de services Internet qui autoriser ou interdire l’accès à partir de port 445.

Bloquent le port Comcast et certaines organisations informatiques. Pour mieux comprendre s’il s’agit de la raison pour laquelle le message « Erreur système 53 », vous pouvez utiliser Portqry pour interroger le point de terminaison TCP:445. Si le point de terminaison TCP:445 est affiché comme filtrés, le port TCP est bloqué. Voici un exemple de requête :

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si le TCP 445 bloqué par une règle sur le chemin d’accès réseau, vous verrez le résultat suivant :

**Le port TCP 445 (service microsoft-ds) : filtré**

Pour plus d’informations sur l’utilisation de Portqry, voir [Description de l’utilitaire de ligne de commande Portqry.exe](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Solution de la Cause 2

Travailler avec votre service informatique pour ouvrir le Port 445 sortantes vers les [plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Cause 3

« Erreur système 53 » peut également être reçue si NTLMv1 communications sont activée sur le client. Vous rencontrez des NTLMv1 activé crée un client moins sécurisé. Par conséquent, les communications seront bloquées pour les fichiers Azure. Pour vérifier s’il s’agit de la cause de l’erreur, vérifiez que la sous-clé de Registre suivante est définie sur une valeur de 3 :

HKLM\System\CurrentControlSet\Control\LSA > LmCompatibilityLevel.

Pour plus d’informations, consultez la rubrique [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) sur TechNet.

#### <a name="solution-for-cause-3"></a>Solution pour Cause 3

Pour résoudre ce problème, rétablissez la valeur LmCompatibilityLevel dans la clé de Registre HKLM\SYSTEM\CurrentControlSet\Control\Lsa à la valeur par défaut de 3.

Fichiers Azure prend en charge uniquement l’authentification NTLMv2. Assurez-vous que la stratégie de groupe est appliquée aux clients. Cela empêchera cette erreur ne se produise. Il est également considérés comme des raisons de sécurité. Pour plus d’informations, voir [Comment faire pour configurer les clients pour utiliser NTLMv2 à l’aide de la stratégie de groupe](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

Le paramètre de stratégie recommandée est **Envoyer les réponses NTLMv2 uniquement**. Cela correspond à une valeur de Registre de 3. Les clients utilisent uniquement l’authentification NTLMv2, et ils utilisent la sécurité de session NTLMv2 si le serveur le prend en charge. Domaine accepte les authentifications LM, NTLM et NTLMv2.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Utiliser NET a réussi, mais ne voyez pas le fichier Azure partager chargée dans l’Explorateur Windows

#### <a name="cause"></a>Cause

Par défaut, l’Explorateur Windows n’est pas exécuté en tant qu’administrateur. Si vous exécutez **net utiliser** à partir d’une invite de commandes administrateur, vous mappez le lecteur réseau « En tant qu’administrateur. » Les lecteurs mappés sont centré sur l’utilisateur, le compte d’utilisateur qui est connecté n’affiche pas les lecteurs si elles sont monter sous un autre compte d’utilisateur.

#### <a name="solution"></a>Solution

Monter le partage à partir d’une ligne de commande non administrateur. Vous pouvez également suivre [cette rubrique TechNet](https://technet.microsoft.com/library/ee844140.aspx) pour configurer la valeur de Registre **EnableLinkedConnections** .

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Mon compte de stockage contient « / » et la net use Échec de la commande

#### <a name="cause"></a>Cause

Lorsque la commande **net use** est exécutée sous invite de commandes (cmd.exe), il est analysé en ajoutant « / » comme option de ligne de commande. Dans ce cas le mappage du lecteur échec.

#### <a name="solution"></a>Solution

Vous pouvez utiliser une des opérations suivantes pour contourner le problème :

• Utilisez la commande PowerShell suivante :

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

À partir d’un fichier de commandes pour ce faire en tant que

`Echo new-smbMapping ... | powershell -command –`

• Mis entre guillemets autour de la clé pour contourner ce problème, à moins que « / » est le premier caractère. S’il s’agit, utilisez le mode interactif et entrez votre mot de passe séparément ou régénérer vos clés pour obtenir une clé qui ne commence pas par le caractère barre oblique (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Mon application/service n’est pas accessible lecteurs fichiers Azure

#### <a name="cause"></a>Cause

Lecteurs sont monter par utilisateur. Si votre application ou service est en cours d’exécution sous un autre compte utilisateur, les utilisateurs ne voyez pas le lecteur.

#### <a name="solution"></a>Solution

Monter le lecteur du même compte d’utilisateur sous lequel l’application est. Cela peut être effectuée à l’aide d’outils tels que psexec.

Par ailleurs, vous pouvez créer un nouvel utilisateur qui a les mêmes privilèges que le compte de service ou un système de réseau et puis exécutez **cmdkey** et l' **Utilisation nette** sous ce compte. Le nom d’utilisateur doit correspondre au nom de compte de stockage, et votre mot de passe doit être la clé de compte de stockage. Une autre option **net** Use consiste à passer dans le nom de compte de stockage et la clé dans les paramètres de nom et mot de passe utilisateur de la commande **net use** .

Après avoir suivi ces instructions, vous pouvez recevoir le message d’erreur suivant : « erreur système 1312 s’est produite. Une session spécifiée n’existe pas. Il peut déjà terminée » lorsque vous exécutez **utiliser net** pour le compte de service système/réseau. Dans ce cas, assurez-vous que le nom d’utilisateur qui est passé à **utiliser net** comprend des informations sur le domaine (par exemple : « [nom du compte de stockage]. file.core.windows .net »).

## <a name="linux-client-problems"></a>Problèmes de client Linux

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erreur « Vous effectuez la copie un fichier vers une destination qui ne prend pas en charge le chiffrement »

#### <a name="cause"></a>Cause

Les fichiers chiffrés BitLocker peuvent être copiées aux fichiers Azure. Toutefois, le stockage de fichiers ne prend pas en charge NTFS EFS. Par conséquent, vous êtes probablement utiliser EFS dans ce cas. Si vous avez des fichiers qui sont chiffrées par EFS, une opération de copie pour le stockage de fichiers peut échouer, sauf si la commande Copier est déchiffrement d’un fichier copié.

#### <a name="workaround"></a>Solution de contournement

Pour copier un fichier sur le stockage de fichiers, vous devez tout d’abord déchiffrer il. Vous pouvez effectuer ceci à l’aide d’une des méthodes suivantes :

• Utilisez **/d copie**.

• Définir la clé de Registre suivante :

- Chemin d’accès = HKLM\Software\Policies\Microsoft\Windows\System
- Type de valeur = DWORD
- Nom = CopyFileAllowDecryptedRemoteDestination
- Valeur = 1

Toutefois, notez que la définition de la clé de Registre affecte toutes les opérations de copier vers des partages réseau.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Partage de l’erreur « Hôte est vers le bas » sur un fichier existant, ou le shell se bloque lorsque vous exécutez des commandes de la liste sur le point de montage

#### <a name="cause"></a>Cause

Cette erreur se produit sur le client Linux lorsque le client est inactif depuis un certain temps. Lorsque cette erreur se produit, le client est déconnecté, et la connexion du client arrive à expiration.

#### <a name="solution"></a>Solution

Ce problème est résolu maintenant dans le noyau Linux dans le cadre du [jeu de modifications](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), en attente backport dans distribution Linux.

Pour works ce problème, maintenir la connexion et évitez d’entrer dans un état d’inactivité, conserver un fichier dans le partage de fichiers Azure que vous écrivez régulièrement. Ceci est une opération d’écriture, telles que la date de création/modification de réécriture sur le fichier. Dans le cas contraire, vous pouvez obtenir des résultats mis en cache et l’opération ne peut pas déclencher la connexion.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>« Erreur 115 de montage » lorsque vous essayez de monter des fichiers Azure sur la VM Linux

#### <a name="cause"></a>Cause

Éditions Linux ne pas encore prennent en charge la fonctionnalité de chiffrement PME 3.0. Dans certaines répartitions utilisateur peut s’afficher un message d’erreur « 115 » s’ils essaient d’Azure montage fichiers à l’aide de PME 3.0 en raison d’une fonctionnalité manquante.

#### <a name="solution"></a>Solution

Si le client Linux SMB utilisé ne reconnaît pas de chiffrement, Azure montage des fichiers à l’aide de PME 2.1 à partir d’un Linux VM dans le même centre de données en tant que le compte de stockage de fichier.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>Linux VM rencontre aléatoires délais commandes comme « ls »

#### <a name="cause"></a>Cause

Cela peut se produire lorsque la commande de montage n’inclut pas l’option **serverino** . Sans **serverino**, la commande ls s’exécute un **jours fériés** sur tous les fichiers.

#### <a name="solution"></a>Solution

Vérifier la **serverino** dans votre entrée « / etc/fstab » :

azureuser.file.Core.Windows.NET/WMS/comer sur/Accueil/sampledir type cifs (rw, nodev, relatime, vers = 2.1, s = ntlmssp, cache = strict, nom d’utilisateur = xxx, domaine = X, file_mode = 0755, dir_mode = 0755, serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Si l’option **serverino** n’est pas présente, décharger et Azure montage des fichiers à nouveau en demandant l’option **serverino** est sélectionnée.

## <a name="learn-more"></a>Pour en savoir plus

- [Prise en main le stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md)

- [Prise en main le stockage de fichiers Azure sous Linux](storage-how-to-use-files-linux.md)

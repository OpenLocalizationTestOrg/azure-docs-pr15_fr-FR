<properties 
   pageTitle="Résoudre les problèmes de déploiement StorSimple | Microsoft Azure"
   description="Décrit comment diagnostiquer et réparer les erreurs qui se produisent lorsque vous déployez tout d’abord StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Résoudre les problèmes de déploiement de périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des conseils de dépannage pour votre déploiement Microsoft Azure StorSimple. Il décrit les problèmes courants, les causes possibles et étapes recommandées pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous configurez StorSimple. Ces informations s’appliquent à la fois le périphérique de physique StorSimple en local et le périphérique virtuel StorSimple.

> [AZURE.NOTE] Problèmes liés à la configuration de périphériques que vous pouvez rencontrer peuvent se produire lorsque vous déployez le périphérique pour la première fois ou qu’ils peuvent se produire plus tard, lorsque l’appareil est opérationnel. Cet article se concentre sur la résolution des problèmes de déploiement de la première fois. Pour résoudre les problèmes d’un périphérique opérationnel, accédez à [résoudre des problèmes de périphérique opérationnel](storsimple-troubleshoot-operational-device.md).

Cet article décrit les outils de résolution des déploiements StorSimple également et fournit un exemple de dépannage étape par étape.

## <a name="first-time-deployment-issues"></a>Problèmes de déploiement de la première fois

Si vous rencontrez un problème lorsque vous déployez votre appareil pour la première fois, procédez comme suit :

- Si vous rencontrez un périphérique physique, assurez-vous que le matériel a été installé et configuré comme décrit dans [installer votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).
- Vérifier les conditions préalables pour le déploiement. Vérifiez que vous avez toutes les informations décrites dans [liste de vérification de configuration du déploiement](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Passez en revue les Notes de publication StorSimple pour voir si le problème est décrite. Les notes de publication incluent des solutions de contournement pour des problèmes d’installation connus. 

Pendant le déploiement de l’appareil, les plus courants problèmes que les utilisateurs face se produire quand ils exécutent l’Assistant Configuration et quand ils inscrivent l’appareil par le biais de Windows PowerShell pour StorSimple. (Vous utilisez Windows PowerShell pour StorSimple pour inscrire et configurer votre appareil StorSimple. Pour plus d’informations sur l’inscription d’appareil, voir [étape 3 : configurer et inscrire votre appareil à utiliser Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Les sections suivantes peuvent vous aider à résoudre les problèmes que vous rencontrez lorsque vous configurez le périphérique StorSimple pour la première fois.

## <a name="first-time-setup-wizard-process"></a>Processus de l’Assistant Configuration de la première fois

Les étapes suivantes résument le processus de l’Assistant Configuration. Pour plus d’informations d’installation détaillées, voir [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

1. Exécutez l’applet de commande [Appeler HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) pour démarrer l’Assistant Configuration vous guide dans les étapes restantes. 
2. Configurer le réseau : l’Assistant Configuration vous permet de configurer les paramètres de réseau pour l’interface de 0 réseau de données sur votre appareil StorSimple. Ces paramètres sont les suivants :
  - Virtual IP (VIP), masque et la passerelle – l’applet de commande [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) est exécutée en arrière-plan. Il configure l’adresse IP, masque et passerelle pour l’interface de 0 réseau de données sur votre appareil StorSimple.
  - Serveur DNS principal – l’applet de commande [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) est exécutée en arrière-plan. Il configure les paramètres DNS pour votre solution StorSimple.
  - Serveur NTP – l’applet de commande [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) est exécutée en arrière-plan. Il configure les paramètres du serveur NTP pour votre solution StorSimple.
  - Proxy web facultatif – l’applet de commande [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) est exécutée en arrière-plan. Il définit et Active la configuration du proxy web pour votre solution StorSimple.
3. Configurer les mots de passe : l’étape suivante consiste à configurer administrateur de l’appareil et les mots de passe Gestionnaire d’instantanés StorSimple. Si vous exécutez la mise à jour 1, puis vous pas devront définir le mot de passe Gestionnaire d’instantanés StorSimple.
  - Le mot de passe administrateur appareil est utilisé pour ouvrir une session sur votre appareil. Le mot de passe de périphérique par défaut est **le mot de passe1**.
  - Le mot de passe Gestionnaire d’instantanés StorSimple est requis lorsque vous configurez un périphérique à utiliser le Gestionnaire d’instantanés StorSimple. Vous devez commencer par définir le mot de passe dans l’Assistant Configuration, et puis vous pouvez définir et modifier à partir du service Manager StorSimple. Ce mot de passe authentifie le périphérique avec le Gestionnaire de capture instantanée StorSimple.
 
    > [AZURE.IMPORTANT] Les mots de passe sont collectées avant l’enregistrement, mais appliquent uniquement après que vous inscrire avec succès l’appareil. S’il existe une défaillance pour appliquer un mot de passe, vous devrez fournir à nouveau le mot de passe jusqu'à ce que les mots de passe requis (répondant aux exigences de complexité) sont collectées.

4. Inscrire l’appareil : la dernière étape consiste à enregistrer le périphérique avec le service Manager StorSimple en cours d’exécution dans Microsoft Azure. L’enregistrement, vous devez [obtenir la clé d’inscription de service](storsimple-manage-service.md#get-the-service-registration-key) à partir du portail classique Azure et fournissez dans l’Assistant Configuration. Une fois que l’appareil est inscrit correctement, une clé de chiffrement des données de service est fournie pour vous. Veillez à conserver cette clé de chiffrement dans un emplacement sécurisé parce qu’il sera nécessaire pour inscrire tous les appareils suivants auprès du service.

## <a name="common-errors-during-device-deployment"></a>Erreurs courantes lors du déploiement de l’appareil

Les tableaux suivants répertorient les erreurs courantes que vous pouvez rencontrer lorsque vous :

- Configurer les paramètres réseau requis.
- Configurer les paramètres de proxy web facultatif.
- Configurer l’administrateur de l’appareil et les mots de passe Gestionnaire d’instantanés StorSimple. 
- Inscrire l’appareil. 

## <a name="errors-during-the-required-network-settings"></a>Erreurs lors des paramètres réseau requis

| Non.| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1  | Appeler-HcsSetupWizard : Cette commande peut uniquement être exécutée sur le contrôleur actif. | Configuration a été effectuée sur le contrôleur passif.| Exécutez cette commande à partir du contrôleur actif. Pour plus d’informations, voir [identifier un contrôleur active sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Appeler-HcsSetupWizard : Appareil n’est pas prêt. | Il existe des problèmes avec la connectivité réseau sur des données 0.| Vérifiez la connectivité réseau physique sur des données 0.|
| 3 | Appeler-HcsSetupWizard : Il existe un conflit d’adresses IP avec un autre système sur le réseau (Exception à partir de HRESULT : 0x80070263). | L’adresse IP fourni pour les données 0 était déjà en cours d’utilisation par un autre système. | Fournir une nouvelle adresse IP qui n’est pas en cours d’utilisation.|
| 4 | Appeler-HcsSetupWizard : Une ressource de cluster a échoué. (Exception à partir de HRESULT : 0x800713AE). | VIP en double. L’adresse IP fourni est déjà en cours d’utilisation.| Fournir une nouvelle adresse IP qui n’est pas en cours d’utilisation.|
| 5 | Appeler-HcsSetupWizard : L’adresse IPv4 non valides. | L’adresse IP est fourni dans un format incorrect.| Vérifiez le format et fournir à nouveau votre adresse IP. Pour plus d’informations, voir [Adressage Ipv4][1]. |
| 6 | Appeler-HcsSetupWizard : L’adresse IPv6 non valide. | L’adresse IP est fourni dans un format incorrect.| Vérifiez le format et fournir à nouveau votre adresse IP. Pour plus d’informations, voir [Adressage Ipv6][2].|
| 7 | Appeler-HcsSetupWizard : Aucun point de terminaison supplémentaires ne sont disponibles sur l’endpoint mapper. (Exception à partir de HRESULT : 0x800706D9) | La fonctionnalité de cluster ne fonctionne pas. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir la procédure suivante.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erreurs lors de le les paramètres de proxy web facultatif

| Non.| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1  | HcsSetupWizard appeler : Paramètre non valide (Exception à partir de HRESULT : 0 x 80070057) | L’un des paramètres fournis pour les paramètres de proxy n’est pas valide.| L’URI n’est fournie dans le format correct. Utilisez le format suivant : http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | HcsSetupWizard appeler : Serveur RPC n’est pas disponible (Exception à partir de HRESULT : 0x800706ba) | La cause est une des opérations suivantes :<ol><li>Le cluster n’est pas jusqu'à.</li><li>Le contrôleur passif Impossible de communiquer avec le contrôleur actif, puis exécutez la commande à partir de contrôleur passif.</li></ol> | Selon la cause :<ol><li>[Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous assurer que le cluster est disponible.</li><li>Exécutez la commande à partir du contrôleur actif. Si vous souhaitez exécuter la commande à partir du contrôleur passif, vous devez vous assurer que le contrôleur passif peut communiquer avec le contrôleur actif. Vous devrez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) si cette connectivité est interrompue.</li></ol> |
| 3 | Appeler-HcsSetupWizard : Appel RPC a échoué (Exception à partir de HRESULT : 0x800706be) | Cluster ne fonctionne pas. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous assurer que le cluster est disponible.|
| 4 | Appeler-HcsSetupWizard : Cluster ressource introuvable (Exception à partir de HRESULT : 0x8007138f) | La ressource de cluster n’est pas trouvée. Cela peut se produire lorsque l’installation n’a pas correcte. | Vous devrez peut-être réinitialiser l’appareil les paramètres par défaut. [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour créer une ressource de cluster.|
| 5 | Appeler-HcsSetupWizard : Cluster ressource pas en ligne (Exception à partir de HRESULT : 0x8007138c)| Ressources de cluster n’êtes pas connectés. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir la procédure suivante.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Erreurs liées à l’administrateur de l’appareil et mots de passe StorSimple instantané Manager

Le mot de passe administrateur de périphérique par défaut est **le mot de passe1**. Ce mot de passe expire après la première ouverture de session ; Par conséquent, vous devrez utiliser l’Assistant Configuration pour le modifier. Vous devez fournir un nouveau mot de passe administrateur appareil lorsque vous enregistrez le périphérique pour la première fois. 

Si vous utilisez le logiciel de gestionnaire d’instantanés StorSimple en cours d’exécution sur l’hôte de Windows Server pour gérer le périphérique, vous devez également fournir un mot de passe Gestionnaire d’instantanés StorSimple lors de l’inscription première. 

Assurez-vous que vos mots de passe remplissent les conditions suivantes :

- Le mot de passe administrateur doit être comprise entre 8 et 15 caractères.
- Votre mot de passe Gestionnaire d’instantanés StorSimple doit comporter 14 ou 15 caractères.
- Les mots de passe doivent contenir 3 des types de 4 caractères suivants : minuscules, majuscules, numériques et spéciaux. 
- Votre mot de passe ne peut pas être identique aux dernière 24 mots de passe.

En outre, n’oubliez pas que les mots de passe arrive à expiration chaque année et peuvent être modifiées qu’après que vous inscrire avec succès l’appareil. Si l’inscription échoue pour une raison quelconque, les mots de passe ne seront pas être modifiés. Pour plus d’informations sur l’administrateur de l’appareil et mots de passe StorSimple instantané Manager, accédez à [utiliser le service Manager StorSimple pour modifier votre mot de passe StorSimple](storsimple-change-passwords.md).

Vous pouvez rencontrer une ou plusieurs des messages d’erreur suivants lors de la configuration de l’administrateur de l’appareil et les mots de passe Gestionnaire d’instantanés StorSimple.

| Non.| Message d’erreur | Action recommandée |
| ---| ------------- | ------------------ | 
| 1  | Le mot de passe dépasse la longueur maximale. |Utiliser un mot de passe qui répond aux conditions suivantes :<ul><li>Le mot de passe administrateur doit être comprise entre 8 et 15 caractères.</li><li>Votre mot de passe Gestionnaire d’instantanés StorSimple doit contenir 14 ou 15 caractères.</li></ul> | 
| 2 | Le mot de passe ne répond pas à la longueur souhaitée. | Utiliser un mot de passe qui répond aux conditions suivantes :<ul><li>Le mot de passe administrateur doit être comprise entre 8 et 15 caractères.</li><li>Votre mot de passe Gestionnaire d’instantanés StorSimple doit contenir 14 ou 15 caractères.</lu></ul> |
| 3 | Le mot de passe doit contenir des caractères en minuscules. | Les mots de passe doivent contenir 3 des types de 4 caractères suivants : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 4 | Le mot de passe doit contenir des caractères numériques. | Les mots de passe doivent contenir 3 des types de 4 caractères suivants : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 5 | Le mot de passe doit contenir des caractères spéciaux. | Les mots de passe doivent contenir 3 des types de 4 caractères suivants : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 6 | Le mot de passe doit contenir 3 des types de 4 caractères suivants : majuscules, minuscules, numériques et spéciaux. | Votre mot de passe ne contient pas les types de caractères requis. Assurez-vous que votre mot de passe répond à ces exigences. |
| 7 | Paramètre ne correspond pas à la confirmation. | Assurez-vous que votre mot de passe répond à toutes les conditions requises et que vous l’avez entrée correctement. |
| 8 | Votre mot de passe ne peut pas correspondre à la valeur par défaut. | Le mot de passe par défaut est *le mot de passe1*. Vous devez modifier ce mot de passe une fois que vous vous connectez pour la première fois. |
| 9 | Le mot de passe que vous avez entré ne correspond pas le mot de passe. Retapez le mot de passe. | Vérifier le mot de passe, puis tapez-le à nouveau. |

Les mots de passe sont collectées avant de l’appareil est enregistré, mais s’appliquent uniquement après l’enregistrement est effectué. Le flux de travail de récupération de mot de passe nécessite le périphérique pour être enregistré. 

> [AZURE.IMPORTANT] En règle générale, si l’échec d’une tentative d’appliquer un mot de passe, puis le logiciel à plusieurs reprises tente de récupérer le mot de passe jusqu'à ce qu’il a réussi. Parfois, le mot de passe ne peut pas être appliqué. Dans ce cas, vous pouvez inscrire l’appareil et continuer, toutefois les mots de passe ne seront pas modifiées. Vous ne recevrez pas d’indication qui n’a pas changé votre mot de passe, le mot de passe administrateur appareil ou gestionnaire d’instantanés StorSimple. Si cette situation se produit, nous vous recommandons de modifier les deux mots de passe.

Vous pouvez réinitialiser les mots de passe dans le portail Azure classique via le service Manager StorSimple. Pour plus d’informations, consultez : 

- [Modifier le mot de passe administrateur](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Modifier le mot de passe Gestionnaire d’instantanés StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Erreurs lors de l’inscription de l’appareil

Le service du Gestionnaire de StorSimple en cours d’exécution dans Microsoft Azure vous permet d’enregistrer le périphérique. Vous pouvez rencontrer une ou plusieurs des problèmes suivants lors de l’inscription d’appareil.

| Non.| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1  | Erreur 350027 : Impossible d’enregistrer le périphérique avec le Gestionnaire de StorSimple. |  | Patientez quelques minutes et puis recommencez l’opération. Si le problème persiste, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).|
| 2  | Erreur 350013 : Une erreur s’est produite lors de l’inscription de l’appareil. Cela peut être dû à clé d’inscription service incorrect. | | Inscrivez-vous à nouveau l’appareil avec la clé d’inscription de service correct. Pour plus d’informations, voir [obtenir la clé d’inscription du service.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Erreur 350063 : Authentification service Manager StorSimple passé, mais l’enregistrement a échoué. Recommencez l’opération après un certain temps. | Cette erreur indique que l’authentification avec ACS a réussi, mais l’appel de Registre apportée au service a échoué. Cela peut être un résultat d’un problème réseau interruptions. | Si le problème persiste, contactez le [Support technique Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Erreur 350049 : Le service n’a pas pu être atteint lors de l’enregistrement. | Lorsque l’appel est effectué au service, une exception web correctement après réception. Dans certains cas, cela peut obtenir fixé avec une nouvelle tentative de l’opération plus tard. | Veuillez vérifier votre adresse IP et le nom DNS, puis relancez l’opération. Si le problème persiste, [contacter le Support Microsoft.](storsimple-contact-microsoft-support.md) | 
| 5 | Erreur 350031 : Le périphérique a déjà été enregistré. | | Aucune action n’est nécessaire. |
| 6 | Erreur 350016 : Échec de l’inscription de l’appareil. | |Vérifiez que la clé d’inscription est correcte. |
| 7 | Appeler-HcsSetupWizard : Une erreur s’est produite lors de l’inscription de votre appareil ; Cela peut être dû à l’adresse IP incorrecte ou le nom DNS. Vérifiez vos paramètres réseau, puis réessayez. Si le problème persiste, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md). (Erreur 350050) | Assurez-vous que votre appareil peut ping du réseau externe. Si vous n’avez pas de connectivité réseau externe, l’inscription peut échouer avec l’erreur suivante. Cette erreur peut être une combinaison d’une ou plusieurs des opérations suivantes :<ul><li>IP incorrecte</li><li>Sous-réseau incorrect</li><li>Passerelle incorrecte</li><li>Paramètres DNS incorrects</li></ul> | Consultez les étapes décrites dans l' [exemple de dépannage étape par étape](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Appeler-HcsSetupWizard : Échec de l’opération en cours en raison d’une erreur interne du service [0x1FBE2]. Recommencez l’opération après un certain temps. Si le problème persiste, contactez le Support Microsoft. | Il s’agit d’une erreur générique levée pour toutes les erreurs invisibles utilisateur de service ou de l’agent. Le plus souvent peut-être que l’authentification ACS a échoué. Il est possible de l’échec qu’il existe des problèmes avec la configuration du serveur NTP et heure sur l’appareil n’est pas correctement réglée. | Corriger l’heure (s’il existe des problèmes), puis réessayez l’opération d’enregistrement. Si vous utilisez la commande Set-HcsSystem - fuseau horaire pour régler le fuseau horaire, majuscule en début de chaque mot dans le fuseau horaire (par exemple « Pacifique »).  Si ce problème persiste, [contactez le Support Microsoft](storsimple-contact-microsoft-support.md) pour étapes suivantes. |
| 9 | Avertissement : Impossible d’activer le périphérique. Votre administrateur de l’appareil et les mots de passe Gestionnaire d’instantanés StorSimple n’ont pas été modifiés. | Si l’inscription échoue, l’administrateur de l’appareil et les mots de passe Gestionnaire d’instantanés StorSimple ne sont pas modifiées. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Outils de résolution des déploiements StorSimple

StorSimple comprend plusieurs outils que vous pouvez utiliser pour résoudre les problèmes de votre solution StorSimple. Parmi lesquels :

- Prise en charge des packages et des journaux de périphérique 
- Applets de commande conçues spécialement pour la résolution des problèmes 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Prise en charge de packages et appareil journaux disponibles pour la résolution des problèmes

Un package de prise en charge contient tous les journaux pertinents qui peuvent aider l’équipe de Support Microsoft à résoudre les problèmes d’appareil. Vous pouvez utiliser Windows PowerShell pour StorSimple pour générer un package de prise en charge chiffré que vous pouvez partager puis avec service de support technique.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Pour afficher les journaux ou le contenu du package de prise en charge

1. Utiliser Windows PowerShell pour StorSimple pour générer un package de prise en charge, comme décrit dans [créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md).

2. Téléchargez le [script déchiffrement](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur votre ordinateur client.

3. Utilisez cette [procédure pas à pas](storsimple-create-manage-support-package.md#edit-a-support-package) pour ouvrir et déchiffrer le package de prise en charge.

4. Les journaux de package déchiffré prise en charge sont disponibles au format etw/etvx. Vous pouvez effectuer les étapes suivantes pour afficher ces fichiers dans l’Observateur d’événements Windows :
  1. Exécutez la commande **eventvwr** sur votre client Windows. Démarre l’Observateur d’événements.
  2. Dans le volet **Actions** , cliquez sur **Ouvrir le journal enregistré** , puis pointez sur les fichiers journaux au format etvx/etw (le package de prise en charge). Vous pouvez à présent afficher le fichier. Une fois que vous ouvrez le fichier, vous pouvez avec le bouton droit et enregistrer le fichier en tant que texte.
   
    > [AZURE.IMPORTANT] Vous pouvez également utiliser l’applet de commande **Get-intercepteur** pour ouvrir ces fichiers dans Windows PowerShell. Pour plus d’informations, voir [Get-intercepteur](https://technet.microsoft.com/library/hh849682.aspx) dans la documentation de référence applet de commande Windows PowerShell.

5. Lorsque les journaux ouvrez dans l’Observateur d’événements, recherchez les journaux suivants qui contiennent des problèmes liés à la configuration du périphérique :

  - Journal de hcs_pfconfig/opérationnel
  - hcs_pfconfig/Config

6. Dans les fichiers journaux, recherchez chaînes liées aux applets de commande appelée par l’Assistant de configuration. Pour obtenir une liste de ces applets de commande, voir [première processus de l’Assistant Configuration](#first-time-setup-wizard-process) . 

7. Si vous n’êtes pas en mesure d’identifier la cause du problème, vous pouvez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir la procédure suivante. Suivez les étapes dans [créer une demande de support](storsimple-contact-microsoft-support.md#create-a-support-request) lorsque vous contactez le Support Microsoft pour obtenir une assistance.

## <a name="cmdlets-available-for-troubleshooting"></a>Applets de commande disponible pour le dépannage

Utiliser les applets de commande Windows PowerShell pour détecter les erreurs de connexion.

- `Get-NetAdapter`: Utilisez cette applet de commande pour détecter l’état des interfaces réseau. 

- `Test-Connection`: Utilisez cette applet de commande pour vérifier la connectivité réseau à l’intérieur ou en dehors du réseau.

- `Test-HcsmConnection`: Utilisez cette applet de commande pour vérifier la connectivité d’un périphérique enregistré.

Si vous exécutez la mise à jour 1 sur votre appareil StorSimple, les applets de commande de diagnostics suivants sont également disponibles.

- `Sync-HcsTime`: Utilisez cette applet de commande pour afficher l’heure de l’appareil et forcer une synchronisation avec le serveur NTP.

- `Enable-HcsPing`et `Disable-HcsPing`: ces applets de commande permet d’autoriser les hôtes ping sur les interfaces réseau sur votre appareil StorSimple. Par défaut, les interfaces réseau StorSimple ne répondent pas aux requêtes ping.

- `Trace-HcsRoute`: Utilisez cette applet de commande comme un outil de suivi d’itinéraire. Il vous envoie des paquets à chaque routeur sur la façon d’une destination finale sur une période donnée et puis calcule les résultats en fonction des paquets renvoyés à partir de chaque saut. Dans la mesure où `Trace-HcsRoute` indique le degré de perte de paquets sur un routeur donné ou un lien, vous pouvez identifier les routeurs ou liens peuvent être à l’origine des problèmes de réseau. 

- `Get-HcsRoutingTable`: Utilisez cette applet de commande pour afficher la table de routage IP locale.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Get-NetAdapter

Lorsque vous configurez les interfaces réseau pour un déploiement de la première fois, l’état du matériel n’est pas disponible dans le service du Gestionnaire de StorSimple l’interface utilisateur, car l’appareil n’est pas encore inscrit auprès du service. En outre, la page État du matériel reflète ne peut-être pas toujours correctement l’état de l’appareil, en particulier si des problèmes qui affectent la synchronisation du service. Dans ce cas, vous pouvez utiliser la `Get-NetAdapter` applet de commande pour déterminer la santé et l’état de vos interfaces réseau.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Pour afficher la liste de toutes les cartes réseau sur votre appareil

1. Démarrez Windows PowerShell pour StorSimple et tapez `Get-NetAdapter`. 

2. Utilisez le résultat de la `Get-NetAdapter` applet de commande et les instructions suivantes pour comprendre l’état de votre interface réseau.
  - Si l’interface est correcte et activé, l’état **ifIndex** apparaît sous la forme **vers le haut**.
  - Si l’interface est correcte mais n’est pas connecté (par un câble réseau), **ifIndex** est affiché comme **désactivé**.
  - Si l’interface est correcte mais non activé, l’état **ifIndex** apparaît sous la forme **NotPresent**.
  - Si l’interface n’existe pas, il n’apparaît pas dans cette liste. Le service du Gestionnaire de StorSimple l’interface utilisateur s’affichent toujours cette interface dans un état a échoué.

Pour plus d’informations sur l’utilisation de cette applet de commande, accédez à [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) dans la référence applet de commande Windows PowerShell. 

Les sections suivantes présentent des exemples de sortie de la `Get-NetAdapter` applet de commande. 

 Dans ces exemples, contrôleur 0 était le contrôleur passif et a été configurée comme suit :

- DONNÉES 0, données 1, données 2 et 3 de données réseau interfaces existant sur l’appareil.
- DONNÉES 4 et 5 données cartes d’interface n’étaient pas présents ; Par conséquent, ils ne figurent pas dans le résultat.
- DONNÉES 0 a été activées.

Contrôleur 1 était le contrôleur actif et a été configurée comme suit :

- DONNÉES 0, données 1, données 2, données 3, 4 de données et réseau de données 5 interfaces existant sur l’appareil.
- DONNÉES 0 a été activées.

**Sortie d’échantillon – contrôleur de 0**

Voici le résultat de contrôleur de 0 (le contrôleur passif). DONNÉES 1, données 2 et 3 de données ne sont pas connectés. DONNÉES 4 et 5 de données ne sont pas répertoriées, car elles ne figurent pas sur l’appareil. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Sortie d’échantillon – contrôleur de 1**

Voici le résultat de contrôleur de 1 (le contrôleur actif). Uniquement les données 0 interface réseau sur l’appareil est configuré et opérationnel.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Résoudre les problèmes avec l’applet de commande tester la connexion

Vous pouvez utiliser la `Test-Connection` applet de commande pour déterminer si votre appareil StorSimple peut se connecter au réseau externe. Si tous les paramètres de mise en réseau, y compris le DNS sont correctement configurés dans l’Assistant de configuration, vous pouvez utiliser la `Test-Connection` applet de commande ping sur une adresse connue en dehors du réseau, tels que outlook.com. 

Vous devez activer la commande ping résoudre les problèmes de connectivité avec cette applet de commande si le test ping est désactivé.

Consultez les exemples de sortie de la `Test-Connection` applet de commande. 

> [AZURE.NOTE] Dans le premier exemple, l’appareil est configuré avec un serveur DNS incorrect. Dans le second exemple, le DNS est correct.
 
**Sortie d’échantillon – DNS incorrectes**

Dans l’exemple suivant, il n’existe pas de sortie pour les adresses IPV4 et IPV6, qui indique que le DNS n’est pas résolu. Cela signifie qu’il n’existe pas de connectivité au réseau externe et un DNS correct doit être fourni. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exemple de résultat : corriger DNS**

Dans l’exemple suivant, le DNS renvoie l’adresse IPV4, indiquant que le système DNS est correctement configuré. Cela permet de confirmer que la connectivité au réseau externe est. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Test-HcsmConnection

Utiliser la `Test-HcsmConnection` applet de commande pour un appareil qui est déjà connecté et enregistré avec votre service Manager StorSimple. Cette applet de commande permet de vérifier la connectivité entre un périphérique enregistré et le service Manager StorSimple correspondant. Vous pouvez exécuter cette commande sur Windows PowerShell pour StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Pour exécuter l’applet de commande Test-HcsmConnection

1. Assurez-vous que l’appareil est inscrit.

2. Vérifier l’état de l’appareil. Si le périphérique est désactivé, en mode maintenance ou en mode hors connexion, vous verrez peut-être les erreurs suivantes : 

   - ErrorCode.CiSDeviceDecommissioned – cela indique que le périphérique est désactivé.
   - ErrorCode.DeviceNotReady – cela indique que l’appareil est en mode maintenance.
   - ErrorCode.DeviceNotReady – cela indique que l’appareil n’est pas en ligne.

3. Vérifiez que le service StorSimple Manager s’exécute (utiliser l’applet de commande [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Si le service n’est pas en cours d’exécution, vous verrez peut-être les erreurs suivantes :

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – cela indique qu’il y a une exception lorsque vous avez exécuté Get-ClusterResource.

4. Vérifier le jeton de Service de contrôle d’accès (ACS). Si une exception est web, il peut être le résultat d’un problème de passerelle, une authentification du proxy manquant, un serveur DNS incorrect ou un échec d’authentification. Messages d’erreur suivants peut s’afficher :

   - ErrorCode.CiSApplianceGateway – cela indique une exception HttpStatusCode.BadGateway : le service de résolution du nom n’a pas pu résoudre le nom d’hôte. 
   - ErrorCode.CiSApplianceProxy – cela indique une exception HttpStatusCode.ProxyAuthenticationRequired (code d’état HTTP 407) : le client ne peut pas s’authentifier avec le serveur proxy. 
   - ErrorCode.CiSApplianceDNSError – cela indique une exception WebExceptionStatus.NameResolutionFailure : le service de résolution du nom n’a pas pu résoudre le nom d’hôte.
   - ErrorCode.CiSApplianceACSError – cela indique que le service a renvoyé une erreur d’authentification, mais il existe une connectivité.
   
    Si elle ne lève pas une exception web, recherchez ErrorCode.CiSApplianceFailure. Cela indique que l’application a échoué.

5. Vérifiez la connectivité de service cloud. Si le service lève une exception web, vous verrez peut-être les erreurs suivantes :

  - ErrorCode.CiSApplianceGateway – cela indique une exception HttpStatusCode.BadGateway : un serveur proxy intermédiaire a reçu une demande incorrecte à partir d’un autre proxy ou du serveur d’origine.
  - ErrorCode.CiSApplianceProxy – cela indique une exception HttpStatusCode.ProxyAuthenticationRequired (code d’état HTTP 407) : le client ne peut pas s’authentifier avec le serveur proxy. 
  - ErrorCode.CiSApplianceDNSError – cela indique une exception WebExceptionStatus.NameResolutionFailure : le service de résolution du nom n’a pas pu résoudre le nom d’hôte.
  - ErrorCode.CiSApplianceACSError – cela indique que le service a renvoyé une erreur d’authentification, mais il existe une connectivité.
  
    Si elle ne lève pas une exception web, recherchez ErrorCode.CiSApplianceSaasServiceError. Cela indique un problème avec le service Manager StorSimple.
 
6. Vérifiez la connectivité Bus des services Azure. ErrorCode.CiSApplianceServiceBusError indique que le périphérique ne peut pas se connecter au Service Bus.
 
Les fichiers journaux CiSCommandletLog0Curr.errlog et CiSAgentsvc0Curr.errlog ont plus d’informations, telles que les détails des exceptions. 

Consultez la documentation pour plus d’informations sur l’utilisation de l’applet de commande, accédez au [Test HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) dans Windows PowerShell.

> [AZURE.IMPORTANT] Vous pouvez exécuter cette applet de commande pour l’actif et le contrôleur passif. 
 
Consultez les exemples de sortie de la `Test-HcsmConnection` applet de commande. 

**Exemple de résultat – appareil correctement inscrit qui exécutent la version StorSimple (juillet 2014)**

Le premier exemple est d’un appareil est inscrit correctement avec le service Manager StorSimple et ne dont l’aucun problème de connectivité. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Exemple de sortie – périphérique correctement inscrit qui exécute StorSimple mise à jour 1**

Si vous exécutez la mise à jour 1 sur votre appareil StorSimple, vous n’aurez pas à exécuter avec le commutateur détaillé.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Exemple de résultat – en mode hors connexion appareil exécute StorSimple version (juillet 2014)**

Cet exemple est d’un appareil qui comporte un état **hors connexion** dans le portail classique Azure.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

Le périphérique pas pu se connecter à l’aide de la configuration du proxy web actuel. Cela peut être un problème avec la configuration du proxy web ou un problème de connectivité réseau. Dans ce cas, vous devez vous assurer que vos paramètres de proxy web sont corrects et vos serveurs proxy web sont en ligne et accessibles. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Résoudre les problèmes avec l’applet de commande de synchronisation HcsTime

Utilisez cette applet de commande pour afficher l’heure de l’appareil. Si l’heure du périphérique comporte un décalage avec le serveur NTP, vous pouvez ensuite utiliser cette applet de commande pour synchroniser l’heure à force avec votre serveur NTP. Si le décalage entre l’appareil et le serveur NTP est supérieur à 5 minutes, un avertissement s’affiche. Si le décalage est supérieure à 15 minutes, puis accédera l’appareil mobile en mode hors connexion. Vous pouvez toujours utiliser cette applet de commande pour forcer une synchronisation. Toutefois, si le décalage est supérieure à 15 heures, puis vous pas pourrez synchronisé forcer l’heure et un message d’erreur seront affichés.

**Exemple de sortie – synchronisation forcé à l’aide de synchronisation HcsTime**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Résoudre les problèmes avec les applets de commande Enable-HcsPing et désactiver HcsPing

Utilisez ces applets de commande pour vous assurer que les interfaces réseau sur votre appareil répondent aux requêtes ping ICMP. Par défaut, les interfaces réseau StorSimple ne répondent pas aux requêtes ping. À l’aide de cette applet de commande est le moyen le plus simple de savoir si votre appareil est en ligne et accessibles.  

**Exemple de sortie – HcsPing-activer et désactiver HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Trace HcsRoute

Utilisez cette applet de commande comme un outil de suivi d’itinéraire. Il vous envoie des paquets à chaque routeur sur la façon d’une destination finale sur une période donnée et puis calcule les résultats en fonction des paquets renvoyés à partir de chaque saut. Étant donné que l’applet de commande indique le degré de perte de paquets sur n’importe quel routeur ou lien, vous pouvez identifier les routeurs ou liens peuvent être à l’origine des problèmes de réseau.

**Exemple de sortie montrant comment effectuer le suivi de l’itinéraire d’un paquet avec Trace HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Get-HcsRoutingTable

Cette applet de commande permet d’afficher la table de routage pour votre appareil StorSimple. Une table de routage est un ensemble de règles qui peuvent vous aider à déterminer où les paquets de données via un réseau IP (Internet Protocol) seront dirigés. 

Le tableau routage indique les interfaces et la passerelle qui achemine les données vers les réseaux spécifiés. Elle donne également le métrique de routage qui est le fabricant de décision pour le chemin d’accès prise pour atteindre une destination particulière. La plus basse le métrique de routage, plus la préférence. 

Par exemple, si vous avez 2 interfaces réseau, données 2 et 3 de données, connecté à Internet. Si les mesures routage des données 2 et 3 de données sont respectivement 15 et 261, 2 de données avec une métrique routage inférieure est l’interface par défaut utilisée pour accéder à Internet.

Si vous exécutez la mise à jour 1 sur votre appareil StorSimple, l’interface de réseau 0 données comporte la préférence plus élevée pour le trafic de cloud. Cela signifie que même s’il existe d’autres interfaces activé pour le cloud, le trafic cloud serait routé par le biais de données 0. 

Si vous exécutez le `Get-HcsRoutingTable` applet de commande sans spécifier de paramètres (comme le montre l’exemple suivant), l’applet de commande affiche les tables de routage IPv4 et IPv6. Vous pouvez également spécifier `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` pour obtenir une table de routage pertinente.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemple de dépannage StorSimple pas à pas

L’exemple suivant montre étape par étape la résolution des problèmes d’un déploiement StorSimple. Dans l’exemple de scénario, d’inscription pour appareils échoue avec un message d’erreur indiquant que les paramètres de réseau ou le nom DNS est incorrect.

Message d’erreur renvoyé est la suivante :

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

L’erreur peut être provoquée par une des opérations suivantes :

- Installation du matériel incorrect
- Interfaces réseau défectueux
- Adresse IP incorrecte, masque, passerelle, serveur DNS principal ou proxy web
- Clé d’enregistrement incorrect
- Paramètres du pare-feu incorrect

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Pour rechercher et résoudre le problème d’inscription appareil

1. Vérifier la configuration de votre appareil : sur le contrôleur actif, exécutez `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] L’Assistant configuration doit s’exécuter sur le contrôleur actif. Pour vérifier que vous êtes connecté sur le contrôleur actif, examinez la bannière présentée dans la console série. La bannière indique si vous êtes connecté à contrôleur de 0 ou 1, et si le contrôleur est actif ou passif. Pour plus d’informations, accédez à [identifier un contrôleur active sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Vérifiez que l’appareil est correctement câblé : Vérifiez le réseau câblage sur le périphérique de fond de panier. Le câblage est spécifique au modèle d’appareil. Pour plus d’informations, accédez à [installer votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

     > [AZURE.NOTE] Si vous utilisez 10 ports de réseau de solution, vous devrez utiliser les cartes QSFP SFP fournies et câbles SFP. Pour plus d’informations, consultez la [liste des câbles, commutateurs et se recommandé par le fournisseur OEM pour les ports Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Vérifier l’état de l’interface réseau :

   - Utiliser l’applet de commande Get-NetAdapter pour détecter l’état des interfaces réseau pour les données de 0. 
   - Si le lien ne fonctionne pas, l’état **ifindex** indique que l’interface est vers le bas. Vous devrez puis vérifiez la connexion réseau entre le port pour le matériel et le commutateur. Vous devrez également éliminer mauvais câblage. 
   - Si vous pensez que les données 0 port du contrôleur actif a échoué, vous pouvez le confirmer en vous connectant aux données 0 port contrôleur 1. Pour confirmer cela, déconnectez le câble réseau à l’arrière du périphérique de contrôleur 0 et connectez le câble au contrôleur 1 puis exécutez l’applet de commande Get-NetAdapter à nouveau. 
   Si les données 0 port sur un contrôleur d’échoue, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir la procédure suivante. Vous devrez peut-être remplacer le contrôleur sur votre système.
 
4. Vérifiez la connectivité à Microsoft :
   - Assurez-vous que les interfaces de 0 réseau de données sur le contrôleur 0 et 1 contrôleur votre boîtier principal sont sur le même sous-réseau. 
   - Vérifiez le concentrateur ou le routeur. En règle générale, vous devez vous connecter à la fois contrôleurs au même concentrateur ou routeur. 
   - Assurez-vous que les commutateurs que vous utilisez pour la connexion des données 0 pour les deux contrôleurs dans le même réseau local virtuel.
   
5. Éliminer les erreurs de l’utilisateur :

  - Exécuter l’Assistant de configuration (exécuter **Appeler HcsSetupWizard**), puis entrez les valeurs à nouveau pour vous assurer qu’il n’y a aucune erreur. 
  - Vérifiez l’enregistrement clé utilisée. La clé d’inscription même peut être utilisée pour vous connecter plusieurs périphériques à un service Manager StorSimple. Utilisez la procédure dans [obtenir la clé d’inscription de service](storsimple-manage-service.md#get-the-service-registration-key) pour vous assurer que vous utilisez la clé d’inscription correcte.

    > [AZURE.IMPORTANT] Si vous avez plusieurs services en cours d’exécution, vous devez vous assurer que la clé d’inscription pour le service approprié est utilisée pour inscrire l’appareil. Si vous avez enregistré un appareil avec le service Manager StorSimple incorrect, vous devrez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir la procédure suivante. Vous devrez peut-être effectuer une réinitialisation usine du périphérique (qui peut entraîner la perte de données) vers puis se connecter au service prévu.

6. Utiliser l’applet de commande tester la connexion pour vérifier que vous êtes connecté au réseau externe. Pour plus d’informations, accédez à [résoudre les problèmes avec l’applet de commande tester la connexion](#troubleshoot-with-the-test-connection-cmdlet).

7. Vérifiez les interférences pare-feu. Si vous avez vérifié que les paramètres IP (VIP), sous-réseau, la passerelle et DNS virtuels sont corrects, vous voyez toujours des problèmes de connectivité, puis il est possible que votre pare-feu bloque les communications entre votre appareil et du réseau externe. Vous devez vous assurer que les ports 80 et 443 sont disponibles sur votre appareil StorSimple pour les communications sortantes. Pour plus d’informations, voir [Configuration requise pour votre appareil StorSimple de mise en réseau](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Examinez les journaux. Accédez à [packages prise en charge et des journaux d’appareil disponible pour le dépannage](#support-packages-and-device-logs-available-for-troubleshooting).

9. Si les étapes précédentes ne résolvent pas le problème, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance.

## <a name="next-steps"></a>Étapes suivantes
[Découvrez comment résoudre les problèmes d’un périphérique opérationnel](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 

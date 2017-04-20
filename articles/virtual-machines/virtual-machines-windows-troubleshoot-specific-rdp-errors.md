<properties
    pageTitle="Messages d’erreur RDP spécifiques pour les machines virtuelles Azure | Microsoft Azure"
    description="Comprendre les messages d’erreur spécifiques que vous pouvez recevoir lors de l’utilisent connexion Bureau à distance pour une machine virtuelle Windows dans Azure"
    keywords="Erreur du Bureau à distance, erreur de connexion Bureau à distance, ne peut pas se connecter à la machine virtuelle, dépannage de bureau à distance"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Résolution des problèmes de messages d’erreur RDP spécifiques à un ordinateur virtuel Windows Azure
Vous pouvez recevoir un message d’erreur spécifique lors de l’utilisation de connexion Bureau à distance à un Windows machine virtuelle () dans Azure. Cet article décrit certains messages d’erreur courants rencontrés, ainsi que de dépannage pour les résoudre. Si vous rencontrez des problèmes de connexion à votre ordinateur virtuel à l’aide de RDP mais ne pas rencontrerez un message d’erreur spécifique, voir le [guide de dépannage pour le Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md).

Pour plus d’informations sur les messages d’erreur spécifiques, voir les rubriques suivantes :

- [La session distante a été déconnectée car il n’existe aucun serveur de licences bureau à distance fournir une licence](#rdplicense).
- [Bureau à distance ne peut pas trouver l’ordinateur « nom »](#rdpname).
- Erreur de [une authentification. Impossible de contacter l’autorité de sécurité locale](#rdpauth).
- [Erreur de sécurité de Windows : vos informations d’identification n’a pas fonctionné](#wincred).
- [Cet ordinateur ne peut pas se connecter à l’ordinateur distant](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>La session distante a été déconnectée car il n’existe aucun serveur de licences bureau à distance fournir une licence.

Cause : La période de grâce de licence 120 jours pour le rôle serveur Bureau à distance a expiré et vous devez installer des licences.

Pour résoudre ce problème, enregistrez une copie locale du fichier RDP à partir du portail et exécutez la commande suivante à une invite de commande PowerShell pour vous connecter. Cette étape désactive la gestion des licences pour simplement cette connexion :

        mstsc <File name>.RDP /admin

Si vous ne devez en fait plus de deux connexions Bureau à distance simultanées à la machine virtuelle, vous pouvez utiliser le Gestionnaire de serveur pour supprimer le rôle serveur Bureau à distance.

Pour plus d’informations, consultez le blog [machine virtuelle Azure échoue avec « Aucun serveur de licences des services Bureau à distance disponible »](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Bureau à distance ne peut pas trouver l’ordinateur « nom ».

Cause : Le client de bureau à distance sur votre ordinateur ne peut pas résoudre le nom de l’ordinateur dans les paramètres du fichier RDP.

Solutions possibles :

- Si vous êtes sur l’intranet d’une organisation, assurez-vous que votre ordinateur a accès au serveur proxy et puisse le trafic HTTPS lui envoyer.

- Si vous utilisez un fichier RDP stocké en local, essayez d’utiliser celui qui est généré par le portail. Cette étape garantit que vous avez le nom DNS correct pour la machine virtuelle, ou le service cloud et le port de point de terminaison de la machine virtuelle. Voici un exemple de fichier RDP généré par le portail :

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

La partie de ce fichier RDP comporte :
- Le nom de domaine complet du service cloud qui contient la machine virtuelle (« tailspin-azdatatier.cloudapp.net » dans cet exemple).

- Le port TCP externe du point de terminaison pour le trafic de bureau à distance (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Une erreur d’authentification s’est produite. Impossible de contacter l’autorité de sécurité locale.

Cause : La cible machine virtuelle ne trouve pas l’autorité de sécurité dans la partie de vos informations d’identification nom d’utilisateur.

Lorsque votre nom d’utilisateur est dans l’écran *système de sécurité*\\*nom d’utilisateur* (exemple : CORP\User1), la partie du *système de sécurité* est le nom de l’ordinateur de la machine virtuelle (pour l’autorité de sécurité locale) ou un nom de domaine Active Directory.

Solutions possibles :

- Si le compte est local de la machine virtuelle, assurez-vous que le nom de la machine virtuelle est correctement orthographié.

- Si le compte se trouve sur un domaine Active Directory, vérifiez l’orthographe du nom de domaine.

- S’il est un compte de domaine Active Directory et le nom de domaine est correctement orthographié, vérifiez qu’un contrôleur de domaine est disponible dans ce domaine. Il est un problème courant dans les réseaux virtuels Azure contenant contrôleur de domaine qu’un contrôleur de domaine n’est pas disponible, car il n’a pas commencé. Pour résoudre ce problème, vous pouvez utiliser un compte d’administrateur local au lieu d’un compte de domaine.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Erreur de sécurité de Windows : vos informations d’identification n’a pas fonctionné.

Cause : La cible machine virtuelle ne peut pas valider votre nom de compte et le mot de passe.

Un ordinateur fonctionnant sous Windows peut valider les informations d’identification d’un compte local ou un compte de domaine.

- Pour les comptes locaux, utilisez le *nom_ordinateur*\\syntaxe du*nom d’utilisateur* (exemple : SQL1\Admin4798).
- Pour les comptes de domaine, utilisez le *nom_domaine*\\syntaxe du*nom d’utilisateur* (exemple : CONTOSO\peterodman).

Si vous avez promu votre machine virtuelle à un contrôleur de domaine dans une nouvelle forêt Active Directory, le compte d’administrateur local vous connecté à l’aide est converti en un compte équivalent avec le même mot de passe dans la nouvelle forêt et le domaine. Le compte local est alors supprimé.

Par exemple, si vous connectez à l’aide du compte local DC1\DCAdmin, puis promus la machine virtuelle comme contrôleur de domaine dans une nouvelle forêt pour le domaine corp.contoso.com, le compte local DC1\DCAdmin est supprimé et un nouveau compte de domaine (CORP\DCAdmin) est créé avec le même mot de passe.

Assurez-vous que le nom du compte est un nom de la machine virtuelle peut vérifier qu’un compte valide et que le mot de passe est correct.

Si vous devez modifier le mot de passe du compte d’administrateur local, voir [comment réinitialiser un mot de passe ou le service de bureau à distance pour machines virtuelles Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Cet ordinateur ne peut pas se connecter à l’ordinateur distant.

Cause : Le compte utilisé pour la connexion ne dispose pas des droits de connexion Bureau à distance.

Chaque ordinateur Windows possède un groupe local utilisateurs du Bureau à distance, qui contient les comptes et les groupes que vous pouvant vous connecter à celle-ci à distance. Les membres du groupe Administrateurs local également ont accès, même si les comptes ne figurent pas dans le groupe local utilisateurs du Bureau à distance. Pour les ordinateurs à un domaine, du groupe Administrateurs local contienne également les administrateurs de domaine pour le domaine.

Vérifiez que le compte que vous utilisez pour vous connecter avec dispose des droits de connexion Bureau à distance. Pour résoudre ce problème, utilisez un domaine ou un compte d’administrateur local pour se connecter via le Bureau à distance. Pour ajouter le compte souhaité dans le groupe local utilisateurs du Bureau à distance, utilisez le composant logiciel enfichable Microsoft Management Console (**Outils système > utilisateurs et groupes locaux > groupes > utilisateurs Bureau à distance**).


## <a name="next-steps"></a>Étapes suivantes
Si aucune de ces erreurs s’est produite et que vous avez un problème avec la connexion à l’aide de RDP inconnu, voir le [guide de dépannage pour le Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Package de diagnostics Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Étapes de l’accès à des applications qui s’exécutent sur un ordinateur virtuel de dépannage, voir [résoudre les problèmes l’accès à l’application en cours d’exécution sur un ordinateur virtuel Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Si vous rencontrez des problèmes à l’aide de SSH (Secure Shell) pour vous connecter à une VM Linux dans Azure, voir [résoudre les connexions SSH pour une machine virtuelle Linux dans Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
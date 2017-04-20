<properties 
    pageTitle="La mise à niveau de l’Agent PhoneFactor au serveur de l’authentification multifacteur Azure"
    description="Ce document décrit comment commencer à utiliser le serveur de l’authentification Multifacteur Azure et la mise à niveau de l’agent de phonefactor plus ancien."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>La mise à niveau de l’Agent PhoneFactor au serveur de l’authentification multifacteur Azure

La mise à niveau à partir de le PhoneFactor Agent 5.x ou version antérieure vers le serveur de l’authentification multifacteur Azure nécessite l’Agent de PhoneFactor et composants liées à désinstaller avant que le serveur de l’authentification multifacteur et ses composants affiliés peuvent être installés.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Pour mettre à niveau de le PhoneFactor Agent au serveur de l’authentification multifacteur Azure
<ol>
<li>Tout d’abord, sauvegardez le fichier de données PhoneFactor. L’emplacement d’installation par défaut est C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Si le portail d’utilisateur est installé :</li>
<ol>
<li>Accédez au dossier installer et sauvegarder le fichier web.config. L’emplacement d’installation par défaut est C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Si vous avez ajouté des thèmes personnalisés au portail, sauvegarder votre dossier personnalisé sous le répertoire C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Désinstaller le portail d’utilisateur par le biais le PhoneFactor Agent (disponible uniquement si installé sur le même serveur que le PhoneFactor Agent) ou Windows programmes et fonctionnalités.</li></ol>




<li>Si le Service Mobile application Web est installé :
<ol>
<li>Accédez au dossier installer et sauvegarder le fichier web.config. L’emplacement d’installation par défaut est C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Désinstaller le Service Mobile application Web via Windows programmes et fonctionnalités.</li></ol>

<li>Si le Kit de développement de Service Web est installé, désinstaller via le PhoneFactor Agent ou Windows programmes et fonctionnalités.

<li>Désinstallez l’Agent PhoneFactor entre les fenêtres programmes et fonctionnalités.

<li>Installer le serveur de l’authentification multifacteur. Notez que le chemin d’installation est sélectionné à partir du Registre de l’installation de le PhoneFactor Agent précédente afin qu’il doit installer au même emplacement (par exemple, C:\Program Files\PhoneFactor). Nouvelles installations auront un autre par défaut à installer le chemin d’accès (par exemple, C:\Program Files\Multi facteur d’authentification serveur). Le fichier de données vers la gauche le PhoneFactor Agent précédent doit être mis à niveau pendant l’installation, afin que vos utilisateurs et vos paramètres doivent toujours soient repris après avoir installé le nouveau serveur de l’authentification multifacteur.

<li>Si vous y êtes invité, activez le serveur de l’authentification multifacteur et vérifiez qu’il est affectée au groupe de réplication correcte.

<li>Si le Kit de développement de Service Web est déjà installée, installez le Kit de développement de Service Web nouvelle via l’Interface utilisateur de plusieurs facteurs d’authentification serveur. Notez que le nom du répertoire virtuel par défaut est désormais « MultiFactorAuthWebServiceSdk » au lieu de « PhoneFactorWebServiceSdk ». Si vous souhaitez utiliser l’ancien nom, vous devez modifier le nom du répertoire virtuel pendant l’installation. Dans le cas contraire, si vous autorisez l’installation à utiliser le nouveau nom par défaut, vous devez redéfinir l’URL dans toutes les applications qui font référence au Kit de développement de Service Web tels que le portail d’utilisateur et le Service Mobile application Web pour qu’il pointe à l’emplacement correct.

<li>Si le portail d’utilisateur est déjà installé sur le serveur de l’Agent PhoneFactor, installez le nouveau portail utilisateur l’authentification multifacteur via l’Interface utilisateur de plusieurs facteurs d’authentification serveur. Notez que le nom du répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PhoneFactor ». Si vous souhaitez utiliser l’ancien nom, vous devez modifier le nom du répertoire virtuel pendant l’installation. Dans le cas contraire, si vous autorisez l’installation à utiliser le nouveau nom par défaut, vous devez cliquez sur l’icône du portail de l’utilisateur sur le serveur de l’authentification multifacteur et mettre à jour l’URL du portail utilisateur sous l’onglet Paramètres.

<li>Si le portail d’utilisateur et/ou le Service Web Mobile application est déjà installée sur un serveur différent de le PhoneFactor Agent :
<ol>
<li>Accédez à l’emplacement d’installation (par exemple, C:\Program Files\PhoneFactor) et copiez l’installer(s) approprié à l’autre serveur. Il existe des programmes d’installation 32 bits et 64 bits pour le portail d’utilisateur et le Service Web de l’application Mobile. Ils sont appelés MultiFactorAuthenticationUserPortalSetupXX.msi et MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi respectivement.</li>
<li>Pour installer le portail d’utilisateur sur le serveur web, ouvrez une invite de commandes en tant qu’administrateur et exécutez la MultiFactorAuthenticationUserPortalSetupXX.msi. Notez que le nom du répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PhoneFactor ». Si vous souhaitez utiliser l’ancien nom, vous devez modifier le nom du répertoire virtuel pendant l’installation. Dans le cas contraire, si vous autorisez l’installation à utiliser le nouveau nom par défaut, vous devez cliquez sur l’icône du portail de l’utilisateur sur le serveur de l’authentification multifacteur et mettre à jour l’URL du portail utilisateur sous l’onglet Paramètres. Les utilisateurs existants devront être informé de la nouvelle URL.</li>
<li>Accédez au portail utilisateur installer emplacement (par exemple, C:\inetpub\wwwroot\MultiFactorAuth) et modifiez le fichier web.config. Copiez les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d’origine a été sauvegardé avant la mise à niveau vers le nouveau fichier web.config. Si le nouveau nom de répertoire virtuel par défaut est resté lors de l’installation du Kit de développement de Service Web, modifiez l’URL dans la section applicationSettings pour qu’il pointe vers l’emplacement correct. Si les autres paramètres par défaut ont été modifiées dans le fichier web.config précédente, appliquez ces mêmes modifications vers le nouveau fichier web.config.</li>
<li>Pour installer le Service Mobile application Web sur le serveur web, ouvrez une invite de commandes en tant qu’administrateur et exécutez la MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Notez que le nom du répertoire virtuel par défaut est désormais « MultiFactorAuthMobileAppWebService » au lieu de « PhoneFactorPhoneAppWebService ». Si vous souhaitez utiliser l’ancien nom, vous devez modifier le nom du répertoire virtuel pendant l’installation. Vous souhaiterez peut-être choisir un nom plus court afin de simplifier pour les utilisateurs finaux à taper dans sur leurs appareils mobiles. Dans le cas contraire, si vous autorisez l’installation à utiliser le nouveau nom par défaut, vous cliquez sur l’icône de l’application Mobile sur le serveur de l’authentification multifacteur et mettre à jour l’URL du Service Web Mobile application.</li>
<li>Accédez au Service Web Mobile application installer emplacement (par exemple, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) et modifiez le fichier web.config. Copiez les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d’origine a été sauvegardé avant la mise à niveau vers le nouveau fichier web.config. Si le nouveau nom de répertoire virtuel par défaut est resté lors de l’installation du Kit de développement de Service Web, modifiez l’URL dans la section applicationSettings pour qu’il pointe vers l’emplacement correct. Si les autres paramètres par défaut ont été modifiées dans le fichier web.config précédente, appliquez ces mêmes modifications vers le nouveau fichier web.config.</li></ol>


<properties
    pageTitle="Sécuriser les applications et les ressources dans Azure RemoteApp | Microsoft Azure"
    description="Apprenez à verrouiller les applications et ressources dans Azure RemoteApp"
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



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Sécuriser les applications et les ressources dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure fournit aux utilisateurs l’accès aux applications de Windows centralisée, vous permet de contrôler ce que vos utilisateurs peuvent et ne pouvez pas faire.  Ceci est particulièrement utile lorsque l’utilisateur se connecte à partir d’un périphérique non géré (par exemple, leur Macbook personnel) et que vous voulez contrôler l’accès utilisateur ou expérience.

Par exemple, si vous utilisez Active Directory pour l’authentification des utilisateurs et que vous voulez empêcher les utilisateurs de copier des données d’une application, vous pouvez configurer une stratégie de groupe Bureau à distance pour empêcher les utilisateurs de copier des données.

Un autre exemple est si vous souhaitez bloquer l’accès internet pour une application spécifique dans votre collection de sites. Vous pouvez créer une règle de pare-feu qui bloque le niveau d’accès lorsque vous créez l’image pour votre collection de sites.

## <a name="implementation-options"></a>Options d’implémentation

  Voici les options d’implémentation clés, qui peuvent être utilisées individuellement ou en tandem selon vos besoins :

1.  Si votre collection de sites RemoteApp est joint au domaine, vous pouvez appliquer une [Stratégie de groupe](https://technet.microsoft.com/library/cc725828.aspx) (à l’exception de la Idle et se déconnecter du délai d’attente stratégies décrites [ici](../azure-subscription-service-limits.md)).
2.  À la place de la stratégie de groupe (si votre collection de sites n’est pas joint au domaine ou si vous n’avez pas les privilèges appropriés dans Active Directory), vous pouvez configurer les [Stratégies locales](https://technet.microsoft.com/library/cc775702.aspx) dans votre image du modèle.  Notez que les stratégies de ce groupe Stratégies locales atout lorsqu’il y a un conflit.
3.  Certains paramètres du système d’exploitation/application ne peuvent pas être configurés via la stratégie, mais peuvent être via la clé de Registre à l’aide de l' [outil RegEdit](./remoteapp-hybridtrouble.md) lors de la configuration de votre image du modèle.
4.  Vous pouvez utiliser [Le pare-feu Windows](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) pour contrôler l’accès réseau vers et depuis l’ordinateur où l’application est en cours d’exécution. Assurez-vous que vous ne bloquer les URL et les ports définis ici.
5.  Vous pouvez utiliser [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) pour contrôler les applications et fichiers que les utilisateurs peuvent exécuter. Par exemple, les utilisateurs expérimentés peuvent calculer l’exécuter les applications que vous n’avez pas publié mais qui sont disponibles dans l’image que vous avez utilisé pour créer la collection - AppLocker peut bloquer ce.

## <a name="detailed-information"></a>Informations détaillées

- Les stratégies RDS suivantes sont susceptibles d’être plus utiles :
    - [Appareil et la Redirection de ressources](https://technet.microsoft.com/library/ee791794.aspx)
    - [Redirection de l’imprimante](https://technet.microsoft.com/library/ee791784.aspx)
    - [Les profils](https://technet.microsoft.com/library/ee791865.aspx).
- Notez que configuration des redirections via la session RemoteApp PowerShell module (comme guide [ici](./remoteapp-redirection.md)) repose sur l’ordinateur client pour appliquer la stratégie, afin que si la sécurité est l’objectif principal vous souhaiterez appliquer la stratégie via la stratégie modèle d’image local ou via la stratégie de groupe.
- [Stratégies de Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Stratégies d’Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (y compris [comment personnaliser la barre d’outils Office](https://technet.microsoft.com/library/cc179143.aspx)).

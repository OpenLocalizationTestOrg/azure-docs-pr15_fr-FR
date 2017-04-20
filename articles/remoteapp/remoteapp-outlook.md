<properties
    pageTitle="À l’aide d’Outlook dans Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment configurer et utiliser Outlook dans Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>À l’aide de Microsoft Outlook dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure prend en charge Microsoft Outlook Office 365. En savoir plus sur comment [fonctionne Office dans Azure RemoteApp](remoteapp-officesubscription.md). Il existe plusieurs paramètres recommandés pour Outlook lorsqu’il est utilisé dans Azure RemoteApp.

## <a name="cached-mode"></a>Mode mis en cache
Mode mis en cache est une configuration recommandée lorsque vous utilisez Outlook dans Azure RemoteApp. Lorsque vous configurez un compte Outlook 2013 pour utiliser le Mode Exchange mis en cache, Outlook 2013 fonctionne à partir d’une copie locale de la boîte aux lettres Microsoft Exchange de l’utilisateur qui est stockée dans un fichier de données en mode hors connexion (fichier OST) sur l’ordinateur de l’utilisateur, ainsi que le mode hors connexion adresse carnet. La boîte aux lettres mis en cache et OAB sont régulièrement mises à jour à partir du service Office 365. En savoir plus sur [les différences entre le mode mis en cache et en ligne](https://technet.microsoft.com/library/jj683103.aspx).

L’utilisateur peut sélectionner **Le Mode Exchange mis en cache** ou **Mode en ligne** au cours de la configuration de compte ou en modifiant les paramètres du compte. Vous pouvez également déployer un mode ou l’autre à l’aide de l’outil de personnalisation Office (OCT) ou la stratégie de groupe.  

Lire les [instructions étape par étape sur l’activation du mode mis en cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Recherche
Dans Azure RemoteApp, à l’aide de la recherche dans Outlook comporte des limitations. RemoteApp Azure utilise machines virtuelles pool pour prendre en compte les sessions utilisateur. L’indexation de la recherche dépend de l’ID de l’ordinateur, qui est différente pour les différentes machines virtuelles. Il est possible que chaque fois qu’un utilisateur se connecte à Azure RemoteApp, ils sont dirigés vers un nouvel ordinateur virtuel. Cela signifierait que, si nous activer la recherche local, l’indexeur s’exécutera chaque fois que le code d’appareil change (lorsque l’utilisateur se trouve sur un autre ordinateur virtuel). Selon la taille de la. Fichier OST, l’indexeur peut prendre beaucoup de temps à compléter et utiliser des ressources nécessaires pour les autres applications. Recherche pas uniquement serait lente mais ne peut pas produire des résultats. À l’aide d’un profil de compte en Mode en ligne serait contourner ce problème, mais les performances globales risquent d’être affectées en raison de l’absence d’un cache local (voir le lien ci-dessus pour plus d’informations sur la différence entre le mode mis en cache et en ligne). Malheureusement, indexées/locale recherche ne peut pas être désactivé et la recherche en ligne ne peut pas être activée par défaut dans Outlook 2013.

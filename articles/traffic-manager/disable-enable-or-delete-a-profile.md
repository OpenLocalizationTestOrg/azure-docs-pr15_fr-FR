<properties
   pageTitle="Désactiver, activer ou supprimer un profil le trafic Manager | Microsoft Azure"
   description="Cet article vous aidera à travailler avec les profils de votre gestionnaire de trafic."
   services="traffic-manager"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>Désactiver, activer ou supprimer un profil


Vous pouvez désactiver un profil existant gestionnaire le trafic afin qu’il redirigera pas les demandes de l’utilisateur à ses points de terminaison configurés. Lorsque vous désactivez un profil le trafic Manager, le profil lui-même et les informations contenues dans le profil est conservés et peut être modifiée dans l’interface du Gestionnaire de trafic. Lorsque vous souhaitez activer de nouveau le profil, vous pouvez facilement faire dans la Azure portal et redirections va reprendre. Lorsque vous créez un profil Manager le trafic dans le portail Azure, il est automatiquement activée.

## <a name="to-disable-a-profile"></a>Pour désactiver un profil

1. Modifier l’enregistrement de ressource DNS sur votre serveur DNS Internet pour utiliser le type d’enregistrement approprié et le pointeur vers un autre nom ou l’adresse IP d’un emplacement spécifique sur Internet. En d’autres termes, modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin qu’il utilise n’est plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil le trafic Manager.
1. Le trafic n’est plus redirigé vers les points de terminaison via les paramètres de profil du Gestionnaire de trafic.
1. Sélectionnez le profil que vous souhaitez désactiver. Sélectionnez le profil, dans la page Gestionnaire de trafic, mettez en surbrillance le profil en cliquant sur la colonne à côté du nom du profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, comme cela vous accédez alors à la page Paramètres pour le profil.
1. Après avoir sélectionné le profil, cliquez sur Désactiver dans la partie inférieure de la page.

## <a name="to-enable-a-profile"></a>Pour activer un profil

1. Sélectionnez le profil que vous souhaitez activer. Sélectionnez le profil, dans la page Gestionnaire de trafic, mettez en surbrillance le profil en cliquant sur la colonne à côté du nom du profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, comme cela vous accédez alors à la page Paramètres pour le profil.
1. Après avoir sélectionné le profil, cliquez sur Activer en bas de la page.
1. Modifier l’enregistrement de ressource DNS sur votre serveur DNS Internet pour utiliser le type d’enregistrement CNAME, qui mappe le nom de domaine de votre entreprise le nom de domaine de votre profil le trafic Manager. Pour plus d’informations, voir [Point un domaine à un domaine Manager le trafic Internet d’entreprise](traffic-manager-point-internet-domain.md).
1. Le trafic commence redirigé vers les points de terminaison à nouveau.

## <a name="delete-a-profile"></a>Supprimer un profil


1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet utilise n’est plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil le trafic Manager.
1. Sélectionnez le profil que vous voulez supprimer. Sélectionnez le profil, dans la page Gestionnaire de trafic, sélectionnez le profil par
1. en cliquant sur la colonne à côté du profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, comme cela vous accédez alors à la page Paramètres pour le profil.
1. Après avoir sélectionné le profil, cliquez sur Supprimer dans la partie inférieure de la page.

## <a name="next-steps"></a>Étapes suivantes

[Gestionnaire de trafic - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

[Configurer la méthode de routage basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage alternées](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)


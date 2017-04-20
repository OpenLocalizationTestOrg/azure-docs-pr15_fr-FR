<properties
    pageTitle="Forum aux questions sur les Services cloud | Microsoft Azure"
    description="Forum aux questions sur les Services en nuage."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>Forum aux questions sur les Services cloud
Cet article fournit des réponses à certaines questions fréquemment posées sur les Services de Cloud Microsoft Azure. Vous pouvez également consulter le [Forum aux questions sur Azure prend en charge](http://go.microsoft.com/fwlink/?LinkID=185083) pour Azure tarifs et prise en charge des informations générales. Vous pouvez également consulter la [page de taille de mémoire virtuelle Services Cloud](cloud-services-sizes-specs.md) pour plus d’informations de taille.

## <a name="certificates"></a>Certificats

### <a name="where-should-i-install-my-certificate"></a>Où dois-je installer mon certificat ?

- **Mon**  
Certificat d’application avec une clé privée (\*.pfx, \*.p12).

- **AUTORITÉ DE CERTIFICATION**  
Tous vos certificats intermédiaires accéder dans cette banque d’informations (stratégie et autorités de certification Sub).

- **RACINE**  
Autorité de certification racine stocker, afin que votre certificat d’autorité de certification racine principale doit venir ici.

### <a name="i-cant-remove-expired-certificate"></a>Je ne parviens pas à supprimer certificat a expiré

Azure vous empêche de suppression d’un certificat s’il est en cours d’utilisation. Vous devez supprimer le déploiement qui utilise le certificat, ou mettre à jour le déploiement avec un certificat renouvelé ou autre.

### <a name="delete-an-expired-certificate"></a>Supprimer un certificat qui a expiré

Dans la mesure où le certificat n’est pas en cours d’utilisation, vous pouvez utiliser l’applet de commande PowerShell [AzureCertificate-supprimer](https://msdn.microsoft.com/library/azure/mt589145.aspx) pour supprimer un certificat.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>J’ai expiré nommés Windows Azure Gestion des services pour les Extensions de certificats

Ces certificats sont créés chaque fois qu’une extension est ajoutée au service cloud tel que l’extension de bureau à distance. Ces certificats sont uniquement utilisées pour chiffrer et déchiffrer la configuration de l’extension privée. Si ces certificats expirent n’a pas d’importance. La date d’expiration n’est pas activée.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>J’ai supprimé les certificats continuent à apparaître

Ils continuent à apparaître probablement en raison d’un outil que vous utilisez, tels que Visual Studio. Lorsque vous vous reconnectez avec un outil qui utilise un certificat, il sera à nouveau être téléchargée vers Azure.

### <a name="my-certificates-keep-disappearing"></a>Mes certificats ne cessent de disparaître

Lorsque l’instance de machine virtuelle réutilise, toutes les modifications locales sont perdues. Utiliser une [tâche de démarrage](cloud-services-startup-tasks.md) pour installer les certificats à la machine virtuelle chaque fois que le rôle démarre.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Je ne trouve pas mes certificats gestion dans le portail

[Certificats de gestion](..\azure-api-management-certs.md) ne sont disponibles dans le portail classique Azure. Le portail Azure actuel n’utilise pas de certificats de gestion. 

### <a name="how-can-i-disable-a-management-certificate"></a>Comment puis-je désactiver un certificat de gestion ?

[Certificats de gestion](..\azure-api-management-certs.md) ne peut pas être désactivée. Vous supprimez via le portail classique Azure lorsque vous ne voulez pas qu’elles plus être utilisé.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Comment créer un certificat SSL pour une adresse IP spécifique ?

Suivez les instructions de la [créer un didacticiel certificat](cloud-services-certs-create.md). Utilisez l’adresse IP comme nom de DNS.

## <a name="security"></a>Sécurité

### <a name="disable-ssl-30"></a>Désactiver SSL 3.0

Pour désactiver SSL 3.0 et utiliser la sécurité TLS, créer une tâche de démarrage qui est décrite dans ce billet de blog : https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Échelle un service cloud

### <a name="i-cannot-scale-beyond-x-instances"></a>Je ne parviens pas à mettre à l’échelle au-delà de X instances

Votre abonnement Azure a une limite sur le nombre de cœurs, que vous pouvez utiliser. Mise à l’échelle ne fonctionnera pas si vous avez utilisé tous les cœurs disponibles. Par exemple, si vous avez une limite de 100 cœurs, cela signifie que vous pouvez y 100 instances de machine virtuelle A1 dimensionnées pour votre service cloud, ou 50 A2 Tabloïd machine virtuelle instances.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Je ne peux pas réserver une adresse IP dans un service cloud multi-VIP

Tout d’abord, assurez-vous que l’instance machine virtuelle que vous essayez de réserver l’adresse IP est activé. Ensuite, vérifiez que vous utilisez des adresses IP réservés pour sembleront les déploiements intermédiaires et de production. **Ne pas** modifier les paramètres pendant que le déploiement est mise à niveau.


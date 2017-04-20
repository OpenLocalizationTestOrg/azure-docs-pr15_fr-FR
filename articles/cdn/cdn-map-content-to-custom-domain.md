<properties
     pageTitle="Comment mapper le contenu du réseau (CDN) Azure distribution de contenu à un domaine personnalisé | Microsoft Azure"
     description="Cette rubrique vous montrer comment mapper un contenu CDN sur un domaine personnalisé."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Comment mapper un domaine personnalisé au point de terminaison de réseau de remise de contenu (CDN)
Vous pouvez mapper un domaine personnalisé à un point de terminaison CDN pour pouvoir utiliser votre propre nom de domaine dans les URL pour le contenu mis en cache plutôt que d’utiliser un sous-domaine de azureedge.net.

Il existe deux façons de mapper votre domaine personnalisé à un point de terminaison CDN :

1. [Créer un enregistrement CNAME avec votre enregistrement de domaines et y mapper votre domaine personnalisé et sous-domaine au point de terminaison CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source, comme `www.contosocdn.com` ou `cdn.contoso.com`, pour un domaine de destination. Dans ce cas, le domaine source est votre domaine personnalisé et sous-domaine (un sous-domaine, telles que **www** ou **cdn** est toujours requis). Le domaine de destination est votre point de terminaison CDN.  

    Le processus de mappage de votre domaine personnalisé pour votre point de terminaison CDN, cependant, vous risquez dans une courte période d’inactivité pour le domaine pendant que vous enregistrez le domaine dans le portail Azure.

2. [Ajouter une étape intermédiaire d’inscription avec **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Si votre domaine personnalisé est actuellement en charge d’une application avec un niveau de service accord () qui ne doit pas d’interruption, vous pouvez utiliser le sous-domaine Azure **cdnverify** fournissent une étape intermédiaire d’inscription afin que les utilisateurs pourront accéder à votre domaine pendant le DNS mappage a lieu.  

Après avoir enregistré votre domaine personnalisé à l’aide d’une des procédures ci-dessus, vous voulez [vérifier que le sous-domaine personnalisé fait référence à votre point de terminaison CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Vous devez créer un enregistrement CNAME avec vos domaines pour mapper votre domaine sur le point de terminaison CDN. Enregistrements CNAME plan sous-domaines spécifiques telle que `www.contoso.com` ou `cdn.contoso.com`. Il n’est pas possible de mapper un enregistrement CNAME pour un domaine racine, tel que `contoso.com`.
>    
> Un sous-domaine peut uniquement être associé à un point de terminaison CDN. L’enregistrement CNAME que vous créez achemine le trafic tous les destinataires du sous-domaine au point de terminaison spécifié.  Par exemple, si vous associez `www.contoso.com` avec votre point de terminaison CDN, puis vous ne pouvez pas l’associer à autres points de terminaison Azure, par exemple un point de terminaison de compte de stockage ou un point de terminaison du service cloud. Toutefois, vous pouvez utiliser différents sous-domaines du même domaine pour les points de terminaison de service différent. Vous pouvez également mapper différents sous-domaines au même point de terminaison CDN.
>
> Pour les points de terminaison **Azure CDN de Verizon** (Standard et Premium), notez qu’elle occupe à **90 minutes** pour que les modifications de domaine personnalisé de se propager à nœuds de bord CDN.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Enregistrer un domaine personnalisé pour un point de terminaison Azure CDN

1.  Connectez-vous au [portail Azure](https://portal.azure.com/).
2.  Cliquez sur **Parcourir**, puis **CDN profils**, puis le profil CDN avec le point de terminaison que vous souhaitez établir une correspondance entre un domaine personnalisé.  
3.  Dans la carte **CDN profil** , cliquez sur le point de terminaison CDN auquel vous voulez associer le sous-domaine.
4.  Dans la partie supérieure de la cuillère de point de terminaison, cliquez sur le bouton **Ajouter un domaine personnalisé** .  Dans la carte **Ajouter un domaine personnalisé** , vous verrez le nom d’hôte du point de terminaison dérivé de votre point de terminaison CDN, à utiliser pour créer un enregistrement CNAME. Le format de l’adresse de nom d’hôte s’affichent en tant que ** &lt;EndpointName >. azureedge.net**.  Vous pouvez copier ce nom d’hôte à utiliser pour créer l’enregistrement CNAME.  
5.  Accédez au site web du votre bureau d’enregistrement domaine, puis recherchez la section pour la création d’enregistrements DNS. Cela peut s’avérer dans une section comme **Nom de domaine**, **DNS**ou **Gestion des noms de serveur**.
6.  Recherchez la section pour la gestion des enregistrements CNAME. Vous devrez peut-être accéder à une page de paramètres avancés et recherchez les mots CNAME, Alias ou sous-domaines.
7.  Créer un enregistrement CNAME qui mappe votre sous-domaine choisie (par exemple, **www** ou **cdn**) au nom d’hôte fourni dans la carte **Ajouter un domaine personnalisé** .
8.  Revenir à la carte **Ajouter un domaine personnalisé** , puis entrez votre domaine personnalisé, y compris le sous-domaine, dans la boîte de dialogue. Par exemple, entrez le nom de domaine au format `www.contoso.com` ou `cdn.contoso.com`.   

    Azure vérifie l’existence de l’enregistrement CNAME pour le nom de domaine que vous avez entré. Si l’enregistrement CNAME est correct, votre domaine personnalisé est validée.  Pour les points de terminaison **Azure CDN de Verizon** (Standard et Premium), il peut prendre jusqu'à 90 minutes pour les paramètres de domaine personnalisé de se propager à tous les nœuds de bord CDN, cependant.  

    Notez que dans certains cas, peut prendre du temps pour l’enregistrement CNAME de se propager à serveurs sur Internet. Si votre domaine n’est pas validé immédiatement et vous pensez que l’enregistrement CNAME est correct, puis patientez quelques minutes et réessayez.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Enregistrer un domaine personnalisé pour un point de terminaison Azure CDN utilisant le sous-domaine cdnverify intermédiaire  

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**, puis **CDN profils**, puis le profil CDN avec le point de terminaison que vous souhaitez établir une correspondance entre un domaine personnalisé.  
3. Dans la carte **CDN profil** , cliquez sur le point de terminaison CDN auquel vous voulez associer le sous-domaine.
4. Dans la partie supérieure de la cuillère de point de terminaison, cliquez sur le bouton **Ajouter un domaine personnalisé** .  Dans la carte **Ajouter un domaine personnalisé** , vous verrez le nom d’hôte du point de terminaison dérivé de votre point de terminaison CDN, à utiliser pour créer un enregistrement CNAME. Le format de l’adresse de nom d’hôte s’affichent en tant que ** &lt;EndpointName >. azureedge.net**.  Vous pouvez copier ce nom d’hôte à utiliser pour créer l’enregistrement CNAME.
5. Accédez au site web du votre bureau d’enregistrement domaine, puis recherchez la section pour la création d’enregistrements DNS. Cela peut s’avérer dans une section comme **Nom de domaine**, **DNS**ou **Gestion des noms de serveur**.
6. Recherchez la section pour la gestion des enregistrements CNAME. Vous devrez peut-être accéder à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.
7. Créer un enregistrement CNAME et de fournir un alias de sous-domaine qui inclut le sous-domaine **cdnverify** . Par exemple, le sous-domaine que vous spécifiez seront dans le format **cdnverify.www** ou **cdnverify.cdn**. Puis indiquez le nom d’hôte, ce qui correspond à votre point de terminaison CDN, dans le format **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Revenir à la carte **Ajouter un domaine personnalisé** , puis entrez votre domaine personnalisé, y compris le sous-domaine, dans la boîte de dialogue. Par exemple, entrez le nom de domaine au format `www.contoso.com` ou `cdn.contoso.com`. Notez que dans cette étape, il est inutile à faire précéder le sous-domaine avec **cdnverify**.  

    Azure vérifie l’existence de l’enregistrement CNAME pour le nom de domaine cdnverify que vous avez entré.
9. À ce stade, votre domaine personnalisé a été vérifié par Azure, mais le trafic vers votre domaine n’est pas encore en cours acheminé vers votre point de terminaison CDN. Une fois que le temps en attente pour autoriser les paramètres de domaine personnalisé de se propager aux nœuds de bord CDN (90 minutes pour **Azure CDN de Verizon**, 1-2 minutes pour **Azure CDN à partir d’Akamai**), revenir au site web de votre bureau d’enregistrement DNS et créer un autre enregistrement CNAME qui mappe votre sous-domaine vers votre point de terminaison CDN. Par exemple, spécifier le sous-domaine que **www** ou **cdn**et le nom d’hôte en tant que ** &lt;EndpointName >. azureedge.net**. Cette étape, l’inscription de votre domaine personnalisé est terminée.
10. Enfin, vous pouvez supprimer l’enregistrement CNAME créé à l’aide de **cdnverify**, telle qu’elle était nécessaire uniquement comme une étape intermédiaire.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Vérifiez que le sous-domaine personnalisé fait référence à votre point de terminaison CDN

- Une fois que vous avez terminé l’enregistrement de votre domaine personnalisé, vous pouvez accéder au contenu est mis en cache sur votre point de terminaison CDN utilisant le domaine personnalisé.
Tout d’abord, assurez-vous que vous disposez du contenu public qui est mises en cache au point de terminaison. Par exemple, si votre point de terminaison CDN est associé à un compte de stockage, le CDN met en cache du contenu dans des conteneurs blob public. Pour tester le domaine personnalisé, assurez-vous que votre conteneur est défini sur Autoriser l’accès public et qu’il contient au moins un blob.
- Dans votre navigateur, accédez à l’adresse de l’objet blob à l’aide du domaine personnalisé. Par exemple, si votre domaine personnalisé est `cdn.contoso.com`, l’URL dans un blob mis en cache est similaire à l’URL suivante : http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Voir aussi

[Comment activer le réseau de distribution de contenu (CDN) pour Azure](./cdn-create-new-endpoint.md)  

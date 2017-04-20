<properties
    pageTitle="Configurer un nom de domaine pour votre point de terminaison du stockage Blob | Microsoft Azure"
    description="Découvrez comment mapper un domaine personnalisé utilisateur sur le point de terminaison du stockage Blob pour un compte de stockage Azure dans le portail classique Azure."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurer un nom de domaine personnalisé pour votre point de terminaison du stockage Blob

## <a name="overview"></a>Vue d’ensemble

Vous pouvez configurer un domaine personnalisé pour l’accès aux données blob dans votre compte de stockage Azure. Le point de terminaison par défaut pour le stockage Blob est `<storage-account-name>.blob.core.windows.net`. Si vous mappez un domaine personnalisé et sous-domaine telle que **www.contoso.com** au point de terminaison blob pour votre compte de stockage, puis vos utilisateurs peuvent également accéder aux données de blob dans votre compte de stockage à l’aide de ce domaine.

>[AZURE.IMPORTANT] Stockage Azure ne prend pas en charge le HTTPS avec des domaines personnalisés encore. Nous ne connaissent que les clients sont intéressés par cette fonctionnalité, et qu’il soit disponible dans une version ultérieure.

Il existe deux façons pour qu’il pointe votre domaine personnalisé pour le point de terminaison blob pour votre compte de stockage. Le moyen le plus simple consiste à créer un enregistrement CNAME mappage de votre domaine personnalisé et sous-domaine au point de terminaison blob. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source vers un domaine de destination. Dans ce cas, le domaine source est votre domaine et sous-domaine--Notez que le sous-domaine est toujours requis. Le domaine de destination est votre point de terminaison du service Blob.

Le processus de mappage de votre domaine personnalisé pour votre point de terminaison blob, cependant, vous risquez dans une courte période d’inactivité pour le domaine pendant que vous enregistrez le domaine dans le [Portail classique Azure](https://manage.windowsazure.com). Si votre domaine personnalisé est actuellement en charge d’une application avec un niveau de service accord () qui ne doit pas d’interruption, vous pouvez utiliser le sous-domaine Azure **asverify** fournissent une étape intermédiaire d’inscription afin que les utilisateurs pourront accéder à votre domaine pendant le DNS mappage a lieu.

Le tableau suivant montre un exemple URL permettant d’accéder aux données blob dans un compte de stockage nommé **mystorageaccount**. Le domaine personnalisé enregistré pour le compte de stockage est **www.contoso.com**:

Type de ressource|Formats d’URL
---|---
Compte de stockage|**URL par défaut :** http://mystorageaccount.blob.core.windows.net<p>**URL de domaine personnalisé :** http://www.contoso.com</td>
Objets BLOB|**URL par défaut :** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL de domaine personnalisé :** http://www.contoso.com/mycontainer/myblob
Conteneur racine|**URL par défaut :** http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$ racine/myblob<p>**URL de domaine personnalisé :** http://www.contoso.com/myblob ou http://www.contoso.com/$ racine/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>Enregistrer un domaine personnalisé pour votre compte de stockage

Utilisez cette procédure pour enregistrer votre domaine personnalisé si vous ne disposez pas des questions concernant l’icône de domaine ne pas être brièvement disponibles aux utilisateurs, ou si votre domaine personnalisé n’héberge pas actuellement une application.

Si votre domaine personnalisé est actuellement en charge d’une application qui ne peut pas contenir les temps d’arrêt, puis utilisez la procédure décrite dans <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">livre un domaine personnalisé pour votre compte de stockage en utilisant le sous-domaine asverify intermédiaires</a>.

Pour configurer un nom de domaine personnalisé, vous devez créer un enregistrement CNAME avec votre enregistrement de domaines. L’enregistrement CNAME spécifie un alias pour un nom de domaine ; Dans ce cas, il mappe l’adresse de votre domaine personnalisé pour le point de terminaison du stockage Blob de votre compte de stockage.

Chaque registrar a une méthode similaire, mais légèrement différente de spécification d’un enregistrement CNAME, mais le concept est le même. Notez que de nombreux packages d’enregistrement de domaine de base n’offrent pas de configuration de DNS, vous serez peut-être amené à mettre à niveau votre package d’inscription de domaine que vous puissiez créer l’enregistrement CNAME.

1.  Dans le [Portail classique Azure](https://manage.windowsazure.com), accédez à l’onglet **espace de stockage** .

2.  Dans l’onglet **stockage** , cliquez sur le nom du compte de stockage pour lequel vous voulez mapper le domaine personnalisé.

3.  Cliquez sur l’onglet **configurer** .

4.  Dans la partie inférieure de l’écran, cliquez sur **Gérer le domaine** pour afficher la boîte de dialogue **Gérer le domaine personnalisé** . Dans le texte en haut de la boîte de dialogue, vous verrez des informations sur la façon de créer l’enregistrement CNAME. Pour cette procédure, ignorer le texte qui fait référence au sous-domaine **asverify** .

5.  Connectez-vous au site Web de votre bureau d’enregistrement DNS, puis accédez à la page de gestion DNS. Cela peut s’avérer dans une section comme **Nom de domaine**, **DNS**ou **Gestion des noms de serveur**.

6.  Recherchez la section pour la gestion des enregistrements CNAME. Vous devrez peut-être accéder à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

7.  Créer un enregistrement CNAME et de fournir un alias de sous-domaine, tel que **www** ou **photos**. Ensuite, fournir un nom d’hôte, ce qui correspond à votre point de terminaison Blob service dans le format **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** est le nom de votre compte de stockage). Utiliser le nom d’hôte est fourni pour vous dans le texte de la boîte de dialogue **Gérer le domaine personnalisé** .

8.  Après avoir créé l’enregistrement CNAME, revenir à la boîte de dialogue **Gérer le domaine personnalisé** et entrez le nom de votre domaine personnalisé, y compris le sous-domaine, dans le champ **Nom de domaine personnalisé** . Par exemple, si votre domaine est **contoso.com** et votre sous-domaine est **www**, entrez **www.contoso.com**; Si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est requis.

9. Cliquez sur le bouton **Enregistrer** pour enregistrer votre domaine personnalisé.

    Si l’enregistrement a réussi, vous verrez le message de **votre domaine personnalisé est actif**. Les utilisateurs peuvent maintenant afficher blob des données dans votre domaine personnalisé, dans la mesure où ils disposent des autorisations appropriées.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Enregistrer un domaine personnalisé pour votre compte de stockage en utilisant le sous-domaine asverify intermédiaire

Cette procédure permet d’enregistrer votre personnalisé domaine si votre domaine personnalisé est actuellement en charge d’une application avec accord qui requiert qu’il y ait sans interruption de service. En créant un enregistrement CNAME qui pointe à partir d’asverify. &lt;sous-domaine&gt;. &lt;customdomain&gt; à asverify. &lt;storageaccount&gt;. blob.core.windows.net, vous pouvez pré-inscription votre domaine avec Azure. Vous pouvez ensuite créer un deuxième enregistrement CNAME qui pointe de &lt;sous-domaine&gt;. &lt;customdomain&gt; à &lt;storageaccount&gt;. blob.core.windows.net, moment auquel le trafic vers votre domaine personnalisé sera dirigé vers votre point de terminaison blob.

Le sous-domaine asverify est un sous-domaine spécial reconnu par Azure. En ajoutant **asverify** à votre propre sous-domaine, vous autorisez Azure reconnaître votre domaine personnalisé sans modifier l’enregistrement DNS pour le domaine. Une fois que vous modifiez l’enregistrement DNS pour le domaine, il sera mappé au point de terminaison blob sans interruption de service.

1.  Dans le [Portail classique Azure](https://manage.windowsazure.com), accédez à l’onglet **espace de stockage** .

2.  Dans l’onglet **stockage** , cliquez sur le nom du compte de stockage pour lequel vous voulez mapper le domaine personnalisé.

3.  Cliquez sur l’onglet **configurer** .

4.  Dans la partie inférieure de l’écran, cliquez sur **Gérer le domaine** pour afficher la boîte de dialogue **Gérer le domaine personnalisé** . Dans le texte en haut de la boîte de dialogue, vous verrez des informations sur la création de l’enregistrement CNAME en utilisant le sous-domaine **asverify** .

5.  Connectez-vous au site Web de votre bureau d’enregistrement DNS, puis accédez à la page de gestion DNS. Cela peut s’avérer dans une section comme **Nom de domaine**, **DNS**ou **Gestion des noms de serveur**.

6.  Recherchez la section pour la gestion des enregistrements CNAME. Vous devrez peut-être accéder à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

7.  Créer un enregistrement CNAME et de fournir un alias de sous-domaine qui inclut le sous-domaine asverify. Par exemple, le sous-domaine que vous spécifiez seront dans le format **asverify.www** ou **asverify.photos**. Ensuite, fournir un nom d’hôte, ce qui correspond à votre point de terminaison Blob service dans format **asverify.mystorageaccount.blob.core.windows.net** (où **mystorageaccount** est le nom de votre compte de stockage). Utiliser le nom d’hôte est fourni pour vous dans le texte de la boîte de dialogue **Gérer le domaine personnalisé** .

8.  Après avoir créé l’enregistrement CNAME, revenir à la boîte de dialogue **Gérer le domaine personnalisé** et entrez le nom de votre domaine personnalisé dans le champ **Nom de domaine personnalisé** . Par exemple, si votre domaine est **contoso.com** et votre sous-domaine est **www**, entrez **www.contoso.com**; Si votre sous-domaine est **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est requis.

9.  Cliquez sur la case à cocher m’indique que **Avancé : utiliser le sous-domaine « asverify » pour enregistrer préalablement mon domaine personnalisé**.

10. Cliquez sur le bouton **Enregistrer** pour enregistrer préalablement votre domaine personnalisé.

    Si le préenregistrement réussit, vous verrez le message de **votre domaine personnalisé est actif**.

11. À ce stade, votre domaine personnalisé a été vérifié par Azure, mais le trafic vers votre domaine n’est pas encore en cours acheminé vers votre compte de stockage. Pour terminer le processus, revenir au site Web de votre bureau d’enregistrement DNS, puis créer un autre enregistrement CNAME qui mappe votre sous-domaine vers votre point de terminaison du service Blob. Par exemple, spécifiez le sous-domaine que **www** ou des **photos**et le nom d’hôte comme **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** est le nom de votre compte de stockage). Cette étape, l’inscription de votre domaine personnalisé est terminée.

12. Enfin, vous pouvez supprimer l’enregistrement CNAME créé à l’aide **asverify**, telle qu’elle était nécessaire uniquement comme une étape intermédiaire.

Les utilisateurs peuvent maintenant afficher blob des données dans votre domaine personnalisé, dans la mesure où ils disposent des autorisations appropriées.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Vérifiez que le domaine personnalisé fait référence à votre point de terminaison du service Blob

Pour vérifier que votre domaine personnalisé est bien mappé à votre point de terminaison du service Blob, créez un blob dans un conteneur public au sein de votre compte de stockage. Puis, dans un navigateur web, utilisez un URI au format suivant pour accéder au blob :

-   http://< ;*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Par exemple, vous pouvez utiliser l’URI suivant pour accéder à un formulaire web via un sous-domaine personnalisé **photos.contoso.com** qui correspond à un objet blob dans le conteneur **myforms** :

-   http://photos.contoso.com/MyForms/ApplicationForm.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Annuler l’enregistrement d’un domaine personnalisé à partir de votre compte de stockage

Pour annuler l’enregistrement d’un domaine personnalisé, procédez comme suit : 

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com). 

2. Dans le volet de navigation, cliquez sur **espace de stockage**. 

3. Dans la page de **stockage** , cliquez sur le nom du compte de stockage pour afficher le tableau de bord. 

5. Dans le ruban, cliquez sur **Gérer le domaine**. 

6. Dans la boîte de dialogue **Gérer le domaine personnalisé** , cliquez sur **Annuler l’enregistrement**. 


## <a name="additional-resources"></a>Ressources supplémentaires

-   [Comment mapper un domaine personnalisé au point de terminaison de réseau de remise de contenu (CDN)](../cdn/cdn-map-content-to-custom-domain.md)

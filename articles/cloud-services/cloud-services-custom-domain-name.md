<properties
    pageTitle="Configurer un nom de domaine personnalisé dans les Services de Cloud | Microsoft Azure"
    description="Découvrez comment exposer votre application Azure ou les données sur un domaine personnalisé en configurant les paramètres DNS."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configuration d’un nom de domaine personnalisé pour un service cloud Azure

> [AZURE.SELECTOR]
- [Portail Azure](cloud-services-custom-domain-name-portal.md)
- [Portail classique Azure](cloud-services-custom-domain-name.md)


Lorsque vous créez un Service Cloud, Azure assigne à un sous-domaine de cloudapp.net. Par exemple, si votre Service Cloud est nommé « contoso », vos utilisateurs ne pourront pas accéder à votre application sur une URL comme http://contoso.cloudapp.net. Azure affecte également une adresse IP virtuelle.

Toutefois, vous pouvez également exposer votre application sur votre propre nom de domaine, tel que contoso.com. Cet article explique comment réserver ou configurer un nom de domaine personnalisé pour les rôles web Service Cloud.

Avez-vous déjà undestand quels enregistrements CNAME et A sont ? [Renvoi au-delà de l’explaination](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Commencer rapidement ! Utilisez le Azure [procédure guidée à](http://support.microsoft.com/kb/2990804). Cela est association d’un nom de domaine personnalisé et sécuriser la communication (SSL) avec les sites Web Azure ou les Services en nuage Azure un clin de œil.

<p/>

> [AZURE.NOTE]
> Les procédures décrites dans cette tâche s’appliquent aux Services Cloud Azure. Pour les Services d’application, voir [ce](../app-service-web/web-sites-custom-domain-name.md). Pour les comptes de stockage, voir [ce](../storage/storage-custom-domain-name.md).


## <a name="understand-cname-and-a-records"></a>Comprendre les enregistrements CNAME et A

CNAME (ou alias enregistrement) et un enregistrements vous permettent d’associer un nom de domaine à un serveur spécifique (ou du service dans ce cas,) toutefois elles fonctionnent différemment. Il existe également quelques considérations spécifiques lorsque vous utilisez un enregistrements avec les services en nuage Azure que vous devez prendre en compte avant de décider lequel utiliser.

### <a name="cname-or-alias-record"></a>Enregistrement CNAME ou Alias

Un enregistrement CNAME mappe un domaine *spécifique* , tel que **contoso.com** ou **www.contoso.com**, à un nom de domaine complet. Dans ce cas, le nom de domaine canonique est le **[myapp] .cloudapp .net** nom de domaine de votre Azure application hébergée. Une fois créé, l’enregistrement CNAME crée un alias pour la **[myapp] .cloudapp .net**. L’entrée CNAME résoudra à l’adresse IP de votre **[myapp] .cloudapp .net** service automatiquement, donc si l’adresse IP du service cloud change, vous n’avez pas à exécuter l’action.

> [AZURE.NOTE]
> Certains bureaux autorise uniquement mapper des sous-domaines lors de l’utilisation d’un enregistrement CNAME, par exemple www.contoso.com et non les noms de racine, tel que contoso.com. Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d’enregistrement, [l’entrée Wikipédia dans l’enregistrement CNAME](http://en.wikipedia.org/wiki/CNAME_record)ou le document [IETF de noms de domaine - implémentation et spécification](http://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Un enregistrement

Un enregistrement A mappe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un domaine génériques* telles que ** \*. contoso.com**, vers une adresse IP. Dans le cas d’un Service Cloud Azure, l’adresse IP virtuelle du service. Le principal avantage d’un enregistrement A sur un enregistrement CNAME est que vous pouvez avoir une entrée qui utilise un caractère générique, tel que \* **. contoso.com**, qui serait gérer des requêtes de plusieurs sous-domaines comme **mail.contoso.com**, **login.contoso.com**ou **www.contso.com**.

> [AZURE.NOTE]
> Dans la mesure où un enregistrement A est mappé à une adresse IP statique, il ne peut pas résoudre automatiquement les modifications à l’adresse IP de votre Service Cloud. L’adresse IP utilisée par votre Service Cloud alloué la première fois que vous déployez sur un emplacement vide (production ou intermédiaire.) Si vous supprimez le déploiement pour l’emplacement, l’adresse IP est publié par Azure et les déploiements futurs vers l’emplacement peuvent bénéficier d’une nouvelle adresse IP.
>
> Pratique, l’adresse IP d’un emplacement de déploiement donné (production ou intermédiaire) est conservée lorsque vous échangez entre intermédiaire et les déploiements de production ou une mise à niveau en place d’un déploiement existant. Pour plus d’informations sur l’exécution de ces actions, voir [comment gérer les services en nuage](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Ajouter un enregistrement CNAME pour votre domaine personnalisé

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé en utilisant les outils fournis par votre bureau d’enregistrement. Chaque registrar a une méthode similaire, mais légèrement différente de spécification d’un enregistrement CNAME, mais les concepts sont identiques.

1. Utilisez une des méthodes suivantes pour rechercher la **. cloudapp.net** nom de domaine affecté à votre service cloud.

    * Connectez-vous au [portail classique Azure], sélectionnez votre service cloud, sélectionnez **tableau de bord**, puis recherchez l’entrée de **L’URL du Site** dans la section **coup de œil** .
    
        ![section coup de œil rapide affichant l’URL du site][csurl]
    
        **OR**  
    
    * Installer et configurer [Azure Powershell](../powershell-install-configure.md)et ensuite utiliser la commande suivante :
        
        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Enregistrer le nom de domaine utilisé dans l’URL renvoyée par des méthodes, car vous en aurez besoin lorsque vous créez un enregistrement CNAME.

1.  Connectez-vous au site Web de votre bureau d’enregistrement DNS et accédez à la page de gestion DNS. Recherchez les liens ou les zones du site comme **Nom de domaine**, de **DNS**ou de **Gestion des noms de serveur**.

2.  À présent la trouver l’endroit où vous pouvez sélectionner ou entrez de CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés. Vous recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

3.  Vous devez également fournir l’alias de domaine ou un sous-domaine pour l’enregistrement CNAME, tels que **www** si vous voulez créer un alias pour **www.customdomain.com**. Si vous voulez créer un alias pour le domaine racine, il peut être répertoriée en tant que la '**@**' symbole dans les outils de votre bureau d’enregistrement DNS.

4. Ensuite, vous devez fournir un nom d’hôte canonique, ce qui correspond à votre domaine d’application **cloudapp.net** dans ce cas.

Par exemple, l’enregistrement CNAME suivant transmet tout le trafic de **www.contoso.com** vers **contoso.cloudapp.net**, le nom de domaine personnalisé de votre application déployée :

| Nom d’hôte/alias/sous-domaine | Domaine canonique     |
| ------------------------- | -------------------- |
| www                       | Contoso.cloudapp.NET |

Visiteur **www.contoso.com** verront jamais l’hôte true (contoso.cloudapp.net), afin que le processus de transfert est invisible à l’utilisateur final.

> [AZURE.NOTE]
> L’exemple ci-dessus ne s’applique qu’aux circulations en le sous-domaine **www** . Dans la mesure où vous ne pouvez pas utiliser des caractères génériques avec des enregistrements CNAME, vous devez créer un CNAME pour chaque domaine/sous-domaine. Si vous voulez rediriger le trafic de sous-domaine, tel que \*. contoso.com, à votre adresse de cloudapp.net, vous pouvez configurer une entrée de **Redirection d’URL** ou **URL suivante** dans vos paramètres DNS, ou créez un enregistrement A.


## <a name="add-an-a-record-for-your-custom-domain"></a>Ajouter un enregistrement A pour votre domaine personnalisé

Pour créer un enregistrement A, vous devez tout d’abord rechercher l’adresse IP virtuelle de votre service cloud. Puis ajoutez une nouvelle entrée dans la table DNS pour votre domaine personnalisé en utilisant les outils fournis par votre bureau d’enregistrement. Chaque registrar a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement A, mais les concepts sont identiques.

1. Utilisez une des méthodes suivantes pour obtenir l’adresse IP de votre service cloud.
    
    * Connectez-vous au [portail classique Azure], sélectionnez votre service cloud, sélectionnez **tableau de bord**, puis recherchez l’entrée **d’adresse IP virtuel Public (VIP)** dans la section **coup de œil** .
    
        ![section coup de œil rapide montrant la VIP][vip]
    
        **OR**  
    
    * Installer et configurer [Azure Powershell](../powershell-install-configure.md)et ensuite utiliser la commande suivante :
    
        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Si vous avez plusieurs points de terminaison associés à votre service cloud, vous recevrez plusieurs lignes contenant l’adresse IP, mais toutes doivent afficher la même adresse.
    
    Enregistrer l’adresse IP, car vous en aurez besoin lors de la création d’un enregistrement A.

1.  Connectez-vous au site Web de votre bureau d’enregistrement DNS et accédez à la page de gestion DNS. Recherchez les liens ou les zones du site comme **Nom de domaine**, de **DNS**ou de **Gestion des noms de serveur**.

2.  À présent la trouver l’endroit où vous pouvez sélectionner ou entrer un enregistrement. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés.

3. Sélectionnez ou entrez le domaine ou le sous-domaine qu’utilisera cet enregistrement A. Par exemple, sélectionnez **www** si vous voulez créer un alias pour **www.customdomain.com**. Si vous voulez créer une entrée de caractères génériques pour tous les sous-domaines, entrez «__*__». Couvre tous les sous-domaines tels que **mail.customdomain.com**et **login.customdomain.com** **www.customdomain.com**.

    Si vous souhaitez créer un enregistrement A pour le domaine racine, il peut être répertoriée en tant que la '**@**' symbole dans les outils de votre bureau d’enregistrement DNS.

4. Entrez l’adresse IP de votre service cloud dans le champ fourni. Associez l’entrée de domaine utilisée dans l’enregistrement A avec l’adresse IP du déploiement de votre service cloud.

Par exemple, suit Qu'un enregistrement transmet tout le trafic de **contoso.com** vers **137.135.70.239**, l’adresse IP de votre application déployée :

| Nom d’hôte/sous-domaine | Adresse IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |



Cet exemple illustre la création d’un enregistrement A pour le domaine racine. Si vous souhaitez créer une entrée de caractères génériques pour couvrir tous les sous-domaines, vous devez entrer «__*__» en tant que le sous-domaine.

>[AZURE.WARNING]
>Adresses IP dans Azure sont dynamiques par défaut. Vous souhaiterez probablement utiliser une [adresse IP réservée](../virtual-network/virtual-networks-reserved-public-ip.md) pour vous assurer que votre adresse IP ne change pas.

## <a name="next-steps"></a>Étapes suivantes

* [Comment gérer les Services en nuage](cloud-services-how-to-manage.md)
* [Comment mapper un CDN contenu à un domaine personnalisé](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuration générale de votre service cloud](cloud-services-how-to-configure.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate.md).




[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portail classique Azure]: https://manage.windowsazure.com
[Validate Custom Domain dialog box]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
[vip]: ./media/cloud-services-custom-domain-name/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name/csurl.png
 
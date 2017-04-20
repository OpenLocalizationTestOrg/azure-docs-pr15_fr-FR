<properties 
   pageTitle="Zones DNS et enregistrements | Microsoft Azure" 
   description="Vue d’ensemble de prise en charge pour l’hébergement DNS zones et des enregistrements dans le système DNS de Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>Enregistrements et zones DNS

Cette page explique les concepts fondamentaux des domaines, zones DNS et les enregistrements DNS et jeux d’enregistrements et comment ils sont pris en charge dans le système DNS Azure.

## <a name="domain-names"></a>Noms de domaine
 
Le système DNS est une hiérarchie de domaines. La hiérarchie commence à partir du domaine « root », dont le nom est simplement '**.**'.  Dessous proviennent des domaines de niveau supérieur, telles que « com », « net », « organisation », 'uk' ou « jp ».  Dessous de ces derniers sont des domaines de deuxième niveau, tels que « org.uk » ou « co.jp ». Les domaines dans la hiérarchie DNS sont globalement distribués, hébergé par les serveurs de noms DNS dans le monde entier.

Un bureau d’enregistrement du nom de domaine est une organisation qui vous permet d’acheter un nom de domaine, tel que « contoso.com ».  Acheter un nom de domaine vous donne la possibilité de contrôler la hiérarchie DNS sous ce nom, par exemple permettant de diriger le nom « www.contoso.com » à votre site web. Le bureau d’enregistrement peut héberger le domaine dans sa propre serveurs de noms de votre part, ou vous pouvez également spécifier des serveurs de noms de remplacement.

DNS Azure fournit une infrastructure de serveur de noms globalement distribué, haute disponibilité, que vous pouvez utiliser pour héberger votre domaine. En hébergeant vos domaines dans Azure DNS, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, API, outils, facturation et prise en charge en tant que les autres services Azure.

Azure DNS ne prend pas actuellement en charge l’achat de noms de domaine. Si vous souhaitez acheter un nom de domaine, vous devez utiliser un bureau d’enregistrement du nom de domaine tiers. Le bureau d’enregistrement en général des frais une petite annuel. Les domaines peuvent ensuite être hébergés dans le système DNS Azure pour la gestion des enregistrements DNS. Pour plus d’informations, voir [délégué un domaine à Azure DNS](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zones DNS 

Une zone DNS est utilisée pour héberger les enregistrements DNS pour un domaine particulier. Pour démarrer l’hébergement de votre domaine dans le système DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. 

Par exemple, le domaine « contoso.com » peut contenir plusieurs enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web).

Lorsque vous créez une zone DNS dans DNS Azure, le nom de la zone doit être unique dans le groupe de ressources. Le même nom de la zone peut être réutilisé dans un groupe de ressources différent ou un autre abonnement Azure. L’emplacement dans lequel plusieurs zones partagent le même nom, chaque instance est affectée adresses serveur de noms différents. Qu’un seul ensemble d’adresses peut être configuré avec le bureau d’enregistrement du nom de domaine.

>[AZURE.NOTE] Vous n’êtes pas obligé de posséder un nom de domaine pour créer une zone DNS avec ce nom de domaine dans le système DNS Azure. Toutefois, vous n’avez pas besoin du domaine pour configurer les serveurs de noms DNS Azure en tant que les serveurs de noms corrects pour le nom de domaine de domaines nom appartient.

Pour plus d’informations, voir [délégué un domaine à Azure DNS](dns-domain-delegation.md).

## <a name="dns-records"></a>Enregistrements DNS

### <a name="record-types"></a>Types d’enregistrements

Chaque enregistrement DNS a un nom et un type. Enregistrements sont organisées en différents types selon les données qu’ils contiennent. La plus courante est de type « A » un enregistrement, qui associe un nom à une adresse IPv4. Un autre type courant est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

DNS Azure prend en charge tous les types d’enregistrements DNS courants : A, AAAA, CNAME, MX, NS, PTR, SOA, SRV et TXT.

### <a name="record-names"></a>Noms d’enregistrement

Dans le système DNS Azure, les enregistrements sont spécifiées en utilisant des noms relatives. Un nom de domaine *complet* (FQDN) inclut le nom de la zone, tandis que n’est pas le cas d’un nom *relatif* . Par exemple, le nom de l’enregistrement relatif « www » dans la zone « contoso.com » donne le nom complet enregistrement « www.contoso.com ».

Un enregistrement *sommet* est un enregistrement DNS auprès de la racine (ou le *sommet*) d’une zone DNS. Par exemple, dans la zone DNS « contoso.com », un enregistrement sommet comporte le nom complet « contoso.com » (il est parfois appelé un domaine *naked* ).  Par convention, le nom relatif '@' est utilisé pour créer des enregistrements sommet.

### <a name="record-sets"></a>Jeux d’enregistrements
Parfois, vous devez créer plusieurs enregistrements DNS avec un nom et type donnés. Par exemple, supposons que le site web « www.contoso.com » est hébergé sur deux différentes adresses IP. Le site Web nécessite deux enregistrements A, une pour chaque adresse IP. Il s’agit d’un exemple d’un jeu d’enregistrements :

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

DNS Azure gère les enregistrements DNS à l’aide de *jeux d’enregistrement*. Un jeu d’enregistrements (également appelé un *ressource* jeu d’enregistrements) est l’ensemble des enregistrements DNS dans une zone ayant le même nom et du même type. La plupart des jeux d’enregistrements contiennent un seul enregistrement, mais exemples comme celui-ci, dont un jeu d’enregistrements contient plusieurs enregistrements, ne sont pas rares.

Par exemple, supposons que vous avez déjà créé un enregistrement A « www » dans la zone « contoso.com », en pointant sur l’adresse IP adresse « 134.170.185.46 » (le premier enregistrement ci-dessus).  Pour créer le second enregistrement vous souhaitez ajouter cet enregistrement pour le jeu d’enregistrements existant, au lieu de créer un nouveau jeu d’enregistrements.

Les types d’enregistrements source de nom et CNAME sont exceptions. Les normes DNS ne permettent pas plusieurs enregistrements avec le même nom pour les types suivants, et donc ces jeux d’enregistrements peut contenir uniquement un enregistrement unique.

### <a name="time-to-live"></a>Durée de vie

La durée de vie, ou TTL, indique la durée pendant laquelle chaque enregistrement est mis en cache par les clients avant de nouveau interrogée. Dans l’exemple ci-dessus, la durée de vie est 3 600 secondes ou 1 heure.

Dans le système DNS Azure, la durée de vie spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement, pour la même valeur est utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.  Vous pouvez spécifier une valeur de durée de vie entre 1 et 2 147 483 647 secondes.

### <a name="wildcard-records"></a>Enregistrements génériques

DNS Azure prend en charge les [enregistrements génériques](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Enregistrements génériques sont retournés en réponse à une requête avec un nom correspondant (sauf s’il existe une correspondance aperçue à partir d’un jeu d’enregistrements non génériques). Jeux d’enregistrements génériques est pris en charge pour tous les types d’enregistrements à l’exception NS et SOA.  

Pour créer un jeu d’enregistrements génériques, utilisez le nom du jeu d’enregistrements '\*'. Par ailleurs, vous pouvez également utiliser un nom comportant «\*« comme la plus à gauche étiquette, par exemple, »\*.foo ».

### <a name="cname-records"></a>Enregistrements CNAME

Jeux d’enregistrements CNAME ne peut pas coexister avec d’autres jeux d’enregistrements du même nom. Par exemple, vous ne pouvez pas créer un enregistrement CNAME définie avec le nom relatif « www » et un enregistrement A avec le nom « www » relatif en même temps.

Étant donné que le sommet zone (nom = ‘@’) contient toujours le NS et SOA jeux qui ont été créés lors de la zone de la création d’enregistrements, vous ne pouvez pas créer un enregistrement CNAME définir au sommet zone.

Ces contraintes proviennent les normes DNS et ne sont pas des limites de DNS Azure.

### <a name="ns-records"></a>Enregistrements de serveur de noms

Un jeu d’enregistrements de serveur de noms est créé automatiquement au sommet de chaque zone (nom = ‘@’), et sont supprimés automatiquement lorsque la zone est supprimée (il ne peut pas être supprimé séparément).  Vous pouvez modifier la durée de vie de ce jeu d’enregistrements, mais vous ne pouvez pas modifier les enregistrements, qui sont préconfigurés pour faire référence aux serveurs de noms DNS Azure affectées à la zone.

Vous pouvez créer et supprimer les autres enregistrements de serveur de noms dans la zone, pas au sommet zone.  Cela vous permet de configurer des zones enfant (voir [délégation sous-domaines dans Azure DNS](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Enregistrements SOA

Un jeu d’enregistrements SOA est créé automatiquement au sommet de chaque zone (nom = ‘@’), et sont supprimés automatiquement lorsque la zone est supprimée.  Enregistrements SOA ne peuvent pas être créés ou supprimés séparément. 

Vous pouvez modifier toutes les propriétés de l’enregistrement SOA à l’exception de la propriété « hôte », qui est préconfigurée de manière à faire référence au nom du serveur de noms principal fourni par le système DNS Azure.

### <a name="spf-records"></a>Enregistrements SPF

Enregistrements SPF (Sender Policy Framework) de l’expéditeur sont utilisés pour spécifier les serveurs de messagerie sont autorisés à envoyer un message électronique au nom d’un nom de domaine donnée.  Une configuration correcte des enregistrements SPF est importante empêcher les destinataires marquer votre courrier comme « indésirables ».

Les RFC DNS introduite à l’origine d’un nouveau type d’enregistrement « SPF » pour prendre en charge ce scénario. Pour prendre en charge les serveurs de noms plus anciens, ils autorisé également l’utilisation du type d’enregistrement TXT pour spécifier les enregistrements SPF.  Cette ambiguïté conduit à confusion, ce qui a été résolue par [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Cela précisé que les enregistrements SPF doivent être créés uniquement à l’aide du type d’enregistrement TXT et déconseillée le type d’enregistrement SPF. 

**Les enregistrements SPF sont prises en charge par le système DNS Azure et doivent être créés en utilisant le type d’enregistrement TXT.** Le type d’enregistrement SPF obsolète n’est pas pris en charge. Lors de [l’importation d’un serveur DNS de la zone fichier](dns-import-export.md), tous les enregistrements SPF en utilisant le type d’enregistrement SPF sont converties en le type d’enregistrement TXT. 

### <a name="srv-records"></a>Enregistrements SRV

[Enregistrements SRV](https://en.wikipedia.org/wiki/SRV_record) sont utilisés par les différents services pour spécifier des emplacements de serveur. Lorsque vous spécifiez un enregistrement SRV dans Azure DNS :

- Le *service* et le *protocole* doivent être indiquées dans le cadre du nom du jeu d’enregistrements, le préfixe des traits de soulignement.  Par exemple, «\_sip. \_tcp.name ».  Un enregistrement au sommet zone, il est inutile de spécifier '@' dans le nom de l’enregistrement, simplement utiliser le service et protocol, par exemple, «\_sip. \_tcp ».
- La *priorité*, *épaisseur*, *port*et *cible* sont spécifiées en tant que paramètres de chaque enregistrement dans le jeu d’enregistrements. 

## <a name="tags-and-metadata"></a>Balises et des métadonnées

### <a name="tags"></a>Balises
Les balises sont une liste de paires nom-valeur et sont utilisées par le Gestionnaire de ressources Azure aux ressources étiquette.  Le Gestionnaire de ressources Azure utilise des balises pour activer les affichages filtrés de votre facture Azure et vous permet également de définir une stratégie dans laquelle les balises sont requis. Pour plus d’informations sur les balises, voir [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

DNS Azure prend en charge l’utilisation de balises de Azure le Gestionnaire de ressources sur les ressources de zone DNS.  Il ne prend pas en charge balises sous jeux d’enregistrements DNS.

### <a name="metadata"></a>Métadonnées

Comme alternative à balises de jeu d’enregistrements, Azure DNS prend en charge annotées jeux d’enregistrements à l’aide de 'metadata'.  Similaires aux balises, métadonnées vous permet d’associer paires nom-valeur à chaque jeu d’enregistrements.  Cela peut être utile, par exemple pour indiquer l’objectif de chaque jeu d’enregistrements.  Contrairement aux balises, métadonnées ne peuvent pas être utilisées pour fournir un affichage filtré votre facture Azure et ne peut pas être indiquée dans une stratégie de gestionnaire de ressources Azure.

## <a name="limits"></a>Limites

Les limites par défaut suivantes s’appliquent lorsque vous utilisez Azure DNS :

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser DNS Azure, découvrez comment [créer une zone DNS](dns-getstarted-create-dnszone-portal.md) et [créer des enregistrements DNS](dns-getstarted-create-recordset-portal.md).
- Pour migrer une zone DNS, découvrez comment [Importer et fichier de zone DNS](dns-import-export.md).

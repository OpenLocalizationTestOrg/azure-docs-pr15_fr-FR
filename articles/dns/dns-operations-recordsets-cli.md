<properties
   pageTitle="Gérer des jeux d’enregistrements DNS et des enregistrements DNS Azure à l’aide de Azure infrastructure du langage commun | Microsoft Azure"
   description="Gestion des jeux d’enregistrements DNS et enregistrements DNS Azure lors de l’hébergement de votre domaine sur Azure DNS. Toutes les commandes d’infrastructure du langage commun pour les opérations sur les jeux d’enregistrements et des enregistrements."
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
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Gestion des enregistrements DNS et des jeux d’enregistrements à l’aide d’infrastructure du langage commun


> [AZURE.SELECTOR]
- [Portail Azure](dns-operations-recordsets-portal.md)
- [Azure infrastructure du langage commun](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Cet article vous explique comment gérer les jeux d’enregistrements et des enregistrements pour votre zone DNS à l’aide de l’interface de ligne entre les plates-formes Azure (commande).

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et des enregistrements DNS. Un jeu d’enregistrements est un ensemble d’enregistrements dans une zone qui ont le même nom et sont du même type. Pour plus d’informations, voir [enregistrements et jeux d’enregistrements compréhension](dns-getstarted-create-recordset-cli.md).


## <a name="configure-the-cross-platform-azure-cli"></a>Configurer l’infrastructure du langage commun disponibilité sur plusieurs plateformes Azure

Azure DNS est un service Gestionnaire de ressources seule Azure. Il n’a pas une API de gestion du Service Azure. Assurez-vous que l’infrastructure du langage commun Azure est configuré pour utiliser le mode directeur des ressources, à l’aide de la `azure config mode arm` commande.

Si vous voyez **erreur : « dns » n’est pas une commande azure**, c’est probablement parce que vous utilisez Azure infrastructure du langage commun en mode de gestion des services Azure, et non dans le mode Gestionnaire de ressources.

## <a name="create-a-new-record-set-and-record"></a>Créer un nouveau jeu d’enregistrements et enregistrement

Pour créer un nouveau jeu dans le portail Azure d’enregistrements, voir [créer un jeu d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Récupérer un jeu d’enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `azure network dns record-set show`. Spécifier le groupe de ressources, nom de la zone, jeu d’enregistrements relative nom et type d’enregistrement. Utilisez l’exemple ci-dessous, remplacer les valeurs par la vôtre.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Liste de jeux d’enregistrements

Vous pouvez répertorier tous les enregistrements dans une zone DNS à l’aide de la `azure network dns record-set list` commande. Vous devez spécifier le nom de groupe de ressources et le nom de la zone.

### <a name="to-list-all-record-sets"></a>Pour répertorier tous les jeux d’enregistrements

Cet exemple renvoie tous les jeux d’enregistrements, quel que soit le nom ou type d’enregistrement :

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>Pour les jeux d’enregistrements liste d’un type de donnée

Cet exemple renvoie tous les jeux d’enregistrements qui correspondent au type d’enregistrement donné (dans ce cas, « A » enregistrements) :

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Ajouter un enregistrement à un jeu d’enregistrements

Vous ajoutez des enregistrements à jeux d’enregistrements à l’aide de la `azure network dns record-set add-record`commande. Les paramètres pour ajouter des enregistrements à un jeu d’enregistrements varient selon le type de l’enregistrement est définie. Par exemple, lorsque vous utilisez un jeu d’enregistrements de type « A », vous pouvez spécifier seulement les enregistrements avec le paramètre `-a <IPv4 address>`.

Pour créer un jeu d’enregistrements, utilisez la `azure network dns record-set create`commande. Spécifier le groupe de ressources, nom de la zone, jeu d’enregistrements nom relatif, type d’enregistrement et temps de vie (TTL). Si la `--ttl` paramètre n’est pas définie, la valeur (en secondes) par défaut à quatre.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Après avoir créé le jeu d’enregistrements « A », ajoutez l’adresse IPv4 à l’aide de la `azure network dns record-set add-record`commande.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Les exemples ci-dessous illustrent comment créer un jeu d’enregistrements de chaque type d’enregistrement. Chaque jeu d’enregistrements contient un seul enregistrement.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Mettre à jour un enregistrement dans un jeu d’enregistrements

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Pour ajouter une autre adresse IP (1.2.3.4) à un enregistrement « A » existant définie (« www ») :

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Pour supprimer une valeur existante à partir d’un jeu d’enregistrements
Utiliser `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Supprimer un enregistrement d’un jeu d’enregistrements

Enregistrements peuvent être supprimés à partir d’un enregistrement à l’aide de la valeur `azure network dns record-set delete-record`. L’enregistrement est supprimé doit être une correspondance exacte avec un enregistrement existant sur tous les paramètres.

Suppression du dernier enregistrement d’un jeu d’enregistrements ne supprime pas le jeu d’enregistrements. Pour plus d’informations, voir la section de cet article, appelé [Supprimer un jeu d’enregistrements](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Supprimer un enregistrement AAAA à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Supprimer un enregistrement CNAME à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Supprimer un enregistrement MX d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Supprimer un enregistrement de serveur de noms de jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Supprimer un enregistrement PTR à partir d’un jeu d’enregistrements
Dans ce cas « mon-arpa-zone.com' représente la zone ARPA représentant votre plage IP.  Chaque enregistrement PTR dans cette zone correspond à une adresse IP au sein de cette plage IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Supprimer un enregistrement SRV d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Supprimer un enregistrement TXT à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Supprimer un jeu d’enregistrements

Jeux d’enregistrements peut être supprimés à l’aide de la `Remove-AzureRmDnsRecordSet` applet de commande. Vous ne pouvez pas supprimer l’architecture orientée services et jeux d’enregistrement NS au sommet zone (nom = "@") qui ont été créées automatiquement lorsque la zone a été créée. Ils seront supprimés automatiquement si la zone est supprimée.

Dans l’exemple suivant, l’enregistrement « A » définir « test a » a été supprimé de la zone DNS « contoso.com » :

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

Le commutateur facultatif *- q* peut être utilisé pour supprimer l’invite de confirmation.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le système DNS Azure, voir [vue d’ensemble de DNS Azure](dns-overview.md). Pour plus d’informations sur l’automatisation DNS, voir [zones DNS création et enregistrement jeux en utilisant le Kit de développement .NET](dns-sdk.md).

Si vous voulez utiliser les enregistrements DNS inverses, voir [comment gérer les enregistrements DNS inverses pour vos services à l’aide de l’infrastructure du langage commun Azure](dns-reverse-dns-record-operations-cli.md).

<properties
   pageTitle="Importer et exporter un fichier de zone domaine DNS Azure à l’aide d’infrastructure du langage commun | Microsoft Azure"
   description="Découvrez comment importer et exporter un fichier de zone DNS Azure DNS à l’aide de Azure infrastructure du langage commun"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importer et exporter un fichier de zone DNS à l’aide de l’infrastructure du langage commun Azure


Cet article vous guidera tout au long comment importer et exporter des fichiers de zone DNS pour DNS Azure à l’aide de l’infrastructure du langage commun Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introduction à la migration de zone DNS

Un fichier de zone DNS est un fichier texte qui contient des détails de chaque enregistrement de nom de domaine (DNS) dans la zone. Il respecte un format standard, rend appropriée pour le transfert des enregistrements DNS entre les systèmes de DNS. À l’aide d’un fichier de zone est un moyen rapide, fiable et pratique pour transférer une zone DNS ou disparition Azure DNS.

DNS Azure prend en charge l’importation et l’exportation de fichiers de zone à l’aide de l’interface de ligne Azure (commande). L’infrastructure du langage commun Azure est un outil de ligne de commande de disponibilité sur plusieurs plateformes utilisé pour gérer les services Azure. Il est disponible pour les plateformes Windows, Mac et Linux à partir de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/). Prise en charge plates-formes est particulièrement important pour importer et exporter des fichiers de zone, car le logiciel de serveur de nom plus courants, [lier](https://www.isc.org/downloads/bind/), est généralement exécuté sur Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtenir de votre fichier de zone DNS existant

Avant d’importer un fichier de zone DNS dans DNS Azure, vous devrez obtenir une copie du fichier de zone. La source de ce fichier dépend de l’endroit où la zone DNS est hébergée actuellement.

- Si votre zone DNS est hébergé par un service partenaire (par exemple, un enregistrement de domaines, dédié fournisseur d’hébergement DNS ou fournisseur de remplacement cloud), ce service doit fournir la possibilité de télécharger le fichier de zone DNS.

- Si votre zone DNS est hébergé sur DNS Windows, le dossier par défaut pour les fichiers de zone est **%systemroot%\system32\dns**. Le chemin d’accès complet à chaque fichier de zone affiche également sous l’onglet **Général** de la console de gestion du service DNS.

- Si votre zone DNS est hébergé à l’aide de liaison, l’emplacement du fichier de zone pour chaque zone est spécifié dans le de fichier de configuration de liaison **named.conf**.

**Utilisation des fichiers de zone auprès de GoDaddy**<BR>
Fichiers zone téléchargés à partir de GoDaddy ont une mise en forme légèrement non standard. Vous devez résoudre ce problème avant d’importer ces fichiers zone dans Azure DNS. Noms DNS dans la section RData de chaque enregistrement DNS sont spécifiées en tant que noms complets, mais elle n’a pas un arrêt «. » Cela signifie qu’ils sont interprétés par d’autres systèmes DNS en tant que noms relatifs. Vous devez modifier le fichier de zone pour ajouter l’arrêt «. » à leur nom avant de les importer dans Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importer un fichier de zone DNS dans DNS Azure


Importer un fichier de zone créer une nouvelle zone dans Azure DNS s’il n’existe pas déjà. Si la zone existe déjà, les jeux d’enregistrements dans le fichier de zone doivent être fusionnées avec les jeux d’enregistrement existants.

### <a name="merge-behavior"></a>Comportement de fusion

- Par défaut, existants et nouveaux jeux d’enregistrements sont fusionnés. Enregistrements identiques au sein d’un jeu d’enregistrements fusionné sont des doublons.

- Par ailleurs, en spécifiant la `--force` option, le processus d’importation remplacent existantes jeux d’enregistrements avec les nouveaux jeux d’enregistrements. Existant ensembles d’enregistrements qui n’ont pas d’un enregistrement correspondant dans le fichier de zone importées ne seront pas supprimés.

- Lors de la fusion de jeux d’enregistrements, la durée de vie de jeux d’enregistrements préexistants est utilisée. Lorsque `--force` est utilisé, la valeur TTL du nouveau jeu d’enregistrements est utilisée.

- Démarrer des paramètres autorité (SOA) (à l’exception de `host`) sont toujours pris à partir du fichier de zone importées, quel que soit `--force` est utilisé. De même, pour l’enregistrement de serveur de nom défini au sommet zone, la durée de vie est toujours considérée du fichier de zone importées.

- Un enregistrement CNAME importé ne remplace pas un enregistrement CNAME existant portant le même nom, à moins que la `--force` paramètre est spécifié.

- Lorsqu’un conflit se produit entre un enregistrement CNAME et un autre enregistrement de la même nom mais de type différent (quelle que soit la qui est existant ou nouveau), l’enregistrement existant est conservé. Il s’agit indépendamment de l’utilisation de `--force`.

### <a name="additional-information-about-importing"></a>Informations supplémentaires sur l’importation

Les notes suivantes fournissent des détails techniques supplémentaires sur la zone processus d’importation.

- La `$TTL` directive est facultative, et il est pris en charge. Lorsqu’aucun `$TTL` directive est remplacé par, les enregistrements sans une durée de vie explicite sont importée définir une durée de vie de 3 600 secondes par défaut. Lorsque deux enregistrements dans le même jeu d’enregistrements spécifient TTLs différents, la valeur inférieure est utilisée.

- La `$ORIGIN` directive est facultative, et il est pris en charge. Lorsqu’aucun `$ORIGIN` est défini, la valeur par défaut utilisée est le nom de la zone comme indiqué dans la ligne de commande (ainsi que l’arrêt «. »).

- La `$INCLUDE` et `$GENERATE` directives ne sont pas prises en charge.

- Ces types d’enregistrements sont prises en charge : A, AAAA, CNAME, MX, NS, SOA, SRV et TXT.

- L’enregistrement SOA est créé automatiquement par le système DNS Azure lorsqu’une zone est créée. Lorsque vous importez un fichier de zone, tous les paramètres SOA proviennent de la zone fichier *à l’exception de* la `host` paramètre. Ce paramètre utilise la valeur fournie par le système DNS Azure. C’est parce que ce paramètre doit faire référence au serveur de noms principal fourni par le système DNS Azure.

- L’enregistrement de serveur de nom défini au sommet zone est également créé automatiquement par le système DNS Azure lorsque la zone est créée. Uniquement la durée de vie de ce jeu d’enregistrements est importée. Ces enregistrements contiennent les noms des serveurs nom fournis par le système DNS Azure. Les données d’enregistrement ne sont pas remplacées par les valeurs contenues dans le fichier de zone importées.

- Au cours de présentation publique, Azure DNS prend en charge uniquement les enregistrements TXT unique chaîne. Enregistrements TXT chaîne multiple seront concaténées et tronqués de façon à 255 caractères.

### <a name="cli-format-and-values"></a>Valeurs et le format de l’infrastructure du langage commun


Le format de la commande Azure infrastructure du langage commun pour importer une zone DNS est la suivante :<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valeurs :

- `<resource group>`est le nom du groupe de ressources pour la zone dans le système DNS Azure.
- `<zone name>`est le nom de la zone.
- `<zone file name>`est le nom/chemin d’accès du fichier de zone à importer.

Si une zone portant ce nom n’existe pas dans le groupe de ressources, il sera créé pour vous. Si la zone existe déjà, les jeux d’enregistrements importées sont fusionnées avec les jeux d’enregistrements existants. Pour remplacer les jeux d’enregistrements existants, utilisez la `--force` option.

Pour vérifier le format d’un fichier de zone sans réellement l’importer, utilisez la `--parse-only` option.

### <a name="step-1-import-a-zone-file"></a>Étape 1. Importer un fichier de zone

Pour importer un fichier de zone pour la zone **contoso.com**.

1. Connectez-vous à votre abonnement Azure à l’aide de l’infrastructure du langage commun Azure.

        azure login

2. Sélectionnez l’abonnement dans lequel vous voulez créer votre nouvelle zone DNS.

        azure account set <subscription name>

3. Azure DNS est un service Gestionnaire de ressources seule Azure, afin de l’infrastructure du langage commun Azure doit être basculé en mode Gestionnaire de ressources.

        azure config mode arm

4. Avant d’utiliser le service DNS Azure, vous devez enregistrer votre abonnement pour utiliser le fournisseur de ressources Microsoft.Network. (Il s’agit une opération unique pour chaque abonnement).

        azure provider register Microsoft.Network

5. Si vous n’avez pas encore, vous devez également créer un groupe de ressources du Gestionnaire de ressources.

        azure group create myresourcegroup westeurope

6. Pour importer la zone **contoso.com** à partir du fichier **contoso.com.txt** dans une zone DNS dans le groupe de ressources **myresourcegroup**, exécutez la commande `azure network dns zone import`.<BR>Cette commande charge le fichier de zone et l’analyser. La commande s’exécute une série de commandes sur le service DNS Azure pour créer la zone et de l’enregistrement toutes définit dans la zone. La commande signale également la progression dans la fenêtre de la console, ainsi que les erreurs et les avertissements. Comme jeux d’enregistrements est créés dans la série, il peut prendre quelques minutes pour importer un fichier de zone volumineux.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Étape 2. Vérifiez que la zone

Pour vérifier la zone DNS après avoir importé le fichier, vous pouvez utiliser l’une des méthodes suivantes :

- Vous pouvez répertorier les enregistrements à l’aide de la commande suivante Azure infrastructure du langage commun.

        azure network dns record-set list myresourcegroup contoso.com

- Vous pouvez répertorier les enregistrements à l’aide de l’applet de commande PowerShell `Get-AzureRmDnsRecordSet`.

- Vous pouvez utiliser `nslookup` pour vérifier la résolution de noms pour les enregistrements. Étant donné que la zone n’est pas encore déléguée, vous devez spécifier les serveurs de noms DNS Azure correctes de manière explicite. L’exemple ci-dessous montre comment récupérer les noms des serveurs affectés à la zone Nom. INFORMATIQUE également montre comment interroger l’enregistrement « www » à l’aide de `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Étape 3. Mise à jour DNS délégation

Une fois que vous avez vérifié que la zone a été importée correctement, vous devrez mettre à jour la délégation DNS pour pointer vers les serveurs de noms DNS Azure. Pour plus d’informations, voir l’article [mettre à jour la délégation DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exporter un fichier de zone DNS à partir d’Azure DNS

Le format de la commande Azure infrastructure du langage commun pour importer une zone DNS est la suivante :<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valeurs :

- `<resource group>`est le nom du groupe de ressources pour la zone dans le système DNS Azure.
- `<zone name>`est le nom de la zone.
- `<zone file name>`est le nom/chemin d’accès du fichier de zone à exporter.

Sous l’importation de la zone, vous tout d’abord vous avez besoin pour vous connecter, sélectionnez votre abonnement, puis configurer l’infrastructure du langage commun Azure pour utiliser le mode Gestionnaire de ressources.

### <a name="to-export-a-zone-file"></a>Pour exporter un fichier de zone


1. Connectez-vous à votre abonnement Azure à l’aide de l’infrastructure du langage commun Azure.

        azure login

2. Sélectionnez l’abonnement dans lequel vous voulez créer votre nouvelle zone DNS.

        azure account set <subscription name>

3. Azure DNS est un service Gestionnaire de ressources seule Azure. L’infrastructure du langage commun Azure doit être basculé en mode Gestionnaire de ressources.

        azure config mode arm

4. Pour exporter l’existant Azure DNS zone **contoso.com** dans le groupe de ressources **myresourcegroup** vers le fichier **contoso.com.txt** (dans le dossier actif), exécutez `azure network dns zone export`. Cette commande appelle le service DNS Azure pour énumérer les ensembles d’enregistrements dans la zone et exporter les résultats dans un fichier de zone compatible avec liaison.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt


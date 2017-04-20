<properties
   pageTitle="À l’aide de DNS dynamique pour enregistrer les noms d’hôte"
   description="Cette page explique comment configurer le système DNS dynamique pour enregistrer des noms d’hôtes dans vos propres serveurs DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>À l’aide de DNS dynamique pour enregistrer des noms d’hôtes dans votre serveur DNS

[Azure fournit la résolution de noms](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour machines virtuelles (machines virtuelles) et les instances de rôle. Toutefois, lorsque votre résolution de noms doit vous limitez pas ceux fournis par Azure, vous pouvez fournir vos propres serveurs DNS. Cela vous donne la possibilité de personnaliser votre solution de DNS pour l’adapter à vos besoins spécifiques. Par exemple, vous devrez peut-être accéder à des ressources locales via votre contrôleur de domaine Active Directory.

Lorsque vos serveurs DNS personnalisés sont hébergés en tant que machines virtuelles Azure vous pouvez transférer des requêtes de nom d’hôte pour la même vnet à Azure pour résoudre les noms d’hôte. Si vous ne souhaitez pas utiliser cet itinéraire, vous pouvez enregistrer votre nom d’hôte machine virtuelle dans votre serveur DNS à l’aide de DNS dynamique.  Azure n’a la possibilité (par exemple, les informations d’identification) pour créer des enregistrements directement dans vos serveurs DNS, afin que les autres dispositions sont souvent nécessaires. Voici quelques scénarios courants des alternatives.

## <a name="windows-clients"></a>Clients Windows

Clients de Windows liés à un domaine non tentent de mises à jour DNS dynamique (DDNS) non sécurisées lors de leur initialisation ou lorsque son adresse IP change. Le nom DNS est le nom d’hôte ainsi que le suffixe DNS principal. Azure laisse le suffixe DNS principal vide, mais vous pouvez le définir dans la machine virtuelle, via [l’interface utilisateur](https://technet.microsoft.com/library/cc794784.aspx) ou [en utilisant automation](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Clients de Windows à un domaine enregistrer leurs adresses IP avec le contrôleur de domaine à l’aide de DNS dynamique sécurisée. Le processus de jonction de domaine définit le suffixe DNS principal sur le client et crée et conserve la relation d’approbation.

## <a name="linux-clients"></a>Clients Linux

Les clients Linux généralement ne s’enregistrent avec le serveur DNS au démarrage, ils supposer que celui-ci fait. Serveurs DHCP d’Azure n’ont pas la possibilité d’ou les informations d’identification pour enregistrer des enregistrements dans votre serveur DNS.  Vous pouvez utiliser un outil appelé *nsupdate*, qui est incluse dans le package de liaison, pour envoyer des mises à jour DNS dynamique. Étant donné que le protocole DNS dynamique normalisé, vous pouvez utiliser *nsupdate* même lorsque vous n’utilisez pas de liaison sur le serveur DNS.

Vous pouvez utiliser les crochets qui sont fournies par l’album pour créer et gérer l’entrée de nom d’hôte du serveur DNS. Pendant le cycle DHCP, le client exécute les scripts dans */etc/dhcp/dhclient-exit-hooks.d/*. Cela peut servir à enregistrer la nouvelle adresse IP à l’aide de *nsupdate*. Par exemple :

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

Vous pouvez également utiliser la commande *nsupdate* pour effectuer des mises à jour DNS dynamiques sécurisées. Par exemple, lorsque vous utilisez un serveur DNS lier, une paire clée publique-privée est [générée](http://linux.yyz.us/nsupdate/).  Le serveur DNS est [configuré](http://linux.yyz.us/dns/ddns-server.html) avec la partie de la clé publique afin qu’il peut vérifier la signature dans la demande. Vous devez utiliser l’option *-k* pour fournir que la paire de clé à *nsupdate* afin que le DNS dynamique mettre à jour la demande à signer.

Lorsque vous utilisez un serveur DNS Windows, vous pouvez utiliser l’authentification Kerberos avec le paramètre *g* dans *nsupdate* (non disponible dans la version Windows de *nsupdate*). Pour ce faire, utilisez *kinit* pour charger les informations d’identification (par exemple, à partir d’un [fichier keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Puis *nsupdate g* chercher les informations d’identification à partir du cache.

Si nécessaire, vous pouvez ajouter un suffixe de recherche DNS dans vos ordinateurs virtuels. Le suffixe DNS spécifié dans le fichier */etc/resolv.conf* . La plupart des Linux distros gérer automatiquement le contenu de ce fichier, généralement pas les modifier. Toutefois, vous pouvez ignorer le suffixe à l’aide *remplacent* commande de l’album. Pour ce faire, dans */etc/dhcp/dhclient.conf*, ajoutez :

        supersede domain-name <required-dns-suffix>;


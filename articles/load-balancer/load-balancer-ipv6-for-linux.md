<properties
    pageTitle="Configuration DHCPv6 pour les machines virtuelles Linux | Microsoft Azure"
    description="Comment configurer DHCPv6 pour les machines virtuelles Linux."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="le protocole IPv6, équilibrage de charge azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configuration DHCPv6 pour les machines virtuelles Linux

Certaines des images sur le marché Azure machine virtuelle Linux n’ont pas DHCPv6 configuré par défaut. Pour prendre en charge du protocole IPv6, DHCPv6 doit être configuré au sein de la distribution du système d’exploitation Linux que vous utilisez. Différentes versions de Linux ont différentes manières de configurer DHCPv6 parce qu’ils utilisent des packages différents.

>[AZURE.NOTE] Images SUSE Linux et CoreOS récentes sur le marché Azure ont été préconfigurés avec DHCPv6. Aucune modification supplémentaires n’est nécessaires pour utiliser ces images.

Ce document décrit comment activer DHCPv6 afin que votre machine virtuelle Linux Obtient une adresse IPv6.

>[AZURE.WARNING] Incorrectement modification de fichiers de configuration réseau peut entraîner perdre l’accès au réseau à votre ordinateur virtuel. Nous vous recommandons de tester vos modifications de configuration sur des systèmes hors production. Les instructions fournies dans cet article ont été testés sur les dernières versions des images sur le marché Azure Linux. Pour votre version spécifique de Linux pour obtenir des instructions plus détaillées, consultez la documentation.

## <a name="ubuntu"></a>Ubuntu

1. Modifier le fichier `/etc/dhcp/dhclient6.conf` et ajoutez la ligne suivante :

        timeout 10;

2. Modifier la configuration du réseau pour l’interface eth0 avec la configuration suivante :

    * Sur **Ubuntu 12.04 et 14.04**, modifiez le fichier`/etc/network/interfaces.d/eth0.cfg`
    * Sur **Ubuntu 16.04**, modifiez le fichier`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Renouveler adresse IPv6 :

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Modifier le fichier `/etc/dhcp/dhclient6.conf` et ajoutez la ligne suivante :

        timeout 10;

2. Modifier le fichier `/etc/network/interfaces` et ajoutez la configuration suivante :

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renouveler adresse IPv6 :

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Linux Oracle

1. Modifier le fichier `/etc/sysconfig/network` et ajoutez le paramètre suivant :

        NETWORKING_IPV6=yes

2. Modifier le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0` et ajoutez les deux paramètres suivants :

        IPV6INIT=yes
        DHCPV6C=yes

3. Renouveler adresse IPv6 :

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Images SLES et openSUSE récentes dans Azure ont été préconfigurés avec DHCPv6. Aucune modification supplémentaires n’est nécessaires pour utiliser ces images. Si vous avez une machine virtuelle basée sur une image SUSE personnalisée ou plus ancienne, procédez comme suit :

1. Installer le `dhcp-client` créer un package, si nécessaire :

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Modifier le fichier `/etc/sysconfig/network/ifcfg-eth0` et ajoutez le paramètre suivant :

        DHCLIENT6_MODE='managed'

3. Renouveler l’adresse IPv6 :

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 et openSUSE Leap

Images SLES et openSUSE récentes dans Azure ont été préconfigurés avec DHCPv6. Aucune modification supplémentaires n’est nécessaires pour utiliser ces images. Si vous avez une machine virtuelle basée sur une image SUSE personnalisée ou plus ancienne, procédez comme suit :

1. Modifier le fichier `/etc/sysconfig/network/ifcfg-eth0` et remplacer ce paramètre

        #BOOTPROTO='dhcp4'

    avec la valeur suivante :

        BOOTPROTO='dhcp'

2. Ajouter le paramètre suivant à `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renouveler l’adresse IPv6 :

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Images CoreOS récentes dans Azure ont été préconfigurés avec DHCPv6. Aucune modification supplémentaires n’est nécessaires pour utiliser ces images. Si vous avez une machine virtuelle basée sur une image CoreOS personnalisée ou plus ancienne, procédez comme suit :

1. Modifier le fichier`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renouveler l’adresse IPv6 :

    ```bash
    # sudo systemctl restart systemd-networkd
    ```

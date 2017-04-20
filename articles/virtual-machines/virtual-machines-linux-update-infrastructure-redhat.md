<properties
   pageTitle="Infrastructure de mises à jour chapeau rouge (RHUI) | Microsoft Azure"
   description="En savoir plus sur rouge chapeau mise à jour Infrastructure (RHUI) pour les instances de Red chapeau Enterprise Linux à la demande dans Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastructure chapeau rouge mises à jour (RHUI) pour les chapeau rouge à la demande Enterprise Linux machines virtuelles dans Azure

Machines virtuelles créées à partir des images rouge chapeau Enterprise Linux (RHEL) à la demande disponibles dans Azure Marketplace sont enregistrés pour accéder à la rouge chapeau mise à jour Infrastructure (RHUI) déployée dans Azure.  Les instances RHEL à la demande ont accès à un référentiel yum régionaux et peut recevoir des mises à jour incrémentielles.

La liste des référentiels yum, qui est gérée par RHUI, est configurée dans votre instance RHEL pendant la mise en service. Vous n’avez pas besoin de faire configuration supplémentaire - exécuter `yum update` une fois votre instance RHEL est prêt obtenir les dernières mises à jour.

> [AZURE.NOTE] Azure infrastructure RHUI a été récemment mis à jour (septembre 2016) et nécessite des modifications dans la configuration de vos instances RHEL existants pour l’accès à la RHUI Azure sans interruption. Reportez-vous à la section RHUI Azure Infrastructure mise à jour pour plus d’informations.


## <a name="rhui-azure-infrastructure-update"></a>Mise à jour RHUI Azure Infrastructure
À partir de septembre 2016, Azure a un nouvel ensemble de serveurs d’Infrastructure de mise à jour chapeau rouge (RHUI). Ces serveurs sont déployés avec le [Gestionnaire de trafic Azure]( https://azure.microsoft.com/services/traffic-manager/) afin qu’un point de terminaison unique (rhui-1.micrsoft.com) peut être utilisée par n’importe quel ordinateur virtuel quelle que soit la région. Ils utilisent également un certificat SSL qui est lié à une autorité de certification connues (racine Baltimore). Rendre cette mise à jour automatique serait dangereux pour des clients qui ont des utilisateurs ou des tables de routage personnalisés pour les serveurs de mise à jour RHUI, cette mise à jour est donc « opter-. » Étapes manuelles pour d’intégration à ces nouveaux serveurs sont disponibles sur cette page et un script complet pour d’intégration de manière automatique (lors de la vérification des étapes individuelles). Les nouvelles images RHEL PAYG sur le marché Azure (versions dates septembre 2016 ou version ultérieures) pointent automatiquement sur les nouveaux serveurs Azure RHUI et ne nécessitent pas une action supplémentaire.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>La barre de planning nouveau RHUI Azure infrastructure d’intégration

| Date | Remarque |
| --- | --- |
|22 septembre 2016 | Serveurs RHUI et des itinéraires d’installation disponible pour une utilisation. Machines virtuelles déployées à l’aide de la nouvelle (septembre 2016 daté) les images marketplace RHEL PAYG utilisera automatiquement les nouveaux serveurs RHUI, mais machines virtuelles existantes sont « participer »
|1 novembre 2016 | Les images machine virtuelle RHEL PAYG héritées (qui utilisent les anciens serveurs Azure RHUI) sont été supprimés de la galerie Azure Marketplace
|16 janvier 2017 | Les anciens serveurs Azure RHUI seront fermés. Mettre à jour tous vos ordinateurs concerné PAYG RHEL virtuels à ce stade de conserver l’accès à RHUI Azure

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Adresses IP pour les serveurs de distribution de contenu RHUI nouveau sont

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procédure de mise à jour manuelles à utiliser les nouveaux serveurs RHUI Azure

Téléchargez (par le biais ourlée) la signature de clé publique

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Vérifiez la clé téléchargée

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Vérifiez la sortie, vérifiez les `keyid` et `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installer la clé publique

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Télécharger, vérifier et installer le Client t/min

Télécharger : Pour RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Pour RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Vérifiez que :

```
rpm -Kv azureclient.rpm
```

Vérifier produit une sortie qu’une signature du package est OK

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installer le t/min

```
sudo rpm -U azureclient.rpm
```

Une fois terminée, vérifiez que vous pouvez accéder formulaire Azure RHUI la machine virtuelle

### <a name="all-in-one-script-for-automating-the-above-task"></a>Tout-en-un script pour automatiser la tâche ci-dessus.
Utilisez le script suivant selon vos besoins pour automatiser les tâches de mise à jour des machines virtuelles concernés vers les nouveaux serveurs RHUI Azure.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Vue d’ensemble RHUI
[Infrastructure de mises à jour chapeau rouge](https://access.redhat.com/products/red-hat-update-infrastructure) offre une solution hautement scalable pour gérer le contenu de référentiel yum instances de cloud Red chapeau Enterprise Linux qui sont hébergés par les fournisseurs de cloud certifié chapeau rouge. Basé sur le projet pâte en amont, RHUI permet de fournisseurs cloud localement miroir contenu référentiels hébergé par un chapeau rouge, création de référentiels personnalisés avec leur propre contenu et rendre ces référentiels disponibles pour un grand groupe d’utilisateurs finaux grâce à un système équilibrage de remise de contenu.

## <a name="regions-where-rhui-is-available"></a>Régions dans lesquels RHUI est disponible
RHUI est disponible dans toutes les régions où RHEL à la demande images est disponibles. Il inclut actuellement publiques toutes les régions commerciales dans la page [tableau de bord état Azure](https://azure.microsoft.com/status/) et les régions pour le gouvernement Azure nous. Accès RHUI pour les machines virtuelles sa mise en service à partir d’images à la demande RHEL est incluse dans leur prix. Disponibilité de cloud nationales/régionales supplémentaire est mise à jour à mesure que nous développe la disponibilité RHEL à la demande à l’avenir.

> [AZURE.NOTE] Accès aux RHUI hébergé Azure est limitée aux ordinateurs virtuels au sein de [plages d’adresses IP de Microsoft Azure centre de données](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Obtenir des mises à jour à partir d’un autre référentiel de mise à jour

Si vous avez besoin obtenir des mises à jour d’un référentiel de mise à jour différente (au lieu de RHUI hébergé Azure) vous devez annuler l’enregistrement de vos instances de RHUI et réenregistrer avec l’infrastructure de mise à jour souhaité (par exemple, rouge chapeau Satellite ou rouge chapeau client portail CDN). Vous aurez besoin abonnements rouge chapeau appropriés pour ces services et d’inscription pour [Accéder au Cloud chapeau rouge dans Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Pour annuler l’enregistrement RHUI réenregistrer à votre suivre d’infrastructure de mise à jour les étapes ci-dessous.

1.  Modifier /etc/yum.repos.d/rh-cloud.repo et changez tous `enabled=1` à `enabled=0`. Par exemple :

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Modifiez /etc/yum/pluginconf.d/rhnplugin.conf et `enabled=0` à `enabled=1`.
3.  Inscrivez ensuite l’infrastructure souhaitée, par exemple rouge chapeau client portail. Suivez rouge chapeau solution guide sur la [façon d’enregistrer et de s’abonner à un système au portail client chapeau rouge](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Accès à la RHUI hébergé Azure est inclus dans le prix d’image RHEL paiement (PAYG). Annuler l’inscription d’une machine virtuelle RHEL PAYG à partir de la RHUI hébergé Azure ne convertit pas la machine virtuelle en type mettre votre-appartient-licence (BYOL) machine virtuelle et par conséquent, vous pouvez être subir frais doubles si vous vous inscrivez la même machine virtuelle avec une autre source des mises à jour. 
> 
> Si vous devez toujours utiliser une infrastructure de mise à jour des autres que RHUI hébergé Azure envisagez de créer et déployer vos propres images (BYOL-type) comme décrit dans l’article [créer et télécharger rouge chapeau-machine virtuelle pour Azure](virtual-machines-linux-redhat-create-upload-vhd.md) .

## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle de rouge chapeau Enterprise Linux d’exploiter et image Azure Marketplace paiement RHUI hébergé Azure atteindre [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Vous ne pourrez pas utiliser `yum update` dans votre instance RHEL sans tout le programme d’installation supplémentaire.
<properties
   pageTitle="Conventions d’appellation pour les ressources Azure recommandées | Conseils | Microsoft Azure"
   description="Conventions d’appellation recommandées pour les ressources Azure. Comment nommer machines virtuelles, comptes de stockage, réseaux, réseaux virtuels, sous-réseaux et autres entités Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Conventions d’appellation pour les ressources Azure recommandées

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Le choix d’un nom pour toutes les ressources de Microsoft Azure est important parce que :

- Il est difficile de modifier un nom.
- Les noms doivent respecter les exigences de leur type de ressource spécifique.

Conventions d’affectation de noms cohérentes facilitent identifier les ressources. Ils peuvent également indiquer le rôle d’une ressource dans une solution.

Cet article est un résumé des règles d’appellation et des restrictions pour les ressources Azure et un ensemble de base des recommandations pour les conventions d’affectation de noms.  Vous pouvez utiliser ces recommandations comme point de départ pour vos propres conventions spécifiques à vos besoins.  

La clé du succès avec des conventions d’est mise en place et en les suivant dans l’ensemble de vos applications et organisations. 

## <a name="naming-subscriptions"></a>Abonnements d’attribution de nom

Lorsque vous nommez abonnements Azure, noms détaillées permettent de comprendre le contexte et l’objectif de chaque abonnement effacer.  Lorsque vous travaillez dans un environnement avec plusieurs abonnements, suivi d’une convention d’appellation partagée peut améliorer la clarté.

Un modèle recommandé pour les abonnements d’appellation est la suivante :

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Société serait généralement identique pour chaque abonnement. Cependant, certaines sociétés peuvent avoir des sociétés enfant au sein de la structure organisationnelle. Ces sociétés peuvent être gérées par un groupe informatique central. Dans ce cas, les pourraient être différencier en demandant le nom de la société parent (*Contoso*) et le nom de la société enfant (*Vent du Nord*).

- Service est un nom de l’organisation où un groupe de personnes travaillent. Cet élément au sein de l’espace de noms comme facultatifs.

- Ligne de produit est un nom spécifique d’un produit ou d’une fonction qui est effectuée à partir du service.
Il s’agit généralement facultatif pour les applications et services usage interne. Toutefois, il est fortement recommandé d’utiliser des services au public nécessitant séparation facile et identification (par exemple séparation claire de la liste enregistrements).

- Environnement est le nom qui décrit le cycle de vie de déploiement des applications ou des services, tels que développement, q & a ou produit.

| Société | Département | Ligne de produit ou Service | Environnement | Nom complet  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Production | Contoso SocialGaming AwesomeService Production |
| Contoso | SocialGaming | AwesomeService | Développement | Contoso SocialGaming AwesomeService développement |
| Contoso | IL | InternalApps | Production | Contoso informatique InternalApps Production |
| Contoso | IL | InternalApps | Développement | Contoso informatique développement InternalApps |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Permet d’éviter toute ambiguïté Affixes

Lorsque vous nommez des ressources dans Azure, il est recommandé d’utiliser courantes préfixes ou suffixes pour identifier le type et le contexte de la ressource.  Alors que toutes les informations sur le type, les métadonnées, contexte, il est disponible par programme, application communs affixes simplifie l’identification visuelle.  Lors de l’incorporation affixes dans votre convention d’appellation, il est important de clairement spécifier si l’apposer est au début du nom (préfixe) ou à la fin (suffixe).  

Par exemple, voici deux noms possibles pour un service d’hébergement un moteur de calcul :

- SvcCalculationEngine (préfixe)
- CalculationEngineSvc (suffixe)

Affixes peuvent désigner différents aspects qui décrivent les ressources. Le tableau suivant montre quelques exemples généralement utilisés.

| Aspect | Exemple | Notes |
| ------ | ------- | ----- |
| Environnement | développement, nomenclature, q & a | Identifie l’environnement pour la ressource |
| Emplacement | UW (nous ouest), ue (Orient US) | Identifie la zone dans laquelle la ressource est déployée |
| Instance | 01, 02 | Pour les ressources qui ont plus d’une instance nommée (serveurs web, etc.). |
| Produit ou Service | service | Identifie le produit, une application ou un service qui prend en charge de la ressource |
| Rôle | SQL, web, de messagerie | Identifie le rôle de la ressource associée |

Lorsque vous développez une convention d’appellation spécifique pour votre société ou projets, il est important de choisir un ensemble d’affixes ainsi que leur position (suffixe ou préfixe).

## <a name="naming-rules-and-restrictions"></a>Nommer les règles et les Restrictions

Chaque type de ressource ou un service dans Azure applique un ensemble de nommer des restrictions et portée ; une convention d’appellation ou un motif doit respecter les règles d’appellation requises et l’étendue.  Par exemple, tandis que le nom d’un ordinateur virtuel mappe vers un nom DNS (et par conséquent doit être unique dans l’ensemble de Azure), le nom d’un VNET est dans l’étendue au groupe de ressources où il est créée.

En règle générale, éviter que tous les caractères spéciaux (`-` ou `_`) en tant que le premier ou dernier caractère dans n’importe quel nom. Ces caractères entraîne la plupart des règles de validation échec.

| Catégorie | Service ou entité | Étendue | Longueur | Casse | Caractères valides | Modèle suggéré | Exemple |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Groupe de ressources | Groupe de ressources | Global | 1-64 | La casse | Alphanumérique, trait de soulignement et des traits d’union | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Groupe de ressources | Jeu de disponibilité | Groupe de ressources | 1-80 | La casse | Alphanumérique, trait de soulignement et des traits d’union | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Général | Balise | Entité associée | 512 (nom), 256 (valeur) | La casse | Alphanumérique | `"key" : "value"` | `"department" : "Central IT"` |
| Cluster | Machine virtuelle | Groupe de ressources | 1-15 | La casse | Alphanumérique, trait de soulignement et des traits d’union | `<name>-<role>-<instance>` | `profx-sql-001` |
| Espace de stockage | Nom de compte de stockage (données) | Global | 3-24 | Lettres minuscules | Alphanumérique | `<service short name><type><number>` | `profxdata001` |
| Espace de stockage | Nom de compte de stockage (disques) | Global | 3-24 | Lettres minuscules | Alphanumérique | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Espace de stockage | Nom du conteneur | Compte de stockage | 3-63 |   Lettres minuscules | Alphanumérique et tiret | `<context>` | `logs` |
| Espace de stockage | Nom d’objets BLOB | Conteneur | 1-1024 | Respecter la casse | Tout caractère URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Espace de stockage | Nom de la file d’attente | Compte de stockage | 3-63 | Lettres minuscules | Alphanumérique et tiret | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Espace de stockage | Nom de la table | Compte de stockage | 3-63 |La casse | Alphanumérique | `<service short name>-<context>` | `awesomeservice-logs` |
| Espace de stockage | Nom de fichier | Compte de stockage | 3-63 | Lettres minuscules | Alphanumérique | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Mise en réseau | Réseau virtuel (VNet) | Groupe de ressources | 2-64 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `<service short name>-[section]-vnet` | `profx-vnet` |
| Mise en réseau | Sous-réseau | VNet parent | 2-80 | Sans respecter la casse | Alphanumérique, trait de soulignement, tiret et période | `<role>-subnet` | `gateway-subnet` |
| Mise en réseau | Interface réseau | Groupe de ressources | 1-80 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Mise en réseau | Groupe de sécurité réseau | Groupe de ressources | 1-80 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Mise en réseau | Règle du groupe de sécurité réseau | Groupe de ressources | 1-80 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `<descriptive context>` | `sql-allow` |
| Mise en réseau | Adresse IP publique | Groupe de ressources | 1-80 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `<vm or service name>-pip` | `profx-sql1-pip` |
| Mise en réseau | Équilibrage de charge | Groupe de ressources | 1-80 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `<service or role>-lb` | `profx-lb` |
| Mise en réseau | Charger la configuration de règles équilibrée | Équilibrage de charge | 1-80 | Sans respecter la casse | Alphanumérique, tiret, trait de soulignement et période | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organisation des ressources avec des balises

Le Gestionnaire de ressources Azure prend en charge la liaison entités avec des chaînes de texte arbitraire pour identifier contexte et rationaliser automation.  Par exemple, la balise `"sqlVersion: "sql2014ee"` a pu identifier machines virtuelles dans un déploiement exécute SQL Server 2014 Enterprise Edition pour exécuter un script automatisé contre les.  Balises doivent être utilisées pour compléter et améliorer contexte côté les conventions d’appellation choisi.

> [AZURE.TIP]L’un des autres avantages de balises sont que balises s’étalent sur groupes de ressources, qui vous permet de créer un lien et corrélation entités dans des environnements hétérogènes.

Chaque ressource ou un groupe de ressources peut contenir un maximum de balises de **15** . Le nom de balise est limité à 512 caractères, et la valeur de balise est limitée à 256 caractères.

Pour plus d’informations sur les ressources de marquage, consultez [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

Les exemples d’utilisation de liaison courants sont notamment :

- **Facturation**; Regrouper les ressources et les associer à des frais de facturation ou de codes de l’arrière.
- **Identification de contexte de Service**; Identifier des groupes de ressources au sein de groupes de ressources pour les opérations courantes et de regroupement
- **Contrôle d’accès et le contexte de sécurité**; Identification du rôle d’administration en fonction de portefeuille, système, service, l’application, instance, etc..

> [AZURE.TIP]Ajouter des balises au plus tôt - balise souvent.  Meilleure ont un planning de référence jeu en place de marquage et évoluent dans le temps au lieu d’avoir à adapter après le fait.  

Voici un exemple de certaines des approches liaison courantes :

| Nom de balise | Clé | Exemple | Commentaire |
| -------- | --- | ------- | ------- |
| Facture à / internes refacturation ID | Facturation  | `IT-Chargeback-1234` | Un e/s interne ou un code de facturation |
| Opérateur ou individu directement responsable (DRI) | managedBy | `joe@contoso.com`  | Alias ou adresse de messagerie |
| Nom du projet | nom du projet | `myproject`  | Nom de la ligne du projet ou un produit |
| Version du projet | version du projet | `3.4`  | Version de la ligne du projet ou un produit |
| Environnement | environnement | `<Production, Staging, QA >` | Identificateur de l’environnement | 
| Niveau | niveau | `Front End, Back End, Data` | Identification de la couche ou rôle/contexte |
| Profil de données | dataProfile | `Public, Confidential, Restricted, Internal` | Critère de diffusion des données stockées dans la ressource |
 
## <a name="tips-and-tricks"></a>Conseils et astuces

Certains types de ressources peuvent nécessiter des soins supplémentaires sur la façon de nommer et conventions.

### <a name="virtual-machines"></a>Machines virtuelles

En particulier dans les topologies plus grande, avec soin dénomination machines virtuelles rationalise identifier le rôle de chaque ordinateur et l’activation de l’écriture de script plus prévisibles.

> [AZURE.WARNING]Chaque machine virtuelle dans Azure dispose d’un nom de ressource Azure et un nom d’hôte système d’exploitation.  
> Si le nom de la ressource et le nom d’hôte sont différents, gestion des ordinateurs virtuels peut être difficile et doit être évitée.
> Par exemple, si une machine virtuelle est créée à partir d’un .vhd déjà contient un système d’exploitation configuré avec un nom d’hôte.

- [Conventions d’appellation pour les machines virtuelles Windows Server](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Comptes de stockage et entités de stockage

Il existe deux principaux exemples d’utilisation pour les comptes de stockage - la sauvegarde des disques pour les machines virtuelles et le stockage de données dans des objets BLOB, les files d’attente et les tables.  Comptes de stockage utilisés pour les disques machine virtuelle doivent suivre la convention d’appellation de les associer avec le nom de la machine virtuelle parent (et avec la nécessité potentielle de plusieurs comptes de stockage pour références SKU machine virtuelle haut de gamme, également appliquer un suffixe de numéro).

> [AZURE.TIP]Comptes de stockage - si les données ou les disques - doivent suivre une convention d’appellation qui permet de plusieurs comptes de stockage à être exploitées (c'est-à-dire toujours à l’aide d’un suffixe numérique).

Il est possible de configurer un nom de domaine personnalisé pour accéder aux données blob dans votre compte de stockage Azure.
Le point de terminaison par défaut pour le service Blob est `https://mystorage.blob.core.windows.net`.

Mais si vous mappez un domaine personnalisé (par exemple, www.contoso.com) au point de terminaison blob pour votre compte de stockage, vous pouvez également accéder blob des données dans votre compte de stockage à l’aide de ce domaine. Par exemple, avec un nom de domaine personnalisé, `http://mystorage.blob.core.windows.net/mycontainer/myblob` qu’ils soient accessibles `http://www.contoso.com/mycontainer/myblob`.

Pour plus d’informations sur la configuration de cette fonctionnalité, reportez-vous à [configurer un nom de domaine personnalisé pour votre point de terminaison du stockage Blob](../storage/storage-custom-domain-name.md).

Pour plus d’informations sur la dénomination des objets BLOB, conteneurs et des tables :

- [Nommer et faisant référence à des conteneurs et des objets BLOB des métadonnées](https://msdn.microsoft.com/library/dd135715.aspx)
- [Nommer les files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx)
- [Nommer des Tables](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Un nom blob peut contenir n’importe quelle combinaison de caractères, mais les caractères URL réservés doivent être correctement séquence d’échappement. Éviter les noms d’objets blob qui se terminent par un point (.), une barre oblique (/), ou une séquence ou une combinaison des deux. Par convention, la barre oblique est le séparateur de répertoire **virtuel** . N’utilisez pas une barre oblique (\) dans un nom de blob. Le client API peut lui permettre, mais échouer puis hachage correctement, et les signatures ne correspondront pas.

Il n’est pas possible de modifier le nom d’un compte de stockage ou un conteneur après que qu’il a été créé.
Si vous voulez utiliser un nouveau nom, vous devez supprimer et créer un nouveau.

> [AZURE.TIP] Nous vous recommandons d’établir une convention d’appellation pour tous les comptes de stockage et types avant de commencer le développement d’un nouveau service ou une application.

## <a name="example---deploying-an-n-tier-service"></a>Exemple : déploiement d’un service multicouches

Dans cet exemple, nous définir une configuration du service multicouches, composée d’IIS serveurs frontaux (hébergés dans Windows Server machines virtuelles), avec SQL Server (hébergée sur deux ordinateurs de virtuels Windows Server), un cluster ElasticSearch (hébergé dans 6 Linux machines virtuelles) et les comptes de stockage associé, réseaux virtuels, ressources regroupement et l’équilibrage de charge.

Nous allons commencer en définissant les conventions contextuelles de cette application :

| Entité | Convention | Description  |
| ------ | ---------- | ------------ |  
| Nom du service | `profx` | Le nom court de l’application ou un service en cours de déploiement |
| Environnement | `prod` | Il s’agit pour le déploiement de production (contrairement à q & a, test, etc..) |

À partir de cette référence nous pouvons puis organisez les conventions pour chacun des types de ressources :

| Type de ressource | Base du salon | Exemple |
| ------------- | --------------- | ------- |
| Abonnement | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Groupe de ressources | `servicename-rg` | `profx-rg` |
| Réseau virtuel | `servicename-vnet` | `profx-vnet` |
| Sous-réseau | `role-subnet` | `sql-vnet` |
| Équilibrage de charge | `servicename-lb` | `profx-lb` |
| Machine virtuelle | `servicename-role[number]` | `profx-sql0` |
| Compte de stockage | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Comme le montre l’exemple suivant diagramme :

![diagramme de topologie d’application] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Exemple de topologie Application")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Exemple - script Azure infrastructure du langage commun pour le déploiement de l’exemple ci-dessus.

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

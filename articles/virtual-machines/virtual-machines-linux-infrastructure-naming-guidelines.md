<properties
    pageTitle="Infrastructure conventions | Microsoft Azure"
    description="Obtenir des informations sur les règles de conception et implémentation clés d’affectation des noms dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Instructions d’appellation infrastructure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension de l’approche conventions d’appellation pour toutes vos ressources Azure différentes générer un ensemble de ressources logique et facilement identifiable au sein de votre environnement.

## <a name="implementation-guidelines-for-naming-conventions"></a>Instructions d’implémentation pour les conventions d’affectation de noms

Décisions :

- Quelles sont vos conventions d’affectation de noms pour les ressources Azure ?

Tâches :

- Définir les affixes à utiliser dans plusieurs de vos ressources pour préserver la cohérence.
- Définir des noms de compte de stockage données la configuration minimale requise pour les rendre unique.
- Documenter la convention d’appellation pour être utilisé et distribuer à toutes les parties concernées de garantir la cohérence des déploiements.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Vous devez avoir une bonne convention d’appellation en place avant de créer rien dans Azure. Une convention d’appellation garantit que toutes les ressources ont un nom prévisible, qui vous aide à réduire les tâches administratives liées à la gestion des ces ressources.

Vous pouvez choisir de suivre un ensemble spécifique de conventions d’attribution de nom défini pour votre organisation entière ou un compte ou un abonnement Azure spécifique. Bien qu’il soit facile pour les personnes au sein des organisations d’établir des règles implicites lorsque vous travaillez avec des ressources Azure, vous devez être en mesure d’adapter pour équipes puissent collaborer dans Azure.

Convenir d’un ensemble d’emblée les conventions d’affectation de noms. Il existe quelques considérations concernant des conventions couper les ensembles de règles.

## <a name="affixes"></a>Effectue

Lorsque vous cherchez à définir une convention d’appellation, prendre une décision n’est que l’apposer :

- Début du nom (préfixe)
- La fin du nom (suffixe)

Par exemple, voici deux noms possibles pour un groupe de ressources à l’aide du `rg` apposer :

- Routage-WebApp (préfixe)
- WebApp-routage (suffixe)

Affixes peuvent désigner différents aspects qui décrivent les ressources. Le tableau suivant montre quelques exemples généralement utilisés.

| Aspect                               | Exemples                                                               | Notes                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Environnement                          | développement, stg, produit                                                         | En fonction de l’objectif et le nom de chaque environnement.                                                     |
| Emplacement                             | usw (Ouest US), utilisez (États-Unis Extrême-Orient 2)                                         | En fonction de la région du centre de données ou la région de l’organisation.                               |
| Composant Azure, service ou produit | Routage de groupe de ressources, VNet pour réseau virtuel                        | Selon le produit pour laquelle la ressource prend en charge.                                          |
| Rôle                                 | base de données, application web                                                           | En fonction du rôle de l’ordinateur virtuel.                                                              |
| Instance                             | 01, 02, 03, etc..                                                       | Pour les ressources qui ont plusieurs instances. Par exemple, les serveurs web dans un service cloud équilibrage de charge. |


Lorsque vous établissez vos conventions d’affectation de noms, assurez-vous qu’ils indiquer clairement quels affixes à utiliser pour chaque type de ressource, puis dans la position (suffixe de vs préfixe).

## <a name="dates"></a>Dates

Il est souvent important déterminer la date de création à partir du nom d’une ressource. Nous vous recommandons du format de date AAAAMMJJ. Ce format permet de garantir que non seulement l’intégralité de la date est enregistrée, mais également que deux les ressources dont les noms diffèrent uniquement sur la date sont triées par ordre alphabétique, dans l’ordre chronologique.

## <a name="naming-resources"></a>Noms des ressources

Définissez chaque type de ressource dans la dénomination, qui doit avoir des règles qui définissent comment affecter des noms à chaque ressource est créée. Ces règles doit être appliqué à tous les types de ressources, par exemple :

- Abonnements
- Comptes
- Comptes de stockage
- Réseaux virtuels
- Sous-réseaux
- Jeux de disponibilité
- Groupes de ressources
- Machines virtuelles
- Points de terminaison
- Groupes de sécurité réseau
- Rôles

Pour vous assurer que le nom fournit des informations suffisantes pour déterminer à quelle ressource il fait référence, vous devez utiliser des noms descriptifs.

## <a name="computer-names"></a>Noms des ordinateurs

Lorsque vous créez une machine virtuelle (), Azure nécessite un nom de machine virtuelle jusqu'à 64 caractères qui est utilisé pour le nom de la ressource. Azure utilise le même nom pour le système d’exploitation installé dans la machine virtuelle. Toutefois, ces noms toujours peut-être pas le même.

Si une machine virtuelle est créée à partir d’un fichier image .vhd qui contient déjà un système d’exploitation, le nom de la machine virtuelle dans Azure peut différer de nom de l’ordinateur de la machine virtuelle système d’exploitation. Cette situation peut ajouter un niveau de difficulté à la gestion de machine virtuelle, ce qui nous ne recommandons pas par conséquent. Affecter la ressource machine virtuelle Azure le même nom que le nom de l’ordinateur que vous attribuez au système d’exploitation de cet ordinateur virtuel.

Nous vous recommandons que le nom de la machine virtuelle Azure est le même que le nom de système d’exploitation sous-jacent.

## <a name="storage-account-names"></a>Noms des comptes de stockage

Comptes de stockage possèdent des règles spéciales régissant leurs noms. Vous ne pouvez utiliser que des lettres minuscules et chiffres. Pour plus d’informations, voir [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) . En outre, le nom de compte de stockage, avec core.windows.net, doit être un nom DNS globalement valide et unique. Par exemple, si le compte de stockage est appelé mystorageaccount, les noms DNS qui en résulte suivants doivent être uniques :

- mystorageaccount.BLOB.Core.Windows.NET
- mystorageaccount.table.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 
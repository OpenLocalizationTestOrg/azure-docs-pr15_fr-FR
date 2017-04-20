<properties
   pageTitle="Comment gérer les ressources de données | Microsoft Azure"
   description="Article procédure mise en surbrillance le contrôle visibilité et la propriété de ressources de données enregistré dans le catalogue de données Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-manage-data-assets"></a>Comment gérer les ressources de données

## <a name="introduction"></a>Introduction

**Catalogue de données Azure** fournit les fonctionnalités pour la découverte de source de données, qui permet aux utilisateurs de facilement découvrir et comprendre les sources de données que dont elles ont besoin pour effectuer une analyse et prendre des décisions. Ces fonctionnalités de découverte avoir un impact considérable lorsque tous les utilisateurs puissent les trouver et comprendre large gamme de sources de données disponibles. Par conséquent, le comportement par défaut du catalogue de données s’applique à toutes les sources de données enregistré être visible – et détectable par – tous les utilisateurs du catalogue.

Catalogue de données ne pas permet aux utilisateurs l’accès aux données elles-mêmes. Accès aux données est contrôlée par le propriétaire de la source de données. Catalogue de données permet aux utilisateurs pour découvrir les sources de données et afficher les métadonnées relatives aux sources d’enregistré dans le catalogue.

Il peut arriver, cependant, où il est possible que les sources de données doivent être uniquement visibles à des utilisateurs spécifiques ou aux membres des groupes spécifiques. Pour ces scénarios, catalogue de données permet aux utilisateurs de prendre possession d’éléments de données enregistré dans le catalogue, et au contrôle puis la visibilité des actifs dont ils sont propriétaires.

> [AZURE.NOTE] Les fonctionnalités décrites dans cet article sont disponibles uniquement dans le Standard Edition de Azure catalogue de données. L’édition gratuite ne propose pas de fonctionnalités pour la propriété et restriction visibilité de biens de données.

## <a name="managing-ownership-of-data-assets"></a>Gestion de la propriété de ressources de données
Par défaut, les ressources de données enregistrés dans le catalogue de données sont inconnues ; tout utilisateur ayant l’autorisation d’accéder au catalogue pouvez découvrir et annoter ces ressources. Les utilisateurs peuvent prendre possession de données sans propriétaire de l’entreprise et peuvent ensuite limiter la visibilité des actifs que dont ils sont propriétaires.

Quand un élément de données dans le catalogue de données appartient, seuls les utilisateurs autorisés par les propriétaires peuvent découvrir l’actif et afficher ses métadonnées et seuls les propriétaires peuvent supprimer l’actif à partir du catalogue.

> [AZURE.NOTE] La propriété dans le catalogue de données affecte uniquement les métadonnées stockées dans le catalogue. Il n’a pas prévu toutes les autorisations sur la source de données sous-jacente.

### <a name="taking-ownership"></a>Prendre possession
Utilisateurs peuvent prendre possession d’éléments de données en sélectionnant l’option « Prendre possession » dans le portail du catalogue de données. Aucune autorisation spéciale est nécessaire pour prendre possession d’un bien données inconnue ; tous les utilisateurs peuvent prendre possession d’un bien de données sans propriétaire.

### <a name="adding-owners-and-co-owners"></a>Ajout de propriétaires et collaboration
Si un élément de données est déjà la propriété, les utilisateurs ne peuvent pas simplement prendre possession – ils doivent être ajoutés en tant que collègue propriétaires par un propriétaire existant. N’importe quel propriétaire peut ajouter des utilisateurs et groupes de sécurité en tant que collègue propriétaires.

> [AZURE.NOTE] Il est recommandé d’avoir au moins deux personnes en tant que propriétaires pour n’importe quel élément de données propriétaire.

### <a name="removing-owners"></a>Suppression de propriétaires
Comme n’importe quel propriétaire de la ressource peut ajouter les propriétaires de co-création, n’importe quel propriétaire de biens pouvez supprimer n’importe quel co-propriétaire.

Si un propriétaire de biens supprime lui-même en tant que propriétaire, il ne peut plus gérer l’actif. Si un propriétaire de biens supprime lui-même en tant que propriétaire et il n’existe aucune autres propriétaires co-création, les biens revient à un état sans propriétaire.

## <a name="visibility"></a>Visibilité
Les propriétaires de données biens peuvent contrôler la visibilité des actifs données qu’ils possèdent. Pour restreindre la visibilité par défaut – où tous les utilisateurs de catalogue de données pouvant découvrir et afficher les biens données – propriétaire de la ressource peut activer ou désactiver le paramètre de visibilité de « Tout le monde » à « Propriétaires & ces utilisateurs » dans les propriétés de l’actif. Propriétaires peuvent ensuite ajouter des utilisateurs et groupes de sécurité.

> [AZURE.NOTE] Dès que possible, les autorisations de la propriété et visibilité biens doivent être affectées aux groupes de sécurité et non à des utilisateurs individuels.

## <a name="catalog-administrators"></a>Administrateurs de catalogue
Les administrateurs de catalogue de données sont implicitement co-création propriétaires de toutes les ressources dans le catalogue. Les propriétaires de biens ne peut pas supprimer la visibilité des administrateurs de catalogue, et les administrateurs peuvent gérer la propriété et la visibilité pour tous les éléments de données dans le catalogue.

## <a name="summary"></a>Résumé
Modèle de marketing participatif du catalogue de données de métadonnées et données parc permet de tous les utilisateurs de catalogue pour contribuer et découvrir. Le Standard Edition de catalogue de données fournit les fonctionnalités de la propriété et de gestion pour limiter la visibilité et l’utilisation des données spécifiques.

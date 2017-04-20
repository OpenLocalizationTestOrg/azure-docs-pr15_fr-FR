<properties
   pageTitle="Migrer vos données vers SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour une migration de vos données vers SQL Azure Data Warehouse pour développer des solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Migrer vos données
Données peuvent être déplacées de différentes sources dans votre SQL Data Warehouse avec un outils divers.  Copie de la définition d’application, SSIS et bcp peuvent être utilisés pour vous aider. Toutefois, comme la quantité de données augmente vous devez réfléchir répartir le processus de migration de données en étapes. Cela vous offre la possibilité d’optimiser chaque étape à la fois pour les performances et la résilience assurer une migration régulier des données.

Cet article traite d’abord les scénarios de migration simple de copie de la définition d’application, SSIS et bcp. Il puis regardez un peu plus loin dans la manière dont la migration peut être optimisée.

## <a name="azure-data-factory-adf-copy"></a>Copie de données par défaut (chargeur) Azure
[Chargeur copie][] fait partie [d’Azure Data Factory][]. Copie de la définition d’application permet d’exporter vos données dans des fichiers plats se trouvant sur le stockage local, aux fichiers plats distants contenus dans le stockage blob Azure ou directement dans SQL Data Warehouse.

Si vos données commencent dans le fichier plat, puis vous devez tout d’abord transférez-le vers blob Azure stockage avant de démarrer une charge dans Data Warehouse SQL. Une fois que les données sont transférées dans le stockage blob Azure vous pouvez choisir d’utiliser [Chargeur copie][] à nouveau pour envoyer les données dans SQL Data Warehouse.

PolyBase fournit également une option de haute performance pour charger les données. Que signifie toutefois, à l’aide de deux outils au lieu d’un. Si vous avez besoin d’obtenir les meilleures performances, utilisez PolyBase. Si vous souhaitez une expérience outil unique (et les données ne sont pas massives) chargeur est votre réponse.

> [AZURE.NOTE] PolyBase nécessite vos fichiers de données dans un format UTF-8. Il s’agit par défaut de la copie de la définition d’application le codage de sorte que rien à modifier. Il s’agit juste un rappel de ne pas modifier le comportement par défaut de copie de la définition d’application.

Pour certains [exemples chargeur][]rédaction de tête sur l’article suivant.

## <a name="integration-services"></a>Services d’intégration ##
Integration Services (SSIS) est un outil extraire transformer, puis chargement puissant et flexible qui prend en charge des flux de travail complexes, la transformation de données et plusieurs options de chargement de données. SSIS permet de transférer des données simplement à Azure ou dans le cadre d’une migration plus large.

> [AZURE.NOTE] SSIS pouvez exporter UTF-8 sans la marque d’ordre octet dans le fichier. Pour configurer ce que vous devez d’abord utiliser le composant de colonne dérivé pour convertir les données de caractères dans le flux de données à utiliser la page de code UTF-8 65001. Une fois que les colonnes ont été convertis, écrire les données dans la carte de destination de fichier plat en vérifiant que 65001 a également été sélectionnée en tant que la page de codes pour le fichier.

SSIS se connecte à SQL Data Warehouse comme il serait se connecter à un déploiement de SQL Server. Toutefois, vos connexions devez utiliser un gestionnaire de connexions ADO.NET. Vous devez également veiller à configurer le « utiliser Insertion en bloc lorsqu’elle est disponible » paramètre pour optimiser le débit. Reportez-vous à l’article [adaptateur de destination ADO.NET][] pour en savoir plus sur cette propriété

> [AZURE.NOTE] Connexion à Azure SQL Data Warehouse à l’aide de OLEDB n’est pas pris en charge.

Par ailleurs, il est toujours la possibilité qu’un package échouer en raison de problèmes réseau ou la limitation. Création de packages afin qu’ils peuvent être reprises au point d’échec, sans rétablissement de travail terminé avant l’échec.

Pour plus d’informations, consultez la [documentation de SSIS][].

## <a name="bcp"></a>bcp
bcp est un utilitaire de ligne de commande qui est conçu pour l’exportation et importation de données de fichier plat. Certains transformation puisse avoir lieu lors de l’exportation de données. Pour effectuer des transformations simples utilisent une requête pour sélectionner et transformer les données. Une fois exportés, les fichiers plats peuvent puis chargées directement dans la cible de la base de données SQL Data Warehouse.

> [AZURE.NOTE] Il est souvent judicieux de sélectionner encapsuler les transformations utilisées pendant l’exportation des données dans un affichage sur le système source. Ainsi que la logique est conservée et le processus répétitif.

Avantages de bcp sont :

- Simplicité. commandes bcp sont simples à créer et exécuter
- Processus de chargement autorisant un redémarrage. Une fois exportés la charge peut être exécutée n’importe quel nombre de fois.

Limitations de bcp sont :

- bcp fonctionne avec uniquement les fichiers plats tabulation. Il ne fonctionne pas avec les fichiers tels que xml ou JSON
- bcp ne prend pas en charge l’exportation vers UTF-8. Cela peut empêcher à l’aide de PolyBase sur des données bcp exporté
- Fonctions de transformation de données sont limitées à la scène exporter uniquement et sont nature simples
- bcp n’a pas été adapté pour être robuste lors du chargement des données via internet. Toute instabilité du réseau peut entraîner une erreur de chargement.
- bcp s’appuie sur le schéma de présence dans la base de données cible avant le chargement

Pour plus d’informations, voir [utiliser bcp pour charger les données dans SQL Data Warehouse][].

## <a name="optimizing-data-migration"></a>Optimisation de migration des données
Un processus de migration de données SQLDW peut être divisé efficacement vers le bas en trois étapes distinctes :

1. Exporter des données sources
2. Transfert de données vers Azure
3. Charger dans la base de données SQLDW cible

Chaque étape peut être optimisé individuellement pour créer un processus de migration robuste, autorisant un redémarrage et résistant optimiser les performances à chaque étape.

## <a name="optimizing-data-load"></a>Optimiser la charge de données
Examiner ceux-ci dans l’ordre inverse pour quelques instants ; le moyen le plus rapide pour charger des données est via PolyBase. Optimisation pour un processus de chargement PolyBase ajoute conditions préalables pour les étapes précédentes afin qu’il est préférable de comprendre cela instants. Ils sont :

1. Codage de fichiers de données
2. Format des fichiers de données
3. Emplacement des fichiers de données

### <a name="encoding"></a>Codage
PolyBase nécessite des fichiers de données soit codé UTF-8. Cela signifie que lorsque vous exportez vos données il doit être conforme à cette exigence. Si vos données contiennent uniquement des caractères ASCII base (ne pas extended ASCII) puis ces carte directement à la norme UTF-8 et que vous n’êtes pas obligé inquiétez le codage. Toutefois, si vos données contiennent des caractères spéciaux tels que trémas, des accents ou des symboles ou vos données prend en charge les langues non latins puis vous devez vous assurer que vos fichiers exportation sont correctement codage UTF-8.

> [AZURE.NOTE] bcp ne prend pas en charge l’exportation de données UTF-8. Par conséquent la meilleure option consiste à utiliser les Services d’intégration ou copie de la définition d’application pour l’exportation de données. Il est important de noter que la marque d’ordre octet UTF-8 (nomenclature) n’est pas requis dans le fichier de données.

Les fichiers encodés à l’aide de UTF-16 devrez ré-apparaître ***préalable*** pour le transfert de données.

### <a name="format-of-data-files"></a>Format des fichiers de données
PolyBase impose un début/fin de ligne fixe de \n ou saut de ligne. Vos fichiers de données doivent être conformes à ce standard. Il n’existe pas de restrictions à terminateurs de chaîne ou une colonne.

Vous devez définir toutes les colonnes dans le fichier dans le cadre de votre table externe dans PolyBase. Assurez-vous que toutes les colonnes exportées sont requis et que les types sont conformes aux normes requises.

Veuillez référer à la [migrer votre schéma] article pour plus d’informations sur les types de données pris en charge.

### <a name="location-of-data-files"></a>Emplacement des fichiers de données
SQL Data Warehouse utilise PolyBase pour charger les données depuis le stockage Blob Azure en mode exclusif. Par conséquent, les données doivent avoir été tout d’abord transférées dans le stockage blob.

## <a name="optimizing-data-transfer"></a>Optimiser le transfert de données
Un des composants de migration des données plus lentes est le transfert des données vers Azure. Non seulement la bande passante réseau possible d’un problème, mais la fiabilité du réseau peut parfois grave de progression. Par défaut migration de données vers Azure est via internet afin que les chances de transférer les erreurs se produisant sont susceptibles de raisonnablement. Toutefois, ces erreurs peuvent nécessiter soient envoyées à nouveau en tout ou partie des données.

Peut être effectué en vous disposez de plusieurs options pour améliorer la vitesse et la résilience de ce processus :

### <a name="expressroute"></a>[ExpressRoute][]
Vous voudrez peut-être envisagez d’utiliser [ExpressRoute][] pour accélérer le transfert. [ExpressRoute][] vous donne une connexion privée établie à Azure afin que la connexion ne sont pas acheminés via internet public. Il s’agit pas d’une étape obligatoire. Toutefois, il contribue à améliorer débit lors de la diffusion des données vers Azure à partir d’un local ou fonctionnalité de co-création emplacement.

Les avantages de l’utilisation de [ExpressRoute][] sont les suivantes :

1. Fiabilité améliorée
2. Vitesse réseau
3. Temps de latence réseau réduits
4. sécurité réseau accrue

[ExpressRoute][] est utile pour un certain nombre de scénarios ; pas seulement la migration.

Vous souhaitez ? Pour plus d’informations et les prix, consultez la [documentation de ExpressRoute][].

### <a name="azure-import-and-export-service"></a>Azure importation et exportation Service
Le Service exporter et importation Azure est un processus de transfert de données destiné aux grandes (Go ++) pour massif (To ++) transferts de données dans Azure. Il s’agisse d’enregistrer vos données sur un disque et leur expédition à un centre de données Azure. Le contenu du disque est ensuite chargé dans Azure stockage BLOB de votre part.

Une vue d’ensemble du processus d’importation exportation se présente comme suit :

1. Configurer un conteneur de stockage d’objets Blob Azure pour recevoir les données
2. Exporter vos données sur le stockage local
2. Copiez les données dans les lecteurs de disques durs SATA II/III 3,5 pouces à l’aide de la [Azure importer/exporter outil]
3. Créer une tâche d’importation utilisation Azure importation et exportation Service fournissant les fichiers journal générés par le [Azure importer/exporter outil]
4. Envoyer les disques votre centre de données Azure désigné
5. Vos données sont transférées vers votre conteneur de stockage d’objets Blob Azure
6. Charger les données dans SQLDW à l’aide de PolyBase

### <a name="azcopy-utility"></a>Utilitaire [AZCopy][]
L’utilitaire [AZCopy][] est un excellent outil pour obtenir vos données transférées vers Azure stockage BLOB. Il est conçu pour les PME (Mo ++) pour les transferts de données volumineux (Go ++). [AZCopy] a également été conçu pour fournir le bon débit résistant lors du transfert de données vers Azure et donc s’avère très intéressante pour l’étape de transfert de données. Une fois transférées, vous pouvez charger les données à l’aide de PolyBase dans SQL Data Warehouse. Vous pouvez également incorporer AZCopy dans vos packages SSIS à l’aide de la tâche « Exécuter le processus ».

Pour utiliser AZCopy, vous devez tout d’abord télécharger et installer. Une [version de production][] et une [version preview][] est disponible.

Pour télécharger un fichier depuis votre système de fichiers, vous devez une commande similaire à celui qui suit :

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Un résumé de processus de haut niveau peut être :

1. Configurer un conteneur d’objets blob Azure stockage pour recevoir les données
2. Exporter vos données sur le stockage local
3. AZCopy vos données dans le conteneur de stockage d’objets Blob Azure
4. Charger les données vers SQL Data Warehouse à l’aide de PolyBase

Complète de documentation disponible : [AZCopy][].

## <a name="optimizing-data-export"></a>Optimiser l’exportation de données
En plus de s’assurer que l’exportation respecte la configuration requise disposée par PolyBase vous pouvez également rechercher afin d’optimiser l’exportation des données à améliorer davantage le processus.

> [AZURE.NOTE] Comme PolyBase exige que les données doivent être au format UTF-8 qu'il est peu probable que vous allez utiliser bcp pour effectuer l’exportation de données. bcp ne prend pas en charge les fichiers de données de sortie UTF-8. SSIS ou copie chargeur sont beaucoup mieux adapté à l’exécution de ce type d’exportation des données.

### <a name="data-compression"></a>Compression de données
PolyBase peut lire les données gzip compressées. Si vous parvenez à compresser vos données dans des fichiers gzip vous sera réduire la quantité de données que vous appuyez sur le réseau.

### <a name="multiple-files"></a>Plusieurs fichiers
Transmettre les grands tableaux en plusieurs fichiers permet non seulement pour améliorer la vitesse d’exportation, il est également utile avec transfert re-startability et la gestion globale des données une fois dans le stockage blob Azure. Parmi les nombreuses fonctionnalités piles de PolyBase est qu’il sera lire tous les fichiers à l’intérieur d’un dossier et traiter comme une table. Il est donc judicieux de sélectionner isoler les fichiers pour chaque table dans son propre dossier.

PolyBase prend également en charge la fonctionnalité appelée « parcours du dossier récursive ». Vous pouvez utiliser cette fonctionnalité pour améliorer l’organisation de vos données exportées afin d’améliorer votre gestion des données.

Pour plus d’informations sur le chargement des données avec PolyBase, voir [Utiliser PolyBase pour charger les données dans SQL Data Warehouse][].


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la migration, voir [migrer votre solution vers SQL Data Warehouse][].
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[Copie de la définition d’application]: ../data-factory/data-factory-data-movement-activities.md 
[Exemples de définition d’application]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md
[Migrer votre solution vers Data Warehouse SQL]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Utiliser bcp pour charger les données dans Data Warehouse SQL]: sql-data-warehouse-load-with-bcp.md
[Utiliser PolyBase pour charger les données dans Data Warehouse SQL]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Données Azure usine]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[Documentation ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

[version de production]: http://aka.ms/downloadazcopy/
[version d’évaluation]: http://aka.ms/downloadazcopypr/
[Carte de destination ADO.NET]: https://msdn.microsoft.com/library/bb934041.aspx
[Documentation SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

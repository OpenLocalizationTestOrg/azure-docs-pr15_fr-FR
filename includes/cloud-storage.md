#<a name="data-management-and-business-analytics"></a>Gestion des données et Business Analytique

Gérer et analyser des données dans le nuage sont simplement important que n’importe où ailleurs. Pour vous permettre de cela, Azure fournit une plage de technologies d’assistance pour travailler avec des données relationnelles et non relationnelles. Cet article présente chacune des options. 

##<a name="table-of-contents"></a>Table des matières      

- [Stockage d’objets BLOB](#blob)
- [Exécution d’un SGBD dans une Machine virtuelle](#dbinvm)
- [Base de données SQL](#sqldb)
    - [Synchronisation de données SQL](#datasync)
    - [Création de rapports de données SQL à l’aide de Machines virtuelles](#datarpt)
- [Stockage de table](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Stockage d’objets BLOB

Le mot « blob » est l’abréviation de « objet binaire volumineux » et décrit exactement quelles un blob est : un ensemble d’informations binaires. Encore même si elles sont simples, des objets BLOB sont très utiles. [Figure 1](#Fig1) illustre les notions de base de stockage d’objets Blob Azure.

<a name="Fig1"></a>![Diagramme d’objets BLOB][blobs]
 
**Figure 1 : Stockage d’objets Blob Azure stocke des données binaires - BLOB - dans des conteneurs.**

Pour utiliser des objets BLOB, vous créez tout d’abord un *compte de stockage*Azure. Dans le cadre du présent, vous spécifiez le centre de données Azure qui stockera les objets que vous créez à l’aide de ce compte. À l’endroit où il réside, chaque blob que vous créez appartient à un conteneur dans votre compte de stockage. Pour accéder à un objet blob, une application fournit une URL avec la forme :

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*conteneur*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; est un identificateur unique attribué lors de la création d’un nouveau compte de stockage, tandis que &lt; *conteneur* &gt; et &lt; *BlobName* &gt; sont les noms d’un conteneur spécifique et un blob dans ce conteneur. 

Azure propose deux types d’objets BLOB. Les choix possibles sont :

- *Bloquer* des objets BLOB, chacun d'entre eux peut contenir jusqu'à 200 gigaoctets de données. Comme son nom l’indique, un blob bloc est divisé en un nombre de blocs. En cas de panne lors du transfert d’un blob bloc, retransmission reprend avec le bloc plus récent au lieu d’envoyer à nouveau le blob entière. Bloquer des objets BLOB sont une approche assez générale du stockage, et qu’elles soient le type de blob plus couramment utilisés aujourd'hui.

- *Page* des objets BLOB, qui peuvent être aussi élevée à un seul to chaque. Page BLOB sont conçues pour un accès aléatoire, et donc chacun d'entre eux est divisé en un nombre de pages. Une application peut lire et écrire des pages individuelles au hasard dans le blob. Dans le Machines virtuelles Azure, par exemple, machines virtuelles que vous créez utilisent des objets BLOB page comme stockage permanent pour disques du système d’exploitation et disques de données.

Si vous choisissez des objets BLOB bloc ou des objets BLOB de page, les applications peuvent accéder données blob de différentes manières. Les options sont les suivantes :

- Directement par le biais d’un RESTful (c'est-à-dire, basé sur HTTP) protocole d’accès. Applications Azure et les applications externes, y compris les applications en cours d’exécution en local, peuvent utiliser cette option.
- À l’aide de la bibliothèque de Client de stockage Azure, qui fournit une interface plus conviviales et en haut du protocole d’accès brut blob RESTful. Là encore, Azure applications et applications externes peuvent accéder à des objets BLOB à l’aide de cette bibliothèque.
- Utilisation de lecteurs Azure, une option qui permet à une application Azure considérer un blob page comme un lecteur local avec un système de fichiers. Pour l’application, le blob page ressemble à un système de fichiers Windows ordinaire accédé à l’aide d’e/s de fichier standard. En fait, lit et écrit sont envoyés au blob page sous-jacente qui mettent en œuvre, le lecteur Azure. 

Pour se protéger contre les défaillances matérielles et améliorer la disponibilité, chaque blob est répliquée entre trois ordinateurs dans un centre de données Azure. Écriture dans un blob met à jour toutes les trois copies, afin de lectures ultérieures ne verrez pas les résultats n’est pas cohérente. Vous pouvez également spécifier que les données d’un blob doivent être copiées sur un autre centre de données Azure dans la même geo mais au moins 500 miles absent (e). Cette copie, appelé *geo réplication*, qui se produit après quelques minutes d’une mise à jour le blob, et il est utile de reprise.

Données dans des objets BLOB peuvent également être accessibles via le *Réseau de remise de contenu (CDN)*Azure. En cache des copies des données blob à des douzaines de serveurs dans le monde entier, le CDN pouvez accélérer l’accès aux informations sont accessible à plusieurs reprises. 

Simple qu’ils sont, objets BLOB sont le bon choix dans de nombreux cas. Le stockage et de diffusion en continu audio et vidéo sont évidentes exemples, comme des sauvegardes et autres types de données d’archivage. Les développeurs peuvent également utiliser des objets BLOB pour contenir tout type de données non structurées qu'ils le souhaitent. Avoir une méthode simple pour stocker et accéder aux données binaires peut être utile étonnant.


## <a name="dbinvm"></a>Exécution d’un SGBD dans une Machine virtuelle

De nombreuses applications aujourd'hui reposent sur un certain type de système bases de données gestion (). Les systèmes relationnelles telles que SQL Server sont les plus fréquemment utilisés choix, mais non relationnelles approches, souvent appelées *NoSQL* technologies, obtenir les plus populaires chaque jour. Pour permettre aux applications en nuage utiliser ces options de gestion des données, Machines virtuelles Azure vous permet d’exécuter un SGBD (relationnelle ou NoSQL) sur un ordinateur virtuel. [Figure 2](#Fig2) montre à quoi cela ressemble avec SQL Server.

<a name="Fig2"></a>![Diagramme de SQL Server dans une Machine virtuelle][SQLSvr-vm]
 
**Figure 2 : Azure Machines virtuelles permet d’exécuter un SGBD une machine virtuelle, avec une connexion permanente fournie par les objets BLOB.**

Pour les développeurs et les administrateurs de base de données, ce scénario ressemble beaucoup exécuter le même logiciel dans leur propre centre de données. Dans l’exemple montré ici, par exemple, quasi-totalité des fonctionnalités de SQL Server peut être utilisée et vous avez accès administratif total sur le système. Vous avez également la responsabilité de gestion du serveur de base de données, bien entendu, comme s’il était exécuté localement.

Comme le montre la [Figure 2](#Fig2) , vos bases de données semblent être stockées sur le disque local de la machine virtuelle le serveur est exécuté dans. Toutefois, en coulisses, chacun de ces disques est écrite dans un blob Azure. (Il est semblable à l’utilisation d’un SAN dans votre propre centre de données, avec un blob comportant beaucoup comme une LUN). Comme avec n’importe quel blob Azure, les données qu’il contient sont répliquées trois fois au sein d’un centre de données et, si vous demandez, geo répliquées vers un autre centre de données dans la même région. Il est également possible d’utiliser des options telles que la mise en miroir pour améliorer la fiabilité de la base de données SQL Server. 

Un autre moyen d’utiliser SQL Server sur un ordinateur virtuel consiste à créer une application hybride, où les données sont situées sur Azure pendant l’exécution de la logique d’application en local. Par exemple, cela peut représentatives lorsque les applications qui s’exécutent à plusieurs endroits ou sur divers appareils mobiles doivent partager les mêmes données. Pour rendre la communication entre la logique cloud base de données et en local plus simple, une organisation peut utiliser réseau virtuel Azure pour créer une connexion de réseau privé virtuel (VPN) entre un centre de données Azure et son propre centre de données locale.


## <a name="sqldb"></a>Base de données SQL

De nombreuses personnes exécutant un SGBD une machine virtuelle est la première option qui vient à l’esprit pour la gestion des données structurées dans le cloud. Il n’est pas le seul choix, cependant, ni toujours le meilleur choix. Dans certains cas, il est préférable plus de gestion des données à l’aide d’une plateforme comme une approche de Service (PaaS). Azure fournit une technologie PaaS appelée base de données SQL qui vous permet d’effectuer cette opération pour les données relationnelles. [Figure 3](#Fig3) illustre cette option. 

<a name="Fig3"></a>![Diagramme de base de données SQL][SQL-db]
 
**Figure 3 : Base de données SQL propose un service de stockage relationnel PaaS partagé.**

Base de données SQL ne donner à chaque client son propre physique instance de SQL Server. Au lieu de cela, il propose un service client multiples, avec un serveur de base de données SQL logique pour chaque client. Tous les clients partagent la capacité de stockage et cluster fournie par le service. Et à l’instar de stockage d’objets Blob, toutes les données de base de données SQL est stocké sur trois ordinateurs différents dans un centre de données Azure, donnant vos bases de données intégrée haute disponibilité (HA).

À l’application, base de données SQL ressemble beaucoup SQL Server. Applications peuvent émettre des requêtes SQL sur des tables relationnelles, utilisez des procédures stockées T-SQL et exécuter des transactions sur plusieurs tables. Et, car les applications accéder à l’aide du protocole de flux de données tabulaires (TDS), le même protocole utilisé pour accéder à SQL Server, de la base de données SQL qu’ils peuvent utiliser des données à l’aide de Framework entité, ADO.NET, JDBC et autres interfaces d’accès aux données familier. 

Mais comme base de données SQL est un service cloud s’exécutant dans des centres de données Azure, vous n’avez pas besoin gérer les aspects physiques du système, telles que l’utilisation du disque. Vous ne devez également à vous soucier de la mise à jour de logiciels ou gestion des autres tâches d’administration de niveau inférieur. Chaque organisation du client toujours contrôle ses propres bases de données, bien entendu, y compris leurs schémas et les connexions de l’utilisateur, mais la plupart des tâches administratives courantes ont été effectuées pour vous. 

Tandis que la base de données SQL ressemble beaucoup à SQL Server aux applications, il ne comporte pas exactement comme un SGBD s’exécutant sur un ordinateur physique ou virtuel. Car il s’exécute sur le matériel partagé, ses performances varie selon la charge placée sur ce matériel par l’ensemble de ses clients. Cela signifie que les performances de, par exemple, une procédure stockée dans la base de données SQL peut-être varier d’un jour à l’autre. 

Aujourd'hui, base de données SQL vous permet de créer une base de données comportant jusqu'à 150 gigaoctets. Si vous avez besoin travailler avec les bases de données volumineuses, le service fournit une option appelée *fédération*. Pour ce faire, un administrateur de base de données crée deux ou plusieurs *membres de la fédération*, chacun d’eux étant une base de données distinct avec son propre schéma. Données sont réparties sur ces membres, ce qui est souvent appelée *ont*, avec chaque membre affecté une *clé de fédération*unique. Une application émet des requêtes SQL par rapport à ces données en spécifiant la clé de fédération qui identifie le membre de la fédération la requête doivent cibler. Cette option permet à l’aide d’une approche relationnelle traditionnelle avec de grandes quantités de données. Comme toujours, il existe des compromis ; requêtes ni transactions peuvent couvrir membres de fédération, par exemple. Mais lorsqu’un service PaaS relationnel est le meilleur choix et ces compromis sont acceptables, à l’aide de la fédération SQL peut être une bonne solution.

Base de données SQL peuvent être utilisée par les applications qui s’exécutent sur Azure ou un autre emplacement, comme dans votre centre de données locale. Cela permet de réaliser des applications en nuage nécessitant des données relationnelles, ainsi que les applications qui peuvent bénéficier de stockage des données dans le nuage local. Une application mobile peut-être compter sur base de données SQL pour gérer des données relationnelles partagées, par exemple, en tant que peut une application inventaire qui s’exécute à plusieurs revendeurs dans le monde entier.

Réflexion sur base de données SQL génère un problème évident (et importantes) : quand devez-vous exécuter SQL Server dans une machine virtuelle, et lorsque est base de données SQL un meilleur choix ? Comme d’habitude, il existe les arbitrages, et donc approche est préférable varie selon vos besoins en matière. 

Un moyen simple de penser consiste à afficher la base de données SQL comme étant pour les nouvelles applications, tandis que SQL Server dans une machine virtuelle est un meilleur choix lorsque vous déplacez un existant en local application dans le cloud. Il peut également être utile d’examiner cette question de façon plus précise, cependant. Par exemple, la base de données SQL est plus facile à utiliser, dans la mesure où il est minime le programme d’installation et d’administration. Mais qui exécute SQL Server dans une machine virtuelle peut avoir des performances plus prévisibles - il n’est pas un service partagé - et prend également en charge des bases de données plus grande non fédéré que base de données SQL. Toujours, base de données SQL assure la réplication intégrée de données et traitement, efficace vous donnant un SGBD disponibilité avec très peu de travail. Tandis que SQL Server vous offre davantage de contrôle et un ensemble d’options un peu plus large, base de données SQL est plus simple à installer et beaucoup moins de travail pour gérer.

Enfin, il est important de souligner que base de données SQL n’est pas le service de données PaaS uniquement sur Azure. Les partenaires Microsoft proposent ainsi que d’autres options. Par exemple, ClearDB propose un MySQL PaaS offrant, tandis que Cloudant vend une option NoSQL. Services de données PaaS sont la solution idéale dans de nombreux cas, et donc cette approche de gestion des données est essentiel de Azure.


### <a name="datasync"></a>Synchronisation de données SQL

Tandis que la base de données SQL ne conserve pas les trois copies de chaque base de données dans un centre de données Azure unique, il ne réplique automatiquement des données entre des centres de données Azure. Au lieu de cela, il propose de synchronisation de données SQL, un service que vous pouvez utiliser pour effectuer cette action. [Figure 4](#Fig4) montre à quoi il ressemble.

<a name="Fig4"></a>![Diagramme illustrant la synchronisation de données SQL][SQL-datasync]
 
**Figure 4 : Synchronisation des données SQL synchronise les données dans la base de données SQL avec des données dans d’autres Azure et centres de données locale.**

Comme indiqué dans le diagramme, synchronisation de données SQL peut synchroniser des données à différents endroits. Supposons que vous exécutez une application dans plusieurs centres de données Azure, par exemple, avec les données stockées dans la base de données SQL. Vous pouvez utiliser la synchronisation de données SQL conserver ces données synchronisées. Synchronisation de données SQL peuvent également synchroniser des données entre un centre de données Azure et une instance de SQL Server en cours d’exécution dans un centre de données locale. Cela peut être utile pour maintenir une copie locale des données utilisées par les applications en local et une copie de cloud utilisé par les applications qui s’exécutent sur Azure. Et bien qu’il n’est pas affichée dans l’illustration, synchronisation de données SQL peuvent également être utilisée pour synchroniser des données entre la base de données SQL et SQL Server s’exécutant sur un ordinateur virtuel Azure ou un autre emplacement.

La synchronisation peut être bidirectionnelle, et vous déterminez exactement quelles données sont synchronisées et la fréquence à laquelle il est traité. (La synchronisation entre les bases de données n’est pas atomique, cependant, il y a toujours au moins un délai). Cependant, il est utilisé, configuration de la synchronisation avec la synchronisation de données SQL est entièrement configuration par l’effort ; Il n’existe pas de code pour écrire.


### <a name="datarpt"></a>Création de rapports de données SQL à l’aide de Machines virtuelles

Une fois qu’une base de données contient des données, il ne faut souhaiterez probablement créer des rapports à l’aide de ces données. Azure peut exécuter SQL Server Reporting Services (SSRS) dans Azure Machines virtuelles, qui équivaut à l’exécution de SQL Server Reporting Services locaux. Vous pouvez ensuite utiliser SSRS pour exécuter des rapports sur des données stockées dans une base de données SQL Azure.  [Figure 5](#Fig5) montre comment fonctionne le processus.

<a name="Fig5"></a>![Diagramme de création de rapports SQL][SQL-report]
 
**Figure 5 : SQL Server Reporting Services en cours d’exécution dans un Machines virtuelles Azure fournit reporting services pour les données dans la base de données SQL. .**

Avant d’un utilisateur peut consulter un rapport, une personne définit ce que ce rapport doit ressembler à (étape 1). Avec SSRS sur un ordinateur virtuel, cela peut être effectuée à l’aide d’une des deux outils : SQL Server Data Tools, une partie de SQL Server 2012 ou son prédécesseur, Business Intelligence (BI) Development Studio. Comme avec SSRS, ces définitions de rapport sont exprimées dans les rapports RDL (Definition Language). Après avoir créé les fichiers RDL pour un rapport, qu’elles sont téléchargées sur un ordinateur virtuel dans le cloud (étape 2). La définition de rapport est maintenant prête à utiliser.

Ensuite, un utilisateur de l’application accède à l’état (étape 3). L’application passe cette demande de SSRS VM (étape 4), qui contacte SQL de base de données ou d’autres sources de données pour obtenir les données dont il a besoin (étape 5). SSRS utilise ces données et les fichiers RDL pertinents pour le rendu du rapport (étape 6), puis renvoie le rapport à l’application (étape 7), qui s’affiche à l’utilisateur (étape 8).

Incorporation d’un rapport dans une application, le scénario illustré ici, n’est pas la seule option. Il est également possible d’afficher des rapports dans le Gestionnaire de rapports sur l’ordinateur virtuel, SharePoint sur l’ordinateur virtuel ou d’une autre façon. Rapports peuvent également être combinées, avec un seul rapport contenant un lien vers un autre.

SSRS sur un ordinateur virtuel Azure vous offre des fonctionnalités complètes comme une solution de création de rapports dans le cloud. Les rapports peuvent utiliser n’importe quelle source de données pris en charge par SSRS. Applications et rapports peuvent inclure un code incorporé ou assemblys pour prendre en charge des comportements par défaut. Rendu et l’exécution du rapport sont rapides, car le moteur et le contenu du serveur rapport fonctionnent ensemble sur le même serveur virtuel.



## <a name="tblstor"></a>Stockage de table

Données relationnelles sont utiles dans de nombreux cas, mais il n’est pas toujours la meilleure option. Si votre application a besoin d’un accès rapide et simple de très grandes quantités de données structurées librement, par exemple, une base de données relationnelle peut ne pas fonctionne correctement. Une technologie NoSQL est susceptible d’être une meilleure option.

Stockage de Table Azure est un exemple de ce type de NoSQL approche. Malgré son nom, stockage de tables non prises en charge des tables relationnelles standards. Il propose à la place, ce qui est appelé *clé/valeur de stocker*, associant un ensemble de données avec une clé particulière, puis vous permettant d’accès d’une application ces données grâce à la clé. [Figure 6](#Fig6) illustre les notions de base.

<a name="Fig6"></a>![Diagramme de stockage de table][SQL-tblstor]
 
**Figure 6 : Stockage de Table Azure est un magasin clé/valeur qui offre un accès simple de grandes quantités de données rapide.**

Comme des objets BLOB, chaque table est associée à un compte de stockage Azure. Tables sont également appelées beaucoup comme des objets BLOB présentant une URL du formulaire

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Comme le montre la figure, chaque table est divisée en plusieurs partitions, chacun d'entre eux pouvant être stocké sur un ordinateur distinct. (Cela est une forme d’ont, comme avec la fédération SQL.) Les applications Azure et en cours d’exécution ailleurs peuvent accéder à une table à l’aide du protocole OData RESTful ou la bibliothèque de Client de stockage Azure.

Chaque partition d’une table conserve certains nombre d' *entités*, chacun d’eux contenant jusqu'à 255 *Propriétés*. Chaque propriété a un nom, un type (par exemple, binaire, Bool, DateTime, ent ou chaîne) et une valeur. Contrairement aux stockage relationnel, ces tables ne possèdent aucun schéma fixe, et donc différentes entités dans la même table peuvent contenir des propriétés avec des types différents. Une seule entité peut-être simplement une propriété de type chaîne contenant un nom, par exemple, alors qu’une autre entité dans la même table a deux propriétés Int contenant un numéro d’identification du client et une évaluation de crédit.

Pour identifier une entité particulière dans un tableau, une application des clés de cette entité. La clé comporte deux parties : une *clé de partition* qui identifie une partition spécifique et une *clé de ligne* qui identifie une entité qu’elle contient. [Figure 6](#Fig6), par exemple, le client demande l’entité avec partition clé A et la clé de la ligne 3 et stockage de tables renvoie entité, y compris l’ensemble des propriétés qu’elle contient.

Cette structure vous permet de tables diviser - une seule table peut contenir jusqu'à 100 To de données, et il permet un accès rapide aux données qu’ils contiennent. Il affiche également des limitations, cependant. Par exemple, il n’existe aucune prise en charge les transactions mises à jour qui s’étalent sur tableaux ou des paires partitions dans une seule table. Un ensemble de mises à jour à une table peut uniquement être regroupé en une transaction atomique si toutes les entités impliquées se trouvent dans la même partition. Il n’est également aucun moyen d’une table de requête basée sur la valeur de ses propriétés, ni aucune prise en charge des jointures entre plusieurs tables. Et à la différence des bases de données relationnelles, tables n’ont aucune prise en charge pour les procédures stockées.

Stockage de Table Azure est un bon choix pour les applications nécessitant un accès rapide et bon marché de grandes quantités de données la plus structurées. Par exemple, une application Internet qui stocke des informations de profil pour un grand nombre d’utilisateurs peut utiliser des tableaux. Un accès rapide est important dans ce cas, et l’application ne probablement besoin de la puissance de SQL. Abandonner cette fonctionnalité pour obtenir la vitesse et la taille peut être parfois intéressant et stockage de Table n’est donc la solution pour certains problèmes.


## <a name="hadoop"></a>Hadoop

Organisations ont été création data warehouse depuis des décennies. Ces collections d’information, souvent stockés dans des tables relationnelles, permettre aux utilisateurs de travailler avec et apprenez à partir des données de différentes manières. Avec SQL Server, par exemple, il est courant d’utiliser des outils tels que SQL Server Analysis Services pour effectuer cette action.

Mais supposons que vous voulez effectuer une analyse des données non relationnelles. Vos données peuvent prendre plusieurs formes : informations de capteurs ou balises RFID, les fichiers journaux dans des batteries de serveurs, données de parcours obtenues par les applications web, des images à partir d’appareils de diagnostics médicales et bien plus encore. Ces données peuvent également être très volumineuses, trop grand pour être utilisé de manière efficace avec un entrepôt de données traditionnel. Problèmes de données volumineux comme ceci, rares quelques années sont maintenant devenus assez courants.

Pour analyser ce type de données volumineux, notre secteur a largement rien donné sur une solution : la technologie open source Hadoop. Hadoop s’exécute sur un cluster d’ordinateurs physiques ou virtuels, propagation les données qu’il fonctionne sur entre ces machines et traitement en parallèle. Les ordinateurs plus Hadoop doit utiliser, plus vite compléter quel que soit le travail fait.

Ce type de problème est naturellement le nuage public. Au lieu de conserver un machines virtuelles armée de serveurs qui peuvent restez inactif plupart du temps, en cours d’exécution Hadoop dans le cloud vous permet de créer en local (et de rémunération du) uniquement lorsque vous avez besoin. Mieux encore, plus et les données de la vue que vous souhaitez analyser avec Hadoop est créée dans le cloud, vous évite se déplacer parmi. Pour vous aider à exploiter ces synergies, Microsoft fournit un service Hadoop sur Azure. [Figure 7](#Fig7) affiche les éléments les plus importants de ce service.

<a name="Fig7"></a>![Diagramme d’hadoop][hadoop]

**Figure 7 : Hadoop sur Azure exécute tâches MapReduce traitent les données en parallèle à l’aide de plusieurs machines virtuelles.**

Pour utiliser Hadoop sur Azure, vous demandez tout d’abord cette plateforme cloud pour créer un cluster Hadoop, indiquant le nombre de machines virtuelles que vous avez besoin. Configuration d’un cluster Hadoop vous-même est une tâche non simple, et donc laisser Azure faire pour vous a du sens. Lorsque vous avez terminé utilisant le cluster, vous arrêtez. Il est inutile de payer pour les ressources de calcul que vous n’utilisez pas.

Une application Hadoop, c'est-à-dire, à une *tâche*, utilise un modèle de programmation appelé *MapReduce*. Comme le montre la figure, la logique pour une tâche MapReduce s’exécute simultanément dans l’ensemble de machines virtuelles. Traitement des données en parallèle, Hadoop analyse des données beaucoup plus rapidement que les solutions seul ordinateur.

Sous Azure, les données un MapReduce travail fonctionne sur est généralement conservée dans le stockage blob. Dans Hadoop, cependant, travaux MapReduce attendez données soient stockées dans le *Système de fichier distribué Hadoop (HDFS)*. HADOOP est semblable au stockage d’objets Blob dans une certaine mesure ; Il réplique les données sur plusieurs serveurs physiques, par exemple. Plutôt que dupliquer cette fonctionnalité, Hadoop sur Azure expose à la place stockage d’objets Blob via l’API HADOOP, comme le montre l’illustration. Tandis que la logique d’un travail MapReduce pense qu’il accède à des fichiers HADOOP ordinaires, le travail est en réalité utilisation des données diffusés en continu vers celle-ci à partir d’objets BLOB. Et pour prendre en charge le cas où plusieurs tâches sont exécutent sur les mêmes données, Hadoop sur Azure permettent également copier les données d’objets BLOB dans HADOOP complet en cours d’exécution sur les ordinateurs virtuels. 

MapReduce travaux est généralement écrits en Java aujourd'hui, une approche Hadoop sur Azure prend en charge. Microsoft a ajouté également de prise en charge pour créer des travaux MapReduce dans d’autres langues, y compris c#, F # et JavaScript. L’objectif consiste à rendre cette technologie de données volumineux plus facilement accessibles à un plus grand groupe de développeurs.

Ainsi que des HADOOP et MapReduce, Hadoop inclut les autres technologies permettre aux utilisateurs d’analyser des données sans écrire une tâche MapReduce eux-mêmes. Par exemple, cochon est un langage de haut niveau conçu pour analyser les données volumineux, tandis que Hive offre un langage similaire à SQL appelé HiveQL. Cochon et Hive effectivement génèrent des travaux MapReduce qui traitent des données HADOOP, mais ils masquent cette complexité à leurs utilisateurs. Les deux sont fournis avec Hadoop sur Azure.

Microsoft fournit également un pilote HiveQL pour Excel. Utilise un complément Excel, analystes créez HiveQL requêtes (et donc travaux MapReduce) directement à partir d’Excel, puis traiter et visualiser les résultats à l’aide de PowerPivot et autres outils Excel. Hadoop sur Azure inclut les autres technologies également, telles que l’apprentissage bibliothèques Mahout, le système d’exploration graph Pegasus et autres éléments de l’ordinateur.

Analyse de données volumineux est important, et donc Hadoop est également importante. En fournissant Hadoop un service géré sur Azure, ainsi que des liens vers des outils familiers tels que Excel, Microsoft vise à rendre cette technologie accessible à un ensemble d’utilisateurs plus vaste.

Plus générale, les données de toutes sortes sont importantes. C’est pourquoi Azure inclut une gamme d’options pour analytique stratégique et de gestion de données. Nom de l’application que vous tentez de créer, il est probable que vous trouverez quelque chose dans cette plateforme en nuage qui est prises en charge pour vous.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

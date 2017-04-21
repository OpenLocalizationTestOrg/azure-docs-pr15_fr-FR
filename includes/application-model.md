# <a name="compute"></a>Cluster

Azure permet à déployer et surveiller votre code de l’application en cours d’exécution à l’intérieur d’un centre de données Microsoft. Lorsque vous créez une application et exécutez-le sur Azure, le code et la configuration gagnante est appelée un Azure hébergé service. Services hébergés sont faciles à gérer, mettre à l’échelle monter et Descendre, reconfigurer et mettre à jour avec les nouvelles versions de code de votre application. Cet article se concentre sur la Azure hébergé modèle d’application de service.<a id="compare" name="compare"></a>

## Table des matières<a id="_GoBack" name="_GoBack"></a>

-   [Avantages de modèle d’Application Azure][]
-   [Service hébergé des concepts clés][]
-   [Considérations relatives à la conception Service hébergé][]
-   [Conception de votre Application pour échelle][]
-   [Définition de Service hébergé et Configuration][]
-   [Le fichier de définition de Service][]
-   [Le fichier de Configuration de Service][]
-   [Créer et déployer un Service hébergé][]
-   [Références][]

## <a id="benefits"> </a>Avantages de modèle d’Application azure

Lorsque vous déployez votre application comme un service hébergé, Azure crée une ou plusieurs machines virtuelles (machines virtuelles) qui contiennent le code de votre application et les ordinateurs virtuels est lancé sur machines physiques résidant dans un des centres de données Azure. Lorsque les demandes de client à votre application hébergée Entrez le centre de données, un équilibrage de charge distribue ces demandes de manière égale sur les ordinateurs virtuels. Alors que votre application est hébergée dans Azure, il obtient trois principaux avantages :

-   **Disponibilité.** Haute disponibilité signifie Qu'azure garantit que votre application est en cours d’exécution autant que possible et est en mesure de répondre aux demandes des clients. Si votre application se termine (en raison d’une exception de non gérée, par exemple), puis Azure permettra de le détecter et il sera automatiquement redémarrer votre application. Si l’ordinateur de votre application est en cours d’exécution sur l’expérience de quelconque d’une défaillance matérielle, puis Azure est également détecter et automatiquement créer un nouvel ordinateur virtuel sur un autre ordinateur physique travail et exécutez votre code à partir de là. Remarque : Pour que votre application obtenir le contrat de niveau de Service de Microsoft de 99,95 % disponibles, vous devez disposer au moins deux machines virtuelles votre code de l’application en cours d’exécution. Cela permet une machine virtuelle traiter les demandes des clients tandis qu’Azure déplace votre code à partir d’un ordinateur virtuel en échec pour une machine virtuelle nouvelle, correctes.

-   **Extensibilité élevées.** Azure vous permet de facilement et modifier le nombre de machines virtuelles votre code de l’application en cours d’exécution pour gérer la charge réelle étant placée votre application de façon dynamique. Cela vous permet d’ajuster votre application de la charge de travail que vos clients sont placer dessus tout en protégeant votre uniquement pour les ordinateurs virtuels que vous avez besoin lorsque vous en avez besoin. Lorsque vous souhaitez modifier le nombre de machines virtuelles, Azure répond quelques minutes, ce qui permet de modifier de façon dynamique le nombre de machines virtuelles en cours d’exécution aussi souvent comme vous le souhaitez.

-   **Facilité de gestion.** Azure étant une plateforme en tant que Service (PaaS) offrant, il gère l’infrastructure (matériel lui-même, électricité et mise en réseau) tenu de conserver ces machines en cours d’exécution. Azure gère également la plateforme, garantissant un système d’exploitation à jour avec tous les bons correctifs et mises à jour de sécurité, ainsi que mises à jour de composant tels que le .NET Framework et Internet Information Server. Étant donné que tous les ordinateurs virtuels exécutent Windows Server 2008, Azure fournit des fonctionnalités supplémentaires telles que la surveillance des diagnostics, support Bureau à distance, pare-feu et configuration de magasin de certificats. Toutes ces fonctionnalités sont fournies à aucun frais supplémentaires. En fait, lorsque vous exécutez votre application dans Azure, la licence de système d’exploitation (OS) de Windows Server 2008 est incluse. Dans la mesure où tous les ordinateurs virtuels exécutent Windows Server 2008, de code qui s’exécute sur Windows Server 2008 fonctionne parfaitement lors de l’exécution dans Azure.

## <a id="concepts"> </a>Hébergé Service des concepts clés

Lorsque votre application est déployée comme un service hébergé dans Azure, il s’exécute à une ou plusieurs *rôles.* Un *rôle* simplement fait référence à la configuration et les fichiers d’application. Vous pouvez définir un ou plusieurs rôles pour votre application, chacune avec son propre jeu de fichiers d’application et de configuration. Pour chaque rôle dans votre application, vous pouvez spécifier le nombre de machines virtuelles ou *instances de rôle*, pour exécuter. L’illustration ci-dessous illustrent deux simple d’une application modèle en tant qu’un service hébergé à l’aide des rôles et les instances de rôle.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figure 1 : Un seul rôle avec trois instances (machines virtuelles) en cours d’exécution dans un centre de données Azure

![image][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figure 2 : Deux rôles, chacune avec deux instances (machines virtuelles), en cours d’exécution dans un centre de données Azure

![image][1]

Instances de rôle généralement traitent les demandes des clients Internet saisie du centre de données à ce qui est appelé un *point de terminaison d’entrée*. Un seul rôle peut avoir des points de terminaison de 0 ou plus d’entrée. Chaque point de terminaison indique un protocole (HTTP, HTTPS ou TCP) et un port. Cette pratique est courante pour configurer un rôle pour que les deux extrémités d’entrée : HTTP à l’écoute sur les ports 80 et HTTPS sur le port 443. L’illustration suivante montre un exemple de deux différents rôles avec dirigeant les demandes des clients vers les différents points de terminaison d’entrée.

![image][2]

Lorsque vous créez un service hébergé dans Azure, il reçoit une adresse IP publiquement dédiée clients peuvent utiliser pour y accéder. Après avoir créé le service hébergé, vous devez également sélectionner un préfixe URL qui est mappé à l’adresse IP. Ce préfixe doit être unique que vous réservez pour l’essentiel le *préfixe*. URL cloudapp.net cette personne d’autre ne peut en aurez. Clients de communiquent avec vos instances de rôles à l’aide de l’URL. En règle générale, ne pas distribuer ou publier le *préfixe*Azure. cloudapp.net URL. À la place, vous acheter un nom de DNS de votre bureau d’enregistrement DNS de choix et configurer votre nom de DNS pour rediriger les demandes des clients à l’URL Azure. Pour plus d’informations, voir [configuration d’un nom de domaine personnalisé dans Azure][].

## <a id="considerations"> </a>Hébergé des éléments de conception de Service

Lorsque vous créez une application à exécuter dans un environnement cloud, il existe plusieurs considérations considérer comme latence, disponibilité et extensibilité élevées.

Choix de l’emplacement localiser votre code de l’application est un facteur important lors de l’exécution d’un service hébergé dans Azure. Il est courant pour déployer votre application aux centres de données les plus proches de vos clients à réduire la latence et obtenir les meilleurs résultats possibles.
Toutefois, vous pouvez choisir un centre de données plus proche de votre société ou plus près à vos données si vous avez des problèmes compétence ou juridiques sur vos données et dans lequel il réside. Il existe six centres de données dans le monde capable d’héberger votre code de l’application. Le tableau ci-dessous indique les emplacements disponibles :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Pays/région**

</td>
<td style="width: 200px;">
**Sous-secteurs**

</td>
</tr>
<tr>
<td>
États-Unis

</td>
<td>
Centre Sud et Amérique centrale

</td>
</tr>
<tr>
<td>
Europe

</td>
<td>
Amérique du Nord et Ouest

</td>
</tr>
<tr>
<td>
Pays d’Asie

</td>
<td>
Sud-est et Moyen-Orient

</td>
</tr>
</tbody>
</table>
Lorsque vous créez un service hébergé, vous sélectionnez une zone sous-adresse indiquant l’emplacement dans lequel vous souhaitez exécuter votre code.

Pour atteindre élevées, il est essentiel que les données de votre application conservées dans un référentiel central accessible à plusieurs instances de rôle. Pour faciliter cette tâche, Azure offre plusieurs options de stockage tels que des objets BLOB et tables de base de données SQL. Consultez l’article [Offres de stockage des données dans Azure][] pour plus d’informations sur ces technologies de stockage. L’illustration suivante montre la façon dont l’équilibrage de charge à l’intérieur du centre de données Azure distribue les demandes des clients sur des instances de rôle différent tous qui ont accès au même emplacement de stockage des données.

![image][3]

En règle générale, vous voulez placer votre code de l’application et de vos données dans le centre de données même comme cela permet de faible latence (meilleures performances) lorsque votre code de l’application accède aux données. En outre, vous ne seront pas imputés pour la bande passante lorsque les données sont déplacées au sein du même centre de données.

## <a id="scale"> </a>Conception de votre Application pour échelle

Vous pouvez être amené à prendre une application unique (par exemple, un site web simple) et faites-lui hébergé dans Azure. Mais fréquemment, votre application peut contenir plusieurs rôles qui collaborent. Par exemple, dans l’illustration ci-dessous, voici deux instances du rôle de site Web, trois instances de ce rôle de traitement des commandes et une instance du rôle du Générateur de rapports. Ces rôles sont travaillant ensemble et le code pour chacun d'entre eux peut être regroupé et déployé comme une seule unité jusqu'à Azure.

![image][4]

La principale raison pour fractionner une application en différents rôles chaque s’exécutant sur son propre jeu d’instances de rôle (autrement dit, machines virtuelles) est à l’échelle les rôles séparément. Par exemple, pendant les vacances, de nombreux clients peuvent être d’acheter des produits de votre société, afin que vous souhaiterez peut-être augmenter le nombre d’instances de rôle en cours d’exécution votre rôle de site Web, ainsi que le nombre d’instances de rôle votre rôle ordre de traitement en cours d’exécution. Après les vacances, vous pouvez obtenir un grand nombre de produits retournés, il vous faudra probablement toujours un grand nombre d’instances de site Web, mais moins de traitement des cas. Pendant le reste de l’année, vous devrez peut-être uniquement quelques le site Web et le traitement des instances. Dans l’ensemble de tout cela, vous devrez peut-être qu’une seule instance du Générateur de rapports. La flexibilité des déploiements basé sur un rôle dans Azure vous permet d’adapter facilement votre application à vos besoins professionnels.

Il est courant d’avoir le rôle instances au sein de votre service hébergé communiquent avec eux. Par exemple, le rôle de site Web accepte une commande client mais puis il libère l’ordre de traitement vers les instances de rôle de traitement des commandes. La meilleure façon de passer formulaire travail un ensemble d’instances de rôles dans un autre jeu d’instances à l’aide de la technologie files d’attente fournie par Azure, le Service de file d’attente ou files d’attente de Bus de Service. L’utilisation d’une file d’attente est une partie essentielle de l’article ici. La file d’attente permet au service hébergé à l’échelle de ses rôles séparément afin de pouvoir équilibrer la charge de travail et coût. Si le nombre de messages dans la file d’attente augmente au fil du temps, vous pouvez évoluer vers le nombre d’instances de rôle traitement des commandes. Si le nombre de messages dans la file d’attente diminue au fil du temps, vous pouvez évoluer vers le bas le nombre d’instances de rôle traitement des commandes. Ainsi, vous seulement payez pour les instances requises pour gérer la charge de travail réel.

La file d’attente fournit également la fiabilité. Lors de la mise à l’échelle vers le bas le nombre d’instances de rôle traitement des commandes, Azure décide les instances à se terminer. Il peut décider de terminer une instance qui se trouve au milieu d’un message file d’attente de traitement. Toutefois, étant donné que le traitement des messages ne se termine pas correctement, le message devient visible à nouveau pour un autre traitement des commandes d’instance de rôle qui prenne et la traite. En raison de visibilité de message file d’attente, les messages sont assurées de finalement traités. La file d’attente joue également un équilibrage de charge en distribuer efficacement ses messages avec des instances de tous les rôles qui vous demandent des messages à partir de celui-ci.

Pour les instances de rôle de site Web, vous pouvez surveiller le trafic entrant sur les et décider de réduire le nombre d'entre eux vers le haut ou vers le bas également. La file d’attente vous permet de mettre à l’échelle le nombre d’instances de rôle de site Web indépendamment des instances de rôle de traitement des commandes. Ceci est très puissante et vous offre une grande flexibilité. Bien entendu, si votre application comporte des rôles supplémentaires, vous pouvez ajouter des files d’attente supplémentaires en tant que le canal d’échange pour communiquer entre eux afin de tirer parti de la même échelle et avantages de coûts.

## <a id="defandcfg"> </a>Hébergé définition de Service et Configuration

Déploiement d’un service hébergé sur Azure nécessite que vous disposez également d’un fichier de définition de service et un fichier de configuration de service. Ces deux fichiers sont des fichiers XML, et ils permettent de spécifier les options de déploiement pour votre service hébergé de manière déclarative. Fichier de définition du service décrit tous les rôles qui composent votre service hébergé et comment ils communiquent. Le fichier de configuration de service décrit le nombre d’instances pour chaque rôle et les paramètres utilisés pour configurer chaque instance de rôle.

## <a id="def"> </a>Le fichier de définition de Service

Comme mentionné précédemment, la définition du service (CSDEF) le fichier est un fichier XML qui décrit les différents rôles qui composent votre application. Le schéma complet pour le fichier XML sont accessibles ici : [] [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx].
Le fichier CSDEF contient un élément WebRole ou WorkerRole pour chaque rôle que vous souhaitez inclure dans votre application. Déploiement d’un rôle en tant qu’un rôle web (à l’aide de l’élément WebRole) signifie que le code s’exécutera sur une instance de rôle contenant Windows Server 2008 et Internet Information Server (IIS).
Déploiement d’un rôle comme un rôle de collaborateur (à l’aide de l’élément WorkerRole) signifie que l’instance de rôle Windows Server 2008 dessus (IIS ne sera pas installé).

Vous pouvez certainement créer et déployer un rôle de collaborateur qui utilise un autre mécanisme pour écouter les demandes web (par exemple, votre code peut créer et utiliser un HttpListener .NET). Dans la mesure où les instances de rôle sont exécutant Windows Server 2008, votre code peut effectuer toutes les opérations sont normalement disponibles à l’application en cours d’exécution sur Windows Server
2008.

Pour chaque rôle, vous indiquez la taille de mémoire virtuelle souhaitée que les instances de ce rôle doivent utiliser. Le tableau ci-dessous indique les différentes tailles de mémoire virtuelle disponibles sur le marché et les attributs de chaque :

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Taille de mémoire virtuelle**

</td>
<td>
**PROCESSEUR**

</td>
<td>
**RAM**

</td>
<td>
**Disque**

</td>
<td>
**E/s réseau de pointe**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Très petite**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MO

</td>
<td>
20 GO

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**MINUSCULE**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GO

</td>
<td>
225 GO

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Moyenne**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GO

</td>
<td>
490 GO

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande taille**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GO

</td>
<td>
1 TO

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Très grande**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GO

</td>
<td>
2 TO

</td>
<td>
\~800 Mbit/s

</td>
</tr>
</tbody>
</table>
Vous êtes chargé de toutes les heures pour chaque machine virtuelle vous utilisez comme une instance de rôle et vous soyez également facturées toutes les données que vos instances de rôles envoient à l’extérieur du centre de données. Vous ne seront pas imputés pour les données de saisie du centre de données. Pour plus d’informations, voir [Azure tarifs] []. En règle générale, il est recommandé d’utiliser plusieurs instances de rôle petite plutôt que d’utiliser plusieurs instances de grande taille afin que votre application soit plus résistante aux défaillances. Après tout, les instances de rôle moins que vous avez, est la plus désastreux un échec de l’un d’eux à votre application globale. En outre, comme mentionné précédemment, vous devez déployer au moins deux instances pour chaque rôle afin d’obtenir le contrat de niveau de service 99,95 % Microsoft fournit.

Le fichier de définition (CSDEF) service est également vous spécifierez nombreux attributs sur chaque rôle dans votre application. Voici quelques-unes des éléments plus utiles à votre disposition :

-   **Certificats**. Vous utilisez des certificats pour le chiffrement des données ou si votre service web prend en charge SSL. Les certificats doivent être téléchargés vers Azure. Pour plus d’informations, voir [Gestion des certificats dans Azure][]. Ce paramètre XML installe les certificats précédemment téléchargés dans le magasin de certificats de l’instance de rôle afin qu’ils soient utilisables par votre code de l’application.

-   **Les noms de paramètre de configuration**. Pour les valeurs que vous souhaitez que vos applications à lire pendant l’exécution sur une instance de rôle. La valeur réelle des paramètres de configuration est définie dans le fichier de configuration (CSCFG) service qui peut être mis à jour à tout moment sans avoir à redéployez votre code. En fait, vous pouvez coder vos applications de manière à détecter les valeurs de configuration modifiés sans subir d’interruption de service.

-   **Points de terminaison de la saisie**. Cette section vous indiquez les HTTP, HTTPS ou TCP points de terminaison (avec ports) que vous souhaitez exposer à l’extérieur via votre *préfixe*. cloadapp.net URL. Lorsqu’Azure déploie votre rôle, il va configurer automatiquement le pare-feu sur l’instance de rôle.

-   **Points de terminaison internes**. Ici, spécifier tout HTTP ou TCP points de terminaison de votre choix exposés à d’autres instances de rôle déployées dans le cadre de votre application. Points de terminaison internes autoriser toutes les instances de rôle dans votre application pour communiquer avec eux, mais ne sont pas accessibles à toutes les instances de rôle qui se trouvent en dehors de votre application.

-   **Modules d’importation**. Ces également installer composants utiles sur vos instances de rôles. Composants existent pour une analyse des diagnostics, Bureau à distance et Azure Connect (qui permet à votre instance de rôle d’accéder aux ressources locales via un canal sécurisé).

-   **Stockage local**. Permet d’allouer un sous-répertoire dans l’instance de rôle de votre application à utiliser. Il est décrit plus en détail dans l’article [Offres de stockage des données dans Azure][] .

-   **Tâches de démarrage**. Tâches de démarrage permettent d’installer les composants requis sur une instance de rôle qu’il démarre. Les tâches peuvent s’exécuter avec élévation de privilèges en tant qu’administrateur si nécessaire.

## <a id="cfg"> </a>Le fichier de Configuration de Service

Le fichier de configuration (CSCFG) service est un fichier XML qui décrit les paramètres qui peuvent être modifiées sans redéployez votre application. Le schéma complet pour le fichier XML sont accessibles ici : [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
Le fichier CSCFG contient un élément de rôle pour chaque rôle dans votre application. Voici certains des éléments que vous pouvez spécifier dans le fichier CSCFG :

-   **Version du système d’exploitation**. Cet attribut vous permet de sélectionner la version du système d’exploitation (OS) souhaitée utilisée pour toutes les instances de rôle votre code de l’application en cours d’exécution. Ce système d’exploitation est connu sous *système d’exploitation invité*, et chaque nouvelle version inclut les derniers correctifs de sécurité et les mises à jour au moment où que la système d’exploitation invité est publiée. Si vous affectez la valeur de l’attribut osVersion «\*», puis Azure met automatiquement à jour la système d’exploitation invité sur chacun de vos instances de rôles comme nouvelles versions du système d’exploitation invité deviennent disponibles. Toutefois, vous pouvez cesser d’utiliser mises à jour automatiques en sélectionnant spécifique qu’invité de la version du système d’exploitation. Par exemple, si l’attribut osVersion à une valeur de « WA-invité-OS-2,8\_201109-01 » entraîne toutes les instances de votre rôle obtenir ce qui est décrit dans cette page web : [http://msdn.microsoft.com/library/hh560567.aspx][]. Pour plus d’informations sur les versions du système d’exploitation invité, voir [Gestion des mises à niveau vers le système d’exploitation des invités Azure].

-   **Instances**. Valeur de cet élément indique le nombre d’instances de rôle souhaité généré le code d’un rôle donné en cours d’exécution. Dans la mesure où vous pouvez télécharger un fichier CSCFG sur Azure (sans redéployez votre application), il est plus simplement simple à modifier la valeur pour cet élément, puis télécharger un nouveau fichier CSCFG pour dynamiquement augmenter ou diminuer le nombre d’instances de rôle votre code de l’application en cours d’exécution. Cela vous permet d’évoluer votre application vers le haut ou vers le bas jusqu'à la charge de travail réel répondre aux demandes tout en contrôlant également combien vous êtes chargé pour l’exécution d’instances de rôle.

-   **Valeurs de paramètre de configuration**. Cet élément indique les valeurs de paramètres (tel que défini dans le fichier CSDEF). Votre rôle peut lire ces valeurs en cours d’exécution. Ces valeurs de paramètres de configuration sont généralement utilisés pour les chaînes de connexion à la base de données SQL ou au stockage Azure, mais ils peuvent être utilisés à des fins de que votre choix.

## <a id="hostedservices"> </a>Créer et déployer un Service hébergé

La création d’un service hébergé nécessite que vous accédez au [Portail de gestion Azure] tout d’abord et mise en service d’un service hébergé en spécifiant un préfixe DNS et le centre de données vous voulez finalement votre code en cours d’exécution. Puis, dans votre environnement de développement, vous créez votre fichier de définition (CSDEF) service, intégrer votre code de l’application et le package (zip) tous ces fichiers dans un fichier de package (CSPKG) service. Vous devez également préparer votre fichier de configuration (CSCFG) service. Pour déployer votre rôle, vous téléchargez les fichiers CSPKG et CSCFG avec l’API de gestion du Service Azure. Une fois déployé, Azure, sera mise en service des instances de rôle dans le centre de données (basé sur les données de configuration), extraire votre code de l’application à partir du package, copiez-le dans les instances de rôle et les instances de démarrage. À présent, votre code est en cours d’exécution.

L’illustration suivante montre les fichiers CSPKG et CSCFG que vous créez sur votre ordinateur de développement. Le fichier CSPKG contient le fichier CSDEF et le code de deux rôles. Après avoir téléchargé les fichiers CSPKG et CSCFG avec l’API de gestion du Service Azure, Azure crée des instances de rôle du centre de données. Dans cet exemple, le fichier CSCFG indiqué que Azure doit créer trois instances de rôle \#1 et deux instances de rôle \#2.

![image][5]

Pour plus d’informations sur le déploiement, la mise à niveau et reconfigurer vos rôles, voir l’article [déploiement et mise à jour des Applications Azure][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Références

-   [Création d’un Service hébergé pour Azure][]

-   [Gestion des Services hébergés dans Azure][]

-   [Migration des Applications vers Azure][]

-   [Configuration d’une Application Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Rédigé par Jeffrey Richter (Wintellect)</p>

</div>

  [Avantages de modèle d’Application Azure]: #benefits
  [Service hébergé des concepts clés]: #concepts
  [Considérations relatives à la conception Service hébergé]: #considerations
  [Conception de votre Application pour échelle]: #scale
  [Définition de Service hébergé et Configuration]: #defandcfg
  [Le fichier de définition de Service]: #def
  [Le fichier de Configuration de Service]: #cfg
  [Créer et déployer un Service hébergé]: #hostedservices
  [Références]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuration d’un nom de domaine personnalisé dans Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Offres de stockage des données dans Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Gestion des certificats dans Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.Microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.Microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Gestion des mises à niveau vers les invités Azure du système d’exploitation]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portail de gestion Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Déploiement et la mise à jour des Applications Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Création d’un Service hébergé pour Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gestion des Services hébergés dans Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migration des Applications vers Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configuration d’une Application Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx

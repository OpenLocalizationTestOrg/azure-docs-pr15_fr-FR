<properties 
    pageTitle="Fonctionnalités de système d’exploitation service d’application Azure" 
    description="En savoir plus sur les fonctionnalités du système d’exploitation disponibles pour les applications web, les serveurs principaux de l’application mobile et applications API service d’application Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Fonctionnalités de système d’exploitation service d’application Azure #

Cet article décrit les fonctionnalités de système d’exploitation référence courantes qui ne sont disponible pour toutes les applications en cours d’exécution sur [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714). Cette fonctionnalité inclut le fichier, réseau, accès au Registre et les journaux de diagnostics et des événements. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>Niveaux de plan de Service d’application

Application Service s’exécute client applications dans un environnement d’hébergement client multiples. Applications déployées dans les niveaux de stockage **gratuit** , **Shared** s’exécuter dans le processus de travail sur machines virtuelles partagés, tandis que les applications déployées dans les couches **Premium** et **Standard** exécuter sur des ordinateurs virtuels dédiés spécialement pour les applications associées à un seul client.

Service d’application prenant en charge une expérience transparente mise à l’échelle entre les différents niveaux, la configuration de sécurité appliquée pour les applications de Service d’application reste la même. Cela garantit qu’applications ne soudainement se comportent différemment, ignore de façon inattendue, lorsque le plan de services d’application bascule d’un niveau à un autre.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Structures de développement

Niveaux de tarification application Service contrôle la quantité de ressources cluster (processeur, disque, mémoire et sortie réseau) disponibles aux applications. Toutefois, la largeur de la fonctionnalité d’infrastructure disponible aux applications reste la même que les couches de mise à l’échelle.

Service d’application prend en charge une variété de structures de développement, y compris ASP.NET, ASP classique, node.js, PHP et Python - ce qui exécuter en tant qu’extensions dans IIS. Pour simplifier et normaliser la configuration de la sécurité, applications de Service d’application s’exécutent généralement les structures de développement différents avec leurs paramètres par défaut. Une approche à la configuration des applications aurait pu être personnaliser la surface d’API et les fonctionnalités pour chaque environnement de développement individuels. Service d’application permet d’accéder à la place une approche plus générique en activant un planning de référence courantes de système d’exploitation fonctionnalité quelle que soit la structure de développement d’une application.

Les sections suivantes synthétisent les types générales des fonctionnalités de système d’exploitation destinées aux applications de Service d’application.

<a id="FileAccess"></a>
##<a name="file-access"></a>Accès aux fichiers

Différents lecteurs existent au sein de Service d’application, y compris les lecteurs locaux et réseau.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Lecteurs locaux

Fondamentalement, application est un service en cours d’exécution en haut de l’infrastructure Azure PaaS (plateforme en tant que service). Par conséquent, les lecteurs locaux qui sont « joints » à une machine virtuelle sont les mêmes types de lecteur disponibles à n’importe quel rôle de travail en cours d’exécution dans Azure. Cela inclut un lecteur de système d’exploitation (le lecteur D:\), un lecteur d’application qui contient les fichiers de cspkg Package Azure utilisés en mode exclusif par application Service (et inaccessibles pour les clients) et un lecteur « utilisateurs » (le lecteur C:\), dont la taille varie selon la taille de la machine virtuelle.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Lecteurs réseau (aka partage UNC)

Un des aspects uniques de Service d’application qui simplifie le déploiement des applications et la maintenance est que tout le contenu utilisateur est stocké sur un jeu de partages UNC. Ce modèle mappe parfaitement au modèle de stockage de contenu utilisé par web local qui héberge les environnements qui disposent de plusieurs serveurs avec équilibrage de charge courantes. 

Dans le Service d’application, il existe nombre de partages UNC créés dans chaque centre de données. Un pourcentage du contenu utilisateur pour tous les clients dans chaque centre de données est affecté à chaque partage UNC. En outre, tous les le fichier de contenu pour un seul client est toujours placé sur le même chemin UNC partager. 

Notez qu’en raison de fonctionnement des services cloud, la machine virtuelle spécifique chargée d’héberger un partage UNC remplacera le temps. Il est assuré que partages UNC seront monter par différentes machines virtuelles car ils sont mis monter et descendre au cours des opérations de cloud normal. Pour cette raison, les applications doivent jamais vous hypothèses codé en dur que les informations de l’ordinateur dans un chemin UNC reste stables au fil du temps. En revanche, ils doivent utiliser la pratique *faux* chemin d’accès absolu **D:\home\site** qui fournit des services d’application. Ce chemin d’accès absolu faux offre une méthode portable, indépendant de l’application et d’utilisateur pour faire référence aux application personnels. À l’aide de **D:\home\site**, une peut se transférer des fichiers partagés à partir d’une application sans avoir à configurer un nouveau chemin d’accès absolu pour chaque transfert.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Types de fichier accès à une application

Chaque client a une structure de répertoire réservé sur un partage UNC spécifique au sein d’un centre de données. Un client peut avoir plusieurs applications créées au sein d’un centre des données spécifiques, afin que tous les répertoires appartenant à un seul compte client sont créés sur le même chemin UNC à partager. Le partage peut inclure les répertoires tels que ceux de contenu, d’erreur et les journaux de diagnostic et les versions antérieures de l’application créée par le contrôle de code source. Comme prévu, répertoires de l’application d’un client sont disponibles pour l’accès en lecture et écriture à l’exécution par code de l’application de l’application.

Sur les lecteurs locaux liés à la machine virtuelle qui s’exécute une application, application Service réserve un segment d’espace sur le lecteur C:\ pour le stockage local temporaire spécifiques à l’application. Même si une application a accès complet en lecture/écriture à son propre stockage local temporaire, que le stockage réellement n’est pas destiné à être utilisé directement par le code d’application. Au lieu de cela, l’objectif consiste à fournir le stockage de fichiers temporaires pour IIS et web structures d’application. Application Service limite également l’espace de stockage local temporaire disponible pour chaque application pour empêcher les applications individuelles de consommer excessive de stockage de fichiers locaux.

Deux exemples de l’application Service utilise le stockage local temporaire sont le répertoire des fichiers ASP.NET temporaires et le répertoire pour IIS dans des fichiers compressés. Le système de compilation ASP.NET utilise le répertoire « Fichiers temporaires ASP.NET » comme emplacement de cache de compilation temporaire. IIS utilise le répertoire « Fichiers compressés temporaires IIS » pour stocker la sortie de réponse compressé. Ces deux types de fichier l’utilisation (ainsi que d’autres personnes) sont remappés dans le Service d’application sur le stockage local temporaire per application. Ce remappage garantit que les fonctionnalités continue comme prévu.

Chaque application dans le Service d’application s’exécute comme une identité de processus de travail de limités uniques aléatoire appelée la « identité pool d’applications », décrite plus ici : [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Code de l’application utilise cette identité pour l’accès en lecture seule base sur le lecteur système d’exploitation (le lecteur D:\). Cela signifie que le code de l’application peut répertorier les structures de répertoire courantes et lire des fichiers courantes sur le lecteur système d’exploitation. Même si cela peut apparaître comme un niveau d’accès, le même répertoires et fichiers quelque peu étendu sont accessibles lorsque vous configurez un rôle de collaborateur dans un Azure service hébergé et lire le contenu du lecteur. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Accès aux fichiers sur plusieurs instances

Le répertoire de base contienne du contenu d’une application, et le code d’application peut écrire dans celui-ci. Si une application s’exécute sur plusieurs instances, le répertoire de base est partagé entre toutes les instances afin que toutes les instances de voir le même répertoire. Par conséquent, par exemple, si une application enregistre les fichiers téléchargés dans le répertoire de base, ces fichiers sont immédiatement disponibles à toutes les instances. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Accès réseau
Code de l’application peut utiliser TCP/IP et protocoles basé sur UDP pour rendre sortant connexions réseau aux points de terminaison accessibles de Internet qui exposent des services externes. Applications peuvent utiliser ces mêmes protocoles pour se connecter aux services de Azure & #151 ; par exemple, en créant un HTTPS connexions à la base de données SQL.

Il existe également une capacité limitée pour les applications établir une connexion en boucle locale et disposez d’une application écoutent ce socket en boucle locale. Cette fonctionnalité existe principalement pour activer les applications écoutent sockets en boucle locale dans le cadre de leurs fonctionnalités. Notez que chaque application voit une connexion en boucle « privé » ; application « A » ne peut pas écouter un socket en boucle locale établi par application « B ».

Canaux nommés sont également pris en charge comme un mécanisme de communication entre processus (IPC) entre les différents processus qui collectivement exécuter une application. Par exemple, le module IIS FastCGI repose sur canaux nommés pour coordonner les processus individuels qui s’exécutent pages PHP.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>L’exécution de code, processus et la mémoire
Comme indiqué précédemment, applications exécuter à l’intérieur de processus de travail limités à l’aide d’une identité du pool d’applications aléatoires. Code de l’application a accès à l’espace de mémoire associée le processus de travail, ainsi que tous les processus enfants qui peuvent être générées par processus CGI ou d’autres applications. Toutefois, une application ne peut pas accéder à la mémoire ou les données d’une autre application même si elle se trouve sur le même ordinateur virtuel.

Applications peuvent exécuter des scripts ou les pages écrites avec les structures de développement web pris en charge. Service d’application ne configurer des paramètres de framework web aux modes plus restreints. Par exemple, ASP.NET applications en cours d’exécution sur l’application de Service s’exécutent dans « illimitées » plutôt que d’utiliser un mode de gestion de la confidentialité plus restreint. Structures Web, y compris les ASP classique et ASP.NET, peuvent appeler des composants COM en cours de processus (mais pas d’expiration des composants COM processus) comme ADO (ActiveX Data Objects) qui sont enregistrés par défaut sur le système d’exploitation Windows.

Applications peuvent générer et exécuter du code arbitraire. Il est possible d’une application exécuter des éléments tels que générer une invite de commandes ou exécuter un script PowerShell. Toutefois, même si les processus et du code arbitraire peuvent être générées à partir d’une application, les scripts et des programmes exécutables sont toujours limités aux privilèges accordés au pool d’applications parent. Par exemple, une application peut créer un fichier exécutable qui effectue un appel sortant de HTTP, mais ce même exécutable ne peut pas tentez de supprimer la liaison l’adresse IP d’un ordinateur virtuel à partir de sa carte réseau. Effectuer un appel sortant réseau est autorisé à code limités, mais vous tentez de reconfigurer les paramètres du réseau sur un ordinateur virtuel nécessite des privilèges d’administrateur.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Les journaux de diagnostics et les événements
Informations de journal sont un autre jeu de données certaines applications essaient d’accéder. Les types d’informations de journal disponibles pour le code en cours d’exécution dans le Service d’application inclut des diagnostics et enregistrer les informations générées par une application qui est également facilement accessible à l’application. 

Par exemple, les journaux W3C HTTP générés par une application active sont disponibles sur un répertoire des journaux dans l’emplacement du partage réseau créée pour l’application, ou disponibles dans le stockage blob si un client a configuré journalisation W3C au stockage. Cette dernière option permet de grandes quantités de journaux soient regroupées sans risque de dépassant les limites de stockage de fichier associés à un partage réseau.

De la même manière, les informations de diagnostic en temps réel à partir des applications .NET peuvent également être enregistrées à l’aide de l’infrastructure de diagnostics et le suivi .NET avec des options pour écrire les informations de suivi au partage réseau de soit l’application, ou vous pouvez également cliquer à un emplacement de stockage blob.

Zones de diagnostics journalisation et de suivi qui ne sont pas disponibles aux applications sont des événements Windows ETW et journaux des événements Windows courants (par exemple, système, Application et sécurité les journaux d’événements). Dans la mesure où les informations de traçage ETW peuvent être potentiellement affichable échelle de l’ordinateur (avec l’utilisateurs à droite), en lecture et en écriture aux événements ETW sont bloqués. Les développeurs remarquerez peut-être des appels de API pour lire et écrire ETW événements et les journaux d’événements Windows courants apparaissent pour l’utiliser, mais que c’est parce que le Service d’application est « émulant « les appels afin qu’elles apparaissent pour réussir. En réalité, le code de l’application n’a pas accès aux données de cet événement.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Accès au Registre
Applications ont accès en lecture seule à l’essentiel (mais pas tous) du Registre de la machine virtuelle qu’ils exécutent sur. Dans la pratique, cela signifie que les clés de Registre qui autorisent l’accès en lecture seule pour le groupe d’utilisateurs locaux sont accessibles par les applications. Une zone du Registre qui n’est actuellement pas pris en charge pour la lecture ou un accès en écriture est la HKEY\_active\_hive utilisateur.

Accès en écriture dans le Registre est bloqué, y compris l’accès à des clés de Registre par utilisateur. Point de vue de l’application, accès en écriture dans le Registre doivent jamais être opposés dans l’environnement Azure dans la mesure où les applications pouvez (et faire) sont-elles migrées entre les différentes machines virtuelles. Le stockage accessible en écriture uniquement permanent qui peut être dépendent par une application est la structure de répertoire de contenu par application stockée sur les partages UNC de Service d’application. 

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

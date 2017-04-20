<properties
   pageTitle="Meilleures pratiques concernant les données sécurité et chiffrement | Microsoft Azure"
   description="Cet article fournit un ensemble de meilleures pratiques pour la sécurité des données et à l’aide de chiffrement intègre des fonctionnalités Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Meilleures pratiques de sécurité des données Azure et de chiffrement

Une des clés de protection des données dans le nuage est accounting pour les états possibles dans lequel vos données peuvent se produire, et quels contrôles sont disponibles pour cet état. En vue de données Azure sécurité chiffrement meilleures pratiques et les recommandations seront autour des États de données suivantes :

- Au repos : Cela inclut toutes les informations d’objets de stockage, des conteneurs et des types qui existent statique sur un support physique, être il magnétique ou un disque optique.

- Voies : Lorsque en cours transfert des données entre des composants, des emplacements ou des programmes, tels que sur le réseau, sur un bus de service (depuis local vers le cloud et vice versa, y compris les connexions hybride tels que ExpressRoute), ou lors d’un processus d’entrée/sortie, il est considéré comme étant en mouvement.

Dans cet article, nous allons étudier un ensemble de recommandations données Azure sécurité et de chiffrement. Les meilleures pratiques suivantes sont dérivées de notre expérience avec la sécurité des données Azure et de chiffrement et de l’expérience des clients tels que vous-même.

Pour chaque meilleure pratique, nous allons expliquer :

- Quelle est la meilleure pratique
- Raison pour laquelle vous souhaitez activer que les meilleures pratiques
- Quelle peut être le résultat si vous ne parvenez pas à activer les meilleures pratiques
- Alternatives possibles pour les meilleures pratiques
- Comment vous pouvez en savoir activer la meilleure pratique

Cet article Azure la sécurité des données et des pratiques recommandées de chiffrement est basé sur un avis consensus et les fonctionnalités de plateforme Azure et les ensembles de fonctionnalités, telles qu’elles existent au moment de la que rédaction de cet article. Avis et technologies changent au fil du temps et de cet article est mise à jour régulièrement afin de refléter ces modifications.

Données Azure sécurité et chiffrement recommandations mentionnées dans cet article sont les suivantes :

- Appliquer l’authentification multifacteur
- Accès contrôle basé sur un rôle d’utilisation
- Chiffrer machines virtuelles Azure
- Utiliser des modèles de sécurité matériel
- Gérer les postes de travail sécurisés
- Activer le chiffrement de données SQL
- Protéger les données lors des transferts
- Appliquer le chiffrement de données au niveau des fichiers


## <a name="enforce-multi-factor-authentication"></a>Appliquer l’authentification multifacteur

La première étape de l’accès aux données et le contrôle dans Microsoft Azure consiste à authentifier l’utilisateur. [Authentification multifacteur Azure Multifacteur](../multi-factor-authentication/multi-factor-authentication.md) est une méthode pour vérifier l’identité de l’utilisateur à l’aide d’une autre méthode qu’uniquement un nom d’utilisateur et un mot de passe. Cette authentification méthode permet divulgation d’informations l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple.

En activant l’authentification Multifacteur Azure pour vos utilisateurs, vous ajoutez un second niveau de sécurité pour les compléments d’authentification utilisateur et les transactions. Dans ce cas, une transaction peut accéder à un document situé dans un serveur de fichiers ou dans votre SharePoint Online. Permet également de l’authentification Multifacteur Azure pour réduire la probabilité qu’une information d’identification compromise a accès aux données de l’organisation.

Par exemple : Si vous appliquez l’authentification Multifacteur Azure pour vos utilisateurs et configurez pour utiliser un appel téléphonique ou message texte en tant que vérification, si les informations d’identification de l’utilisateur sont compromises, l’intrus ne pourrez accéder à toutes les ressources dans la mesure où il n’ont pas accès à téléphone de l’utilisateur. Les organisations qui n’ajoutent pas ce niveau de protection d’identité supplémentaire sont plus sensibles pour attaque de vol d’informations d’identification, qui peut entraîner compromis de données.

Une alternative pour les organisations qui souhaitent conserver l’authentification contrôle local consiste à utiliser le [Serveur de l’authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), également appelée l’authentification Multifacteur locaux. À l’aide de cette méthode vous pourrez toujours appliquer l’authentification multifacteur, tout en conservant l’authentification Multifacteur server en local.

Pour plus d’informations sur l’authentification Multifacteur Azure, veuillez lire l’article [mise en route avec l’authentification multifacteur Azure dans le cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Accès contrôle basé sur un rôle d’utilisation
Limiter l’accès basé sur les principes de sécurité [devez connaître](https://en.wikipedia.org/wiki/Need_to_know) et [moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) . Il s’agit impérative pour les organisations qui souhaitent d’appliquer des stratégies de sécurité pour accéder aux données. Azure contrôle d’accès basé sur un rôle (RBAC) peut servir à affecter des autorisations aux utilisateurs, des groupes et des applications à une certaine étendue. La portée d’une attribution de rôle peut être un abonnement, un groupe de ressources ou une ressource.

Vous pouvez tirer parti [des rôles RBAC intégrés](../active-directory/role-based-access-built-in-roles.md) dans Azure pour affecter des privilèges aux utilisateurs. Envisagez d’utiliser des *Collaborateurs de compte de stockage* pour les opérateurs cloud qui nécessaires pour gérer les comptes de stockage et le rôle de *Collaborateur de compte de stockage classique* pour gérer les comptes de stockage classique. Pour les opérateurs cloud qui doit gérer les machines virtuelles et compte de stockage, vous pouvez les ajouter à collaborateur d’un *ordinateur virtuel* .

Les organisations qui n’appliquent pas de contrôle d’accès de données en tirant parti des fonctionnalités, telles que RBAC peuvent être donnant davantage de privilèges que nécessaire pour leurs utilisateurs. Cela peut affecter les données compromis en demandant à certains utilisateurs ayant accès à des données qui ne doit pas avoir en premier lieu.

Vous pouvez Apprenez-en davantage sur Azure RBAC en lisant l’article [Azure Role-Based le contrôle d’accès](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Chiffrer Machines virtuelles Azure
Pour de nombreuses organisations, [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire vers la confidentialité des données, conformité et souveraineté de données. Chiffrement de disque Azure permet aux administrateurs informatiques souhaitant chiffrer disques Windows et Linux IaaS Machine virtuelle (machine virtuelle). Chiffrement de disque Azure s’appuie sur la fonctionnalité BitLocker standard du secteur de Windows et la fonctionnalité DM Crypt de Linux pour fournir le chiffrement de volume pour le système d’exploitation et les disques de données.

Vous pouvez tirer parti de chiffrement de disque Azure pour protéger et protéger vos données pour répondre aux exigences de conformité et votre sécurité de l’organisation. Organisations doivent également envisager d’utiliser le chiffrement afin de limiter l’accès aux données de contenu lié à non autorisé risques. Il est également recommandé de chiffrer lecteurs avant d’écrire des données sensibles leur.

Veillez à chiffrer des volumes de données de votre ordinateur virtuel et le volume de démarrage pour protéger les données au reste de votre compte de stockage Azure. Sauvegarder les clés de chiffrement et les secrets grâce à [L’archivage sécurisé de clé Azure](../key-vault/key-vault-whatis.md).

Pour vos serveurs de Windows en local, vous pouvez le chiffrement suivant meilleures pratiques :

- Utiliser [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) pour le chiffrement des données
- Stocker les informations de récupération dans AD DS.
- S’il existe toute question que clés BitLocker ont été compromis, nous vous recommandons de mettre en forme le lecteur de supprimer toutes les instances des métadonnées BitLocker à partir du disque ou déchiffrer et de chiffrer la totalité du lecteur à nouveau.

Les organisations qui n’appliquent pas de chiffrement des données sont susceptibles d’être exposés à des problèmes d’intégrité des données, telles que les utilisateurs malveillants ou non autorisés vol de vos données et compromis comptes ayant accès non autorisé aux données au format effacer. Outre ces risques, sociétés qui doivent être conformes aux réglementations, doivent prouver qu’ils sont diligent et utilisent les contrôles de sécurité correcte pour améliorer la sécurité des données.

Vous pouvez plus d’informations sur le chiffrement des disques Azure en lisant l’article [Azure disque chiffrement pour Windows et machines virtuelles IaaS Linux](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utiliser des Modules matériels de sécurité

Solutions de chiffrement secteur utilisent clés secrètes pour chiffrer les données. Par conséquent, il est essentiel que ces touches sont stockées en toute sécurité. Gestion des clés devient partie intégrante de protection des données, dans la mesure où il sera utilisé pour stocker les clés secrètes qui sont utilisées pour chiffrer les données.

Chiffrement de disque Azure utilise [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/) pour vous aider à contrôler et gérer des clés de chiffrement de disque et secrets dans votre abonnement de l’archivage sécurisé clés, tout en garantissant que toutes les données dans les disques machine virtuelle sont chiffrées au reste dans votre espace de stockage Azure. Vous devez utiliser l’archivage sécurisé de clé Azure à un audit clés et l’utilisation de stratégie.

Il existe de nombreux risques liés à des contrôles de sécurité approprié n’ayant ne pas mis en place pour protéger les clés secrètes qui ont été utilisés pour chiffrer vos données. Si les pirates ont accès à la clé secrète, ils pourront déchiffrer les données et potentiellement avoir accès aux informations confidentielles.

Vous pouvez en savoir plus sur les recommandations générales pour la gestion des certificats dans Azure en lisant l’article [Gestion des certificats dans Azure : conseils](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Pour plus d’informations sur l’archivage sécurisé de clé Azure, lisez [prise en main l’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gérer les postes de travail sécurisés

Étant donné que la plupart des attaques cibler l’utilisateur final, le point de terminaison devient l’un des principaux points d’attaque. Si un pirate se le point de terminaison, il peut exploiter les informations d’identification de l’utilisateur pour accéder aux données de l’organisation. La plupart des attaques de point de terminaison sont en mesure de tirer parti du fait que les utilisateurs finaux sont les administrateurs dans leur poste de travail local.

Vous pouvez réduire ces risques en utilisant un poste de travail gestion sécurisée. Nous vous recommandons d’utiliser un [Dotés de privilèges Access postes de travail (patte)](https://technet.microsoft.com/library/mt634654.aspx) afin de réduire la surface d’attaque dans les postes de travail. Ces postes de travail gestion sécurisée peuvent vous aider à limiter certaines de ces attaques garantissent vos données sont plus sûres. Veillez à utiliser patte pour renforcer la sécurité et de verrouiller votre poste de travail. Il s’agit d’une étape à fournir des garanties haute sécurité pour les comptes sensibles, tâches et protection des données importantes.

Absence de protection de point de terminaison peut mettre vos données en danger, assurez-vous d’appliquer des stratégies de sécurité sur tous les périphériques qui sont utilisés pour utiliser des données, quelle que soit l’emplacement des données (cloud ou en local).

Vous pouvez en savoir que plus sur les privilèges accéder poste de travail en lisant l’article [Sécurisation de l’accès privilégié](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Activer le chiffrement de données SQL

[Chiffrement transparent des données de base de données SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) permet de protéger contre la menace d’activité malveillante en effectuant en temps réel chiffrer et déchiffrer la base de données, les sauvegardes associées et les fichiers journaux des transactions inactives sans nécessiter des modifications à l’application.  TDE chiffre le stockage d’une base de données entière en utilisant une clé symétrique appelée la clé de chiffrement de base de données.

Même lorsque le stockage dans son intégralité est chiffré, il est très important chiffrer votre base de données. Il s’agit d’une implémentation de l’approche défense en profondeur pour la protection des données. Si vous utilisez une [Base de données SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) et que vous souhaitez protéger des données sensibles telles que la carte de crédit ou des numéros de sécurité sociale, vous pouvez chiffrer les bases de données avec FIPS 140-2 validées 256 bits le chiffrement AES répondant aux conditions de nombreuses normes industrielles (par exemple, HIPAA, PCI).

Il est important de comprendre que les fichiers liés à [extension de pool de mémoire tampon](https://msdn.microsoft.com/library/dn133176.aspx) (Broadcast) ne sont pas chiffrés lorsqu’une base de données est chiffré à l’aide de TDE. Vous devez utiliser les outils de chiffrement niveau fichier système comme BitLocker ou le [Système de fichiers chiffrant](https://technet.microsoft.com/library/cc700811.aspx) () pour Broadcast les fichiers associés.

Depuis un utilisateur autorisé, comme un administrateur de sécurité ou un administrateur de base de données peut accéder aux données même si la base de données est chiffré avec TDE, vous devez également suivre les recommandations ci-dessous :

- Authentification SQL au niveau de la base de données
- Authentification Azure Active Directory à l’aide des rôles RBAC
- Les utilisateurs et les applications doivent utiliser des comptes distincts s’authentifier. Ainsi, vous pouvez limiter les autorisations accordées aux utilisateurs et aux applications et réduire les risques liés à l’activité malveillante
- Mettre en œuvre la sécurité au niveau de base de données à l’aide des rôles de base de données fixe (comme db_datareader ou db_datawriter), ou vous pouvez créer des rôles personnalisés pour votre application accorder des autorisations explicites aux objets de base de données sélectionnées

Les organisations qui n’utilisent pas le chiffrement au niveau de base de données peuvent être plus sensibles pour les attaques qui pourraient compromettre les données situées dans des bases de données SQL.

Vous pouvez plus d’informations sur le chiffrement SQL TDE en lisant l’article [Chiffrement Transparent des données avec la base de données SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Protéger les données lors des transferts

Protection des données lors des transferts doit être partie essentielle de votre stratégie de protection des données. Dans la mesure où les données pour déplacer et inversement plusieurs endroits, il est recommandé que vous utilisez toujours les protocoles SSL/TLS pour échanger des données à différents endroits. Dans certains cas, vous souhaiterez isoler le canal de communication entière entre votre locaux et cloud infrastructure à l’aide d’un réseau privé virtuel (VPN).

Pour les données déplacement entre votre infrastructure en local et Azure, vous devez prendre en compte garanties appropriées, telles que VPN ou HTTPS.

Pour les organisations qui ont besoin de sécuriser l’accès à partir de plusieurs postes de travail trouve local vers Azure, utilisez [Azure VPN de site à](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Pour les entreprises qui ont besoin pour sécuriser l’accès à partir d’un poste de travail situé en local sur Azure, utilisez [Point-à-Site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

De liaison de données plus grandes ensembles peuvent être déplacés sur un réseau étendu haut débit dédié tel que [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si vous choisissez d’utiliser ExpressRoute, vous pouvez également chiffrer les données au niveau de l’application à l’aide de [SSL/TLS](https://support.microsoft.com/kb/257591) ou autres protocoles pour une protection accrue.

Si vous interagissez avec le stockage Azure via le portail d’Azure, toutes les transactions se produisent via HTTPS. [API REST de stockage](https://msdn.microsoft.com/library/azure/dd179355.aspx) sur HTTPS peut également être utilisée pour interagir avec [Le stockage Azure](https://azure.microsoft.com/services/storage/) et [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/).

Les organisations qui ne parviennent pas à protéger le transfert de données sont plus sensibles pour [les attaques dans milieu](https://technet.microsoft.com/library/gg195821.aspx), [écoute](https://technet.microsoft.com/library/gg195641.aspx) et piratage de session. Ces attaques peuvent être la première étape pour accéder à des données confidentielles.

Vous pouvez Apprenez-en davantage sur option Azure VPN en lisant l’article [planification et conception pour la passerelle VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Appliquer le chiffrement de données au niveau des fichiers

Une autre couche de protection qui peut d’améliorer le niveau de sécurité pour vos données est chiffrer le fichier, quelle que soit l’emplacement du fichier.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utilise les stratégies de chiffrement, d’identité et d’autorisation pour sécuriser vos fichiers et la messagerie électronique. Azure RMS fonctionne sur plusieurs appareils — téléphones et tablettes PC en protégeant au sein de votre organisation et en dehors de votre organisation. Cette fonctionnalité est possible, car Azure RMS ajoute un niveau de protection reste avec les données, même si elle laisse des limites de votre organisation.

Lorsque vous utilisez Azure RMS pour protéger vos fichiers, vous utilisez le chiffrement standard avec prise en charge complète de [FIPS140 - 2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Lorsque vous tirer parti Azure RMS pour la protection des données, vous avez la garantie que la protection reste avec le fichier, même si elle est copiée vers le stockage qui n’est pas sous le contrôle du service informatique, par exemple un service de stockage cloud. La même se produit pour les fichiers partagés par courrier électronique, le fichier est protégé en tant que pièce jointe à un message électronique, des instructions comment ouvrir la pièce jointe protégée.

Lorsque vous planifiez adoption Azure RMS nous recommandons les éléments suivants :

- Installez le [RMS partage d’application](https://technet.microsoft.com/library/dn339006.aspx). Cette application s’intègre à Office applications en installant un Office complément afin que les utilisateurs peuvent facilement protéger des fichiers directement.
- Configurer des applications et services pour prendre en charge Azure RMS
- Créer des [modèles personnalisés](https://technet.microsoft.com/library/dn642472.aspx) qui reflètent vos besoins. Par exemple : un modèle de données secrètes supérieure qui doivent être appliquées à toutes les très secret liés des messages électroniques.

Organisations qui sont faibles sur la protection des fichiers et de [classification de données](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) peuvent être plus susceptibles d’être fuite de données. Sans protection des fichiers NOMPROPRE, les organisations ne pourrez obtenir des informations d’entreprise, surveiller de manière inappropriée et empêcher tout accès malveillant aux fichiers.

Vous pouvez Apprenez-en davantage sur Azure RMS en lisant l’article de la [Mise en route d’Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

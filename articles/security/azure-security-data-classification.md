<properties
   pageTitle="Classification des données pour Azure | Microsoft Azure"
   description="Cet article fournit une introduction les notions de base de classification de données et met en surbrillance sa valeur, en particulier dans le contexte de cloud computing et à l’aide de Microsoft Azure"
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
   ms.author="yurid"/>

# <a name="data-classification-for-azure"></a>Classification des données pour Azure

Cet article fournit une introduction les notions de base de classification de données et met en surbrillance sa valeur, en particulier dans le contexte de cloud computing et à l’aide de Microsoft Azure. 

## <a name="data-classification-fundamentals"></a>Principes de base de données classification

Classification de données dans une organisation requiert large connaissance des besoins de votre organisation et bien comprendre où se trouvent vos données de l’entreprise.  
 
Les données utilisées dans un des trois états de base : 

- Inactives 
- Processus 
- Lors des transferts 
 
Les trois états nécessitent des solutions techniques uniques pour la classification des données, mais les principes appliqués de classification de données doivent être identique pour chacun. Données qui sont considérées comme confidentiel doivent rester confidentielles au repos, processus et lors des transferts. 
 
Données peuvent également être structurées ou non structurées. Processus de classement par défaut pour les données structurées figurant dans les bases de données et des feuilles de calcul sont moins complexes et long à gérer que celles des données non structurées telles que des documents, code source et à la messagerie. 

> [AZURE.TIP] Pour plus d’informations sur les fonctionnalités Azure et des pratiques recommandées pour les données de chiffrement lisez [Recommandations chiffrement de données Azure](azure-security-data-encryption-best-practices.md)

En règle générale, les organisations ont plus des données non structurées à des données structurées. Quelle que soit l’indique si les données sont structurées ou non, il est important pour vous permet de gérer le critère de diffusion de données. Mise en œuvre appropriée, la classification des données vous permet de garantir que les données sensibles ou confidentielles actifs sont gérés contrôlable supérieure que les éléments de données qui sont considérés comme public ou gratuit distribuer. 

### <a name="controlling-access-to-data"></a>Contrôler l’accès aux données 

Authentification et autorisation sont souvent confondus avec eux et leurs rôles mal comprise. En réalité elles sont assez différentes, comme indiqué dans l’illustration suivante.  

![Accès aux données et contrôle](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### <a name="authentication"></a>Authentification 

Authentification comprend généralement au moins deux parties : un ID utilisateur ou le nom d’utilisateur pour identifier un utilisateur et un jeton, par exemple un mot de passe pour vous assurer que les informations d’identification de nom d’utilisateur sont valide. Le processus ne fournit pas de l’utilisateur authentifié ayant accès à des éléments ou les services ; Il vérifie que l’utilisateur est qui ils disent qu’ils sont.   

> [AZURE.TIP] [Azure Active Directory](../active-directory/active-directory-whatis.md) fournit des services d’identité sur le nuage qui vous permettent d’authentifier et autoriser les utilisateurs. 

### <a name="authorization"></a>Autorisation
 
L’autorisation consiste à fournir un utilisateur authentifié la fonctionnalité permettant d’accéder à une application, jeu de données, fichier de données ou un autre objet. Affectation d’utilisateurs authentifiés les droits d’utilisation, modifier ou supprimer des éléments qu’ils peuvent accéder nécessite l’attention sur la classification des données. 

Autorisation réussie nécessite l’implémentation d’un mécanisme pour valider les besoins des utilisateurs individuels pour accéder aux fichiers et les informations basé sur une combinaison de rôle, la stratégie de sécurité et stratégie risque considérations. Par exemple, données de spécifique métier des applications métier n’ayez pas à accéder à tous les employés et uniquement un sous-ensemble des employés devront probablement accéder aux fichiers de ressources humaines (RH). Mais pour les organisations pour contrôler qui peut accéder aux données, ainsi que quand et comment, un système efficace d’authentification des utilisateurs doit être en place. 

> [AZURE.TIP] dans Microsoft Azure, veillez à tirer parti de contrôle d’accès Azure Role-Based (RBAC) pour accorder uniquement la quantité d’accès que les utilisateurs ont besoin pour effectuer leur travail. Pour plus d’informations, consultez [utiliser les affectations de rôle pour gérer l’accès à vos ressources Azure Active Directory](../active-directory/role-based-access-control-configure.md) . 

### <a name="roles-and-responsibilities-in-cloud-computing"></a>Rôles et responsabilités dans le cloud computing 

Bien que les fournisseurs de cloud peuvent vous aider à gérer les risques, clients doivent s’assurer que gestion de classification de données et sont implémentées correctement pour fournir le niveau de services de gestion des données.  
 
Responsabilités de classification de données varient selon le modèle de service cloud est en place, comme indiqué dans l’illustration suivante. Les trois modèles de service cloud primaire sont infrastructure sous forme d’un service (IaaS), plateforme en tant que service (PaaS) et les logiciels en tant que service (SaaS). Implémentation de mécanismes de classification de données varient également selon les vis-à-vis et aux attentes du fournisseur cloud. 

![Rôles](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Bien que vous êtes responsable de classification de vos données, fournisseurs cloud doivent rendre engagements écrites sur comment elles secure et mettre à jour de la confidentialité des données client stockées dans leur cloud.  

- Configuration requise pour **les fournisseurs IaaS** sont limités à faire en sorte que l’environnement virtuel peut prendre en charge des capacités de classification de données et aux exigences de conformité client. Fournisseurs IaaS ont un rôle plus petit classification de données, car ils doivent uniquement pour vous assurer que les données client résout aux exigences de conformité. Toutefois, fournisseurs doivent tout en garantissant que leurs environnements virtuels répondre aux exigences de classification de données en plus de la sécurisation de leurs centres de données.
- **Fournisseurs de services PaaS** responsabilités peuvent être combinées, étant donné que la plateforme peut être utilisée dans une approche en couches pour fournir sécurité pour un outil de classification. Fournisseurs de services PaaS peuvent être responsables de l’authentification et éventuellement certaines règles d’autorisation et doivent fournir des fonctionnalités de classification de données couche de l’application et sécurité. Beaucoup comme des fournisseurs IaaS, PaaS fournisseurs doivent s’assurer que leur plate-forme est conforme à des exigences de classification de données pertinentes.
- **Fournisseurs SaaS** sont souvent considérés comme cadre d’une chaîne d’autorisation et est besoin pour vous assurer que les données stockées dans cette solution SaaS application peut être contrôlée par type de classification. Applications SaaS peuvent être utilisées pour les applications métier et par leur besoin de nature même à vous permettent d’authentifier et autoriser les données utilisées et stockées. 

## <a name="classification-process"></a>Processus de classification 

De nombreuses organisations qui comprennent la nécessité de classification de données et want mettre en œuvre il rencontrent une authentification de base : où commencer ?

Un moyen simple et efficace pour mettre en œuvre la classification des données consiste à utiliser la vérification de l’offre, DO, agir modèle [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). L’illustration suivante représente sous forme graphique les tâches qui sont requises pour implémenter la classification des données dans ce modèle.  

1. **Planifier**. Identifier les ressources de données, un dépositaire de données à déployer le programme de classification et développer des profils de protection. 
2. **DO**. Une fois que les règles de classification de données sont accordés, déployez le programme et mise en œuvre des technologies d’application nécessaire pour les données confidentielles.  
3. **Vérifier**. Vérifier et valider des rapports pour vous assurer que les outils et les méthodes en cours d’utilisation sont adressage efficacement les stratégies de classification. 
4. **ACT**. Vérifier l’état de l’accès aux données et les fichiers de révision et les données qui nécessitent une révision à l’aide d’une méthodologie reclassement et révision pour accepter les changements et faire face aux nouveaux risques.  

![Planifier, suivre, vérifier, agir](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###<a name="select-a-terminology-model-that-addresses-your-needs"></a>Sélectionner un modèle de terminologie qui répond à vos besoins
 
Il existe plusieurs types de processus de classification de données, y compris les processus manuels, processus basée sur l’emplacement de classent les données selon un utilisateur ou du système de l’emplacement, processus basée sur l’application, comme le classement de base de données spécifique et automatisation des processus utilisés par les technologies différentes, certaines d'entre elles sont décrites dans la section « Protection des données confidentielles » plus loin dans cet article.  
 
Cet article présente deux modèles terminologie GRG basés sur des modèles bien utilisés et respectées du secteur. Ces modèles terminologie, qui proposent tous deux trois niveaux de sensibilité classification, apparaissent dans le tableau suivant.  

> [AZURE.NOTE] lors de la classification un fichier ou une ressource qui combine les données sont généralement classées en différents niveaux, le plus haut niveau de classification présenter devrait établir la classification globale. Par exemple, un fichier contenant des données sensibles et restreintes doit être classé comme restreint.  

| **Critère de diffusion**   | **Modèle de terminologie 1**   | **Modèle de terminologie 2** |
|--------------------|---------------------------|-------------------------|
| Élevé               | Confidentielles              | Limité              |
| Moyenne             | Pour un usage interne uniquement     | Sensibles               |
| Min                | Public                    | Non restreint            |

#### <a name="confidential-restricted"></a>Confidentiel (limité) 

Les informations sont considérées comme confidentiel ou restreint incluent des données qui peuvent être grave à une ou plusieurs personnes et/ou organisations si compromis ou perdue. Ces informations sont souvent fournies sur une base « vous avez besoin de savoir » et peuvent inclure : 

- Données personnelles, y compris les informations d’identification personnelle tels que de sécurité sociale ou numéros d’identification nationales nombres passport, numéros de carte de crédit, du pilote numéros de licence, médical et numéros d’identification stratégie assurance.  
- Documents financiers, y compris les numéros de compte financier telles que la vérification ou investissement. 
- Matériel d’entreprise, par exemple documents ou les données de propriété intellectuelle unique ou spécifique.  
- Données juridiques, y compris les éléments potentiels conseiller privilégié. 
- Données d’authentification, y compris les clés de chiffrement privé, paires de mot de passe nom d’utilisateur ou autres séquences identification tels que des fichiers de clés privées biométriques. 

Données qui sont considérées comme confidentielles fréquemment ont réglementation et conformité en matière de gestion des données. 

#### <a name="for-internal-use-only-sensitive"></a>Pour un usage interne uniquement (dépendantes)
 
Informations qui sont considérées comme étant de sensibilité moyenne incluent les fichiers et les données qui n’aurait pas une incidence grave sur une organisation ou individuelles perte ou sa destruction. Ces informations peuvent inclure : 

- Courrier électronique, la plupart d'entre eux peut être supprimée ou distribuée sans à l’origine d’une crise (à exclure de boîtes aux lettres ou un message électronique aux personnes qui sont identifiés dans la classification confidentielle).  
- Documents et fichiers qui ne contiennent pas de données confidentielles.
 
En règle générale, cette classification inclut tous les éléments qui ne sont pas confidentielles. Cette classification peut inclure la plupart des données métiers, étant donné que la plupart des fichiers qui sont gérées ou utilisés au quotidien peuvent être classés comme sensible. À l’exception de données sont rendues public ou confidentielles, toutes les données au sein d’une organisation de l’entreprise peuvent être classées comme sensibles par défaut. 

#### <a name="public-unrestricted"></a>Public (aucune restriction)
 
Informations qui sont considérées comme publics incluent des données et fichiers qui ne sont pas critiques aux besoins professionnels ou aux opérations. Cette classification peut également inclure des données a été délibérément rendues accessibles au public pour leur usage, tels que des documents marketing ou appuyez sur annonces. Par ailleurs, cette classification peut inclure des données telles que des messages de spam stockés par un service de messagerie. 

### <a name="define-data-ownership"></a>Définir la propriété des données
 
Il est important de disposer d’une chaîne effacer garde de la propriété pour tous les éléments de données. Le tableau suivant identifie les rôles de la propriété des données différentes dans les efforts de classification de données et leurs droits respectifs.  

| **Rôle**        | **Créer**    | **Modifier/supprimer**   | **Délégué**  | **Lecture**    | **Archivage/restauration**   |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Propriétaire           | X             | X                   | X             | X           | X                     |
| Dépositaire       |               |                     | X             |             |                       |
| Administrateur   |               |                     |               |             | X                     |
| Utilisateur\*          |               | X                   |               | X           |                       |
**Les utilisateurs peuvent disposer de droits supplémentaires, telles que modifier et supprimer par un dépositaire* 

> [AZURE.NOTE] Ce tableau ne fournit pas une liste exhaustive des rôles et droits, mais simplement un échantillon représentatif. 

Le **propriétaire de la ressource** est le créateur d’origine des données, qui peuvent déléguer la propriété et affecter un dépositaire. Lorsqu’un fichier est créé, le propriétaire doit être en mesure d’affectation d’une classification, ce qui signifie qu’ils ont une responsabilité de comprendre ce qui doit être considéré comme confidentiel en fonction des stratégies de leur organisation. Toutes les données d’un propriétaire biens de données peuvent être classés automatique que pour un usage interne uniquement (la casse) sauf s’ils sont responsables de propriétaire ou de création de types de données (limité) confidentielles. Rôle du propriétaire remplacera fréquemment, une fois que les données sont considérées. Par exemple, le propriétaire peut créer une base de données de classification des informations et renoncer à leurs droits pour le dépositaire de données.  

> [AZURE.NOTE] les propriétaires de biens données utilisent souvent un mélange de services, des périphériques et des médias, certaines d'entre elles sont personnels et certaines d'entre elles appartiennent à l’organisation. Une stratégie d’organisation effacer permettent de vous assurer que l’utilisation des appareils tels que les ordinateurs portables et périphériques Smart Device est conformément aux directives de classification de données.  

Le **dépositaire de capital de données** est affectée par le propriétaire de la ressource (ou leur délégué) pour gérer les biens en fonction des accords avec le propriétaire de la ressource ou conformément aux exigences de la stratégie applicable. Dans l’idéal, le rôle de conservateur peut être implémenté dans un système automatisé. Un dépositaire de capital garantit que les contrôles d’accès nécessaires sont fournies et est responsables de la gestion et la protection des biens déléguées à leurs soins. Impossible de responsabilités du dépositaire actif :  

- Protection du bien conformément à la direction du propriétaire de la ressource ou en accord avec le propriétaire de la ressource 
- Assurer le respect des règles de classification 
- Pour informer les propriétaires de biens de toute modification apportée aux contrôles convenu et/ou procédures de protection avant ces modifications prise d’effet 
- Création de rapports au propriétaire de la ressource sur les modifications apportées aux ou la suppression des responsabilités de dépositaire de capital 
- Un **administrateur** représente un utilisateur qui est chargé de s’assurer que l’intégrité référentielle est conservée, mais elles ne sont pas un propriétaire de la ressource, dépositaire ou utilisateur. En fait, plusieurs rôles d’administrateur fournissent des services de gestion des données conteneur sans avoir accès aux données. Le rôle d’administrateur inclut les biens de sauvegarde et restauration des données, gestion des enregistrements des actifs et en choisissant et acquisition d’exploitation les périphériques et le stockage de cette maison. 
- L’utilisateur actif inclut toutes les personnes qui a accès aux données ou un fichier. Affectation de Access est souvent déléguée par le propriétaire pour le dépositaire actif.  

### <a name="implementation"></a>Mise en œuvre
  
Considérations sur la gestion s’appliquent à toutes les méthodologies classification. Les considérations suivantes vous avez besoin inclure les détails concernant les personnes, que, où, quand et pourquoi un élément de données serait être utilisé, accessible, modifié ou supprimé. Gestion des biens tous les doivent être effectués avec comprendre comment une organisation affichages ses risques, mais une méthodologie simple peut être appliquée, telle que définie dans le processus de classification de données. Recommandations supplémentaires pour la classification des données incluent l’introduction de nouvelles applications et les outils et la gestion des modifications après l’implémentation d’une méthode de classification.  

### <a name="reclassification"></a>Reclassement
 
Reclassement ou modifiez l’état de classement d’un bien de données doit être effectuée lorsqu’un utilisateur ou un système détermine que profil importance ou risque de l’élément de données a changé. Cet effort est important de s’assurer que l’état de classification continue à être actuel et valide. La plupart des contenus qui n’est pas classée manuellement pouvez classés automatiquement ou en fonction de l’utilisation par un dépositaire de données ou un propriétaire de données. 

### <a name="manual-data-reclassification"></a>Reclassement manuelle des données
 
Dans l’idéal, cet effort grâce au que les détails d’une modification sont capturés et audit. La raison probablement de reclassement manuel serait pour des raisons de critère de diffusion, ou pour les enregistrements dans le format de papier, ou une spécification pour consulter les données incorrectement classées à l’origine. Étant donné que ce document estime la classification des données et transfert de données dans le cloud, efforts reclassement manuel de verser l’attention sur une base cas par cas et une analyse des risques serait idéale pour répondre aux exigences de classification. En règle générale, un tel effort considère la stratégie de l’organisation sur ce qui doit être classées, l’état de classification par défaut (toutes les données et des fichiers sensibles mais non confidentielles) et prendre des exceptions pour les données de risque. 

### <a name="automatic-data-reclassification"></a>Reclassement automatique des données
 
Reclassement automatique des données utilise la même règle générale comme classification manuelle. L’exception est que des solutions automatisées peuvent de garantir que les règles sont suivies et appliqués selon vos besoins. Classification des données peut être effectuée dans le cadre d’une stratégie de l’application de classification de données, qui peut être appliquée lorsque les données sont stockées, en cours d’utilisation et à l’aide de la technologie d’autorisation lors des transferts.

- Basé sur l’application. L’utilisation de certaines applications par défaut définit un niveau de classification. Par exemple, les données à partir de logiciel de gestion de la relation client, RH et outils de gestion des enregistrements d’intégrité sont confidentielles par défaut. 
- Basé sur l’emplacement. Emplacement de données peut aider à identifier critère de diffusion de données. Par exemple, données qui sont stockées par un HR ou le service Finances sont censées être de caractère confidentiel.  
 
### <a name="data-retention-recovery-and-disposal"></a>Disposition et restauration conservation des données 

Récupération des données et disposition, tels que des données reclassement, est un aspect essentiel de la gestion des données de l’entreprise. Les principes de récupération des données et disposition seraient définies par une stratégie de rétention des données et mises en œuvre de la même manière que reclassement de données ; un tel effort doit être effectué par les rôles dépositaire et administrateur comme une tâche collaborative.  

Perte de données ou l’échec conformes aux exigences réglementaires et juridiques découverte une panne aient une stratégie de rétention des données peut entraîner. La plupart des organisations qui ne disposent pas d’une stratégie de rétention des données clairement définis ont tendance à utiliser une stratégie de rétention « conserver tout » par défaut. Toutefois, une telle stratégie de rétention a des risques supplémentaires dans les scénarios de services de cloud. 

Par exemple, une stratégie de rétention des données pour les fournisseurs de services de cloud peut être considérée comme « la durée de l’abonnement » (à condition que le service est payé pour les données sont conservées). Tel un accord paie pour rétention ne résout pas les stratégies de rétention d’entreprise ou réglementaires. Définition d’une stratégie pour des données confidentielles permet de garantir que les données sont stockées et supprimées en fonction de meilleures pratiques. En outre, une stratégie d’archivage pouvant être créée pour respecter de comprendre les données doivent être supprimées et à quel moment. 

Stratégie de rétention des données doit être appliqué à la réglementation et aux exigences de conformité, ainsi que des exigences de rétention juridique de l’entreprise. Données classées peuvent provoquer des questions sur la durée de conservation et des exceptions pour les données qui ont été stockées avec un fournisseur ; ces questions sont généralement pour les données qui n’ont pas été classées correctement. 

> [AZURE.TIP] Pour plus d’informations sur les stratégies de rétention des données Azure et plus, lisez le [Contrat d’abonnement Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/)

## <a name="protecting-confidential-data"></a>Protection des données confidentielles
  
Une fois que les données sont considérées, recherche et mise en œuvre de protection des données confidentielles devient partie intégrante de toute stratégie de déploiement de la protection de données. Protection des données confidentielles nécessite une attention supplémentaire à comment les données sont stockées et transmises dans conventionnelles architectures ainsi que dans le cloud. 

Cette section fournit des informations de base sur certaines technologies permettant d’automatiser les efforts de l’application de protection des données qui ont été classées comme confidentielles. 
 
Comme la figure suivante illustre, ces technologies peuvent être déployés en tant que locaux ou solutions basées sur le nuage, ou de manière hybride, avec certaines leur déployé en local et d’autres dans le cloud. (Quelques technologies d’assistance, comme le chiffrement et la gestion des droits, étendent également aux périphériques utilisateur).  

![Technologies d’assistance](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### <a name="rights-management-software"></a>Logiciel de gestion des droits  

Une solution pour la protection contre la perte de données est un logiciel de gestion des droits relatifs à le. Contrairement aux approches qui tentent d’interrompre le flux des informations en provenance d’une organisation, logiciel de gestion des droits relatifs à le fonctionne en profondes niveaux au sein de technologies de stockage de données. Documents sont chiffrés et contrôler les déchiffre utilise des contrôles d’accès qui sont définis dans une solution de contrôle d’authentification comme un service d’annuaire.  

> [AZURE.TIP] Vous pouvez utiliser Azure Rights Management (RMS Azure) en tant que la solution de protection des informations pour protéger vos données dans différents scénarios. Lecture [Nouveautés Azure Rights Management ?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) pour plus d’informations sur cette solution Azure.

Voici quelques-uns des avantages du logiciel de gestion des droits relatifs à le : 

- Informations sensibles sauvegardées. Les utilisateurs peuvent protéger leurs données directement à l’aide d’applications compatibles avec gestion des droits. Aucune étape supplémentaire est requise : co-création de documents, envoi de messages électroniques et publication des données offrent une expérience de protection des données cohérentes. 
- Protection se déplace avec les données. Clients restent dans le contrôle des personnes qui ont accès à leurs données, que ce soit dans le cloud, l’infrastructure informatique existant, ou au bureau de l’utilisateur. Organisations peuvent choisir de chiffrer leurs données et de limiter l’accès en fonction de leurs besoins. 
- Stratégies de protection des informations par défaut. Administrateurs et utilisateurs peuvent utiliser des stratégies standards pour de nombreux scénarios professionnels courants, tels que « Société confidentiel – en lecture seule » et « Ne pas transférer ». Un ensemble complet de l’utilisation des droits sont prises en charge telles que la lecture, copier, imprimer, enregistrer, modifier et transmettre pour offrir plus de souplesse dans la définition de droits d’utilisation personnalisés. 

> [AZURE.TIP] Vous pouvez protéger des données dans le stockage Azure à l’aide de [Chiffrement de Service de stockage Azure](../storage/storage-service-encryption.md) des données au repos. Vous pouvez également utiliser [Le chiffrement des disques Azure](azure-security-disk-encryption.md) pour protéger les données contenues sur disques virtuels utilisés pour le Machines virtuelles Azure.

### <a name="encryption-gateways"></a>Passerelles de chiffrement

Les passerelles de chiffrement fonctionnent dans leurs propres calques pour fournir des services de chiffrement en redirigeant tout accès aux données sur le nuage. Cette approche ne doit pas être confondue avec celle d’un réseau privé virtuel (VPN). Passerelles de chiffrement sont conçus pour fournir une couche transparente aux solutions basées sur le cloud.   

Les passerelles de chiffrement peuvent fournir un moyen de gérer et sécuriser les données qui ont été classées comme confidentiel en chiffrant les données lors des transferts, ainsi que les données au repos.  
 
Les passerelles de chiffrement sont placées dans le flux de données entre les périphériques de l’utilisateur et les données d’application centres pour fournir des services de chiffrement/déchiffrement. Ces solutions, telles que VPN, sont principalement des solutions en local. Ils sont conçus pour fournir un tiers avec un contrôle sur les clés de chiffrement, qui vous aide à réduire les risques de placer les données et la clé de gestion avec un seul fournisseur. Ces solutions sont conçues, comme le chiffrement, pour travailler de façon transparente et en toute transparence entre les utilisateurs et le service. 

> [AZURE.TIP] Vous pouvez utiliser Azure ExpressRoute pour étendre vos réseaux locaux dans le cloud Microsoft via une connexion privée dédiée. Lisez [présentation technique ExpressRoute](../expressroute/expressroute-introduction.md) pour plus d’informations sur cette fonctionnalité. Une autre option pour croisée connectivité locaux entre votre réseau local et [Azure est un réseau privé virtuel de site à](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### <a name="data-loss-prevention"></a>Prévention des pertes de données 
Perte de données (parfois appelée fuite de données) est un facteur important, et la prévention des pertes de données externes via initiés malveillants et accidentels est essentielle pour de nombreuses organisations.  
 
Technologies de prévention des pertes de données permettent de vous assurer que les solutions tels que des services de messagerie ne pas transmettent les données qui ont été classées comme confidentielles. Organisations peuvent tirer parti des fonctionnalités DLP dans les produits existantes afin d’éviter les pertes de données. Ces fonctionnalités utilisent les stratégies qui peuvent être facilement créés à partir de zéro ou en utilisant un modèle fourni par le fournisseur de logiciel.  
 
Technologies DLP peuvent effectuer analyse approfondie du contenu via correspondances de mot clé, dictionnaire des correspondances, évaluation des expressions régulières et autre examen de contenu pour détecter le contenu qui enfreint les stratégies DLP d’organisation. Par exemple, DLP permet d’éviter la perte de types de données suivants : 

- Numéros d’identification nationales et de sécurité sociale 
- Informations bancaires 
- Numéros de carte de crédit  
- Adresses IP 

Certaines technologies DLP offrent également la possibilité de remplacement de la configuration de DLP (par exemple, si une organisation a besoin transmettre les informations de sécurité sociale à un processeur paie). Par ailleurs, il est possible de configurer DLP afin que les utilisateurs soient avertis avant de même tenter d’envoyer des informations sensibles qui ne doivent pas être transmises. 

> [AZURE.TIP] Vous pouvez utiliser les fonctionnalités d’Office 365 DLP pour protéger vos documents. Lecture [contrôles de conformité d’Office 365 : prévention des pertes](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) pour plus d’informations.

## <a name="see-also"></a>Voir aussi

- [Meilleures pratiques de chiffrement des données Azure](azure-security-data-encryption-best-practices.md)
- [Azure Gestion des identités et contrôle d’accès meilleures pratiques de sécurité](azure-security-identity-management-best-practices.md)
- [Blog de l’équipe sécurité Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Centre de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx)


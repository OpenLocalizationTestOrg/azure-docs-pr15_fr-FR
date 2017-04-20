<properties
   pageTitle="Azure AD Connect synchronisation : présentation de l’architecture | Microsoft Azure"
   description="Cette rubrique décrit l’architecture de synchronisation Azure AD Connect et explique les termes utilisés."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect synchronisation : présentation de l’architecture
Cette rubrique couvre l’architecture de base pour la synchronisation Azure AD Connect. Dans de nombreux aspects, il est similaire à ses prédécesseurs MIIS 2003, 2007 ILM et FIM 2010. Azure AD Connect synchronisation est l’évolution de ces technologies. Si vous êtes habitué avec n’importe lequel de ces technologies antérieures, le contenu de cette rubrique seront également familière. Si vous débutez avec la synchronisation, cette rubrique concerne les vous. Il n’est toutefois pas besoin de connaître les détails de cette rubrique pour réussir en effectuant des personnalisations de synchronisation Azure AD Connect (appelé moteur de synchronisation dans cette rubrique).

## <a name="architecture"></a>Architecture
Le moteur de synchronisation crée une vue intégrée d’objets qui sont stockées dans plusieurs sources de données connectées et gère les informations d’identité dans les sources de données. Cet affichage intégré est déterminé par les informations d’identité récupérées à partir de sources de données connectées et un ensemble de règles qui déterminent la façon de traiter ces informations.

### <a name="connected-data-sources-and-connectors"></a>Sources de données connectées et des connecteurs
Le moteur de synchronisation traite les informations d’identité autre référentiels de données, par exemple Active Directory ou une base de données SQL Server. Chaque référentiel de données qui organise ses données dans un format de base de données et qui fournit des méthodes d’accès aux données standard est un candidat de source de données potentiel pour le moteur de synchronisation. Les référentiels de données qui sont synchronisés par le moteur de synchronisation sont appelées des **sources de données connectées** ou **annuaires connectés** (CD).

Le moteur de synchronisation encapsule interaction avec une source de données connectée au sein d’un module appelé **connecteur**. Chaque type de source de données connectée a un connecteur spécifique. Le connecteur se traduit par une opération requise dans le format de la source de données connectée prend en charge.

Connecteurs passer des appels d’API pour échanger des informations d’identité (lire et écrire) avec une source de données connectées. Il est également possible d’ajouter un lien personnalisé à l’aide de l’infrastructure de connectivité extensible. L’illustration suivante montre comment un connecteur connecte une source de données connectée au moteur de synchronisation.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Données peuvent circuler dans les deux sens, mais il ne peut pas circuler dans les deux sens simultanément. En d’autres termes, un connecteur peut être configuré pour autoriser à partir de la source de données connectée au moteur de synchronisation ou de moteur de synchronisation pour la source de données connectée de flux de données, mais uniquement une de ces opérations peut se produire à un moment donné pour un objet et attribut. La direction peut être différente pour différents objets et des attributs différents.

Pour configurer un connecteur, vous spécifiez les types d’objets que vous voulez synchroniser. Spécifier les types d’objet définit la portée d’objets qui sont inclus dans le processus de synchronisation. L’étape suivante consiste à sélectionner les attributs à synchroniser, qui est appelé une liste d’inclusion attributs. Ces paramètres peuvent être modifiés à tout moment en réponse aux modifications aux règles de votre entreprise. Lorsque vous utilisez l’Assistant installation Azure AD Connect, ces paramètres sont configurés pour vous.

Pour exporter des objets à une source de données connectées, la liste d’inclusion attribut doit inclure au moins les attributs minimum requis pour créer un type d’objet spécifique dans une source de données connectées. Par exemple, l’attribut **sAMAccountName** doit être inclus dans la liste d’inclusion attribut exporter un objet utilisateur dans Active Directory parce que tous les objets utilisateur dans Active Directory doivent avoir un attribut **sAMAccountName** défini. Là encore, l’Assistant installation effectue cette configuration pour vous.

Si la source de données connectée utilise des composants structurels, tels que des partitions ou conteneurs pour organiser les objets, vous pouvez limiter les zones dans la source de données connectée qui sont utilisés pour une solution donnée.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Structure interne de l’espace de noms de moteur de synchronisation
L’espace de noms de moteur de synchronisation entière se compose de deux espaces de noms qui stockent les informations d’identité. Deux espaces de noms sont les suivantes :

- L’espace de connecteur (SC)
- Le métaverse

L' **espace de connecteur** est une zone de mise en attente qui contient les représentations des objets désignés à partir d’une source de données connectées et les attributs spécifiés dans la liste d’inclusion attribut. Le moteur de synchronisation utilise l’espace de connecteur pour déterminer ce qui a changé dans la source de données connectées et à l’étape modifications entrantes. Le moteur de synchronisation utilise également l’espace de connecteur à l’étape modifications sortantes pour l’exportation à la source de données connectées. Le moteur de synchronisation conserve un espace de connecteur distinct comme zone de travail pour chaque connecteur.

En utilisant une zone de mise en attente, le moteur de synchronisation reste indépendant des sources de données connectées et n’est pas affecté par leur disponibilité et l’accessibilité. Par conséquent, vous pouvez traiter les informations d’identité à tout moment en utilisant les données dans la zone de travail. Le moteur de synchronisation peut demander uniquement les modifications apportées à l’intérieur de la source de données connectée depuis la dernière session de communication terminée ou push uniquement les modifications aux informations d’identité la source de données connectée n’a pas encore reçu, qui permet de réduire le trafic réseau entre le moteur de synchronisation et la source de données connectées.

En outre, moteur de synchronisation stocke les informations d’état sur tous les objets qu’il étapes en plus de l’espace connecteur. Lors de la réception de nouvelles données, moteur de synchronisation évalue toujours si les données ont déjà été synchronisées.

Le **méta-verse** est une zone de stockage contenant les informations d’identité agrégées de plusieurs sources de données connectées, fournir une vue globale, intégrée unique de tous les objets combinés. Objets métaverse sont créés avec les informations d’identité récupérées à partir de sources de données connectées et un ensemble de règles qui vous permettent de personnaliser le processus de synchronisation.

L’illustration suivante montre l’espace de noms espace connecteur et l’espace de noms métaverse dans le moteur de synchronisation.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objets d’identité de moteur de synchronisation
Les objets dans le moteur de synchronisation sont des représentations de soit des objets dans la source de données connectée ou de la vue intégrée qui se synchroniser moteur de ces objets. Chaque objet moteur de synchronisation doit avoir un identificateur globalement unique (GUID). GUID fournissent l’intégrité des données et express relations entre les objets.

### <a name="connector-space-objects"></a>Objets espace de connecteur
Lorsque le moteur de synchronisation communique avec une source de données connectée, il lit les informations d’identité de la source de données connectées et utilise ces informations pour créer une représentation de l’objet d’identité de l’espace de connecteur. Vous ne pouvez pas créer ou supprimer ces objets individuellement. Toutefois, vous pouvez supprimer manuellement tous les objets dans un espace de connecteur.

Tous les objets dans l’espace de connecteur ont deux attributs suivants :

- Un identificateur unique global (GUID)
- Un nom unique (également connu sous le nom de domaine)

Si la source de données connectée affecte un attribut unique à l’objet, les objets dans l’espace de connecteur peuvent également comporter un attribut d’ancrage. L’attribut d’ancrage identifie de façon unique un objet dans la source de données connectées. Le moteur de synchronisation utilise le point d’ancrage pour rechercher la représentation correspondante de cet objet dans la source de données connectées. Moteur de synchronisation suppose que le point d’ancrage d’un objet change jamais pendant la durée de vie de l’objet.

La plupart des connecteurs utilisent un identificateur unique connu pour générer un point d’ancrage automatiquement pour chaque objet lors de l’importation. Par exemple, le connecteur Active Directory utilise l’attribut **GUID d’objet** pour un point d’ancrage. Pour les sources de données connectées qui ne fournissent pas un identificateur unique clairement défini, vous pouvez spécifier génération de point d’ancrage dans le cadre de la configuration du connecteur.

Dans la mesure où les cas, le point d’ancrage créé à partir d’un ou plusieurs attributs uniques d’un objet, ni des modifications apportées et identifiant de manière unique identifie l’objet dans l’espace de connecteur (par exemple, un numéro d’employé ou un ID utilisateur).

Un objet espace connecteur peut être une des opérations suivantes :

- Un objet intermédiaire
- Un espace réservé

### <a name="staging-objects"></a>Objets de mise en attente
Un objet intermédiaire représente une instance des types d’objet désigné de la source de données connectées. Outre le GUID et le nom unique, un objet intermédiaire comporte toujours une valeur qui indique le type d’objet.

Objets de mise en attente qui ont été importés toujours ont une valeur pour l’attribut d’ancrage. Objets de mise en attente qui ont été mis en service que vous venez par le moteur de synchronisation et qui sont en cours de création de la source de données connectées n’ont pas une valeur pour l’attribut d’ancrage.

Objets mis en œuvre aussi comporter des valeurs actuelles des attributs de l’entreprise et informations opérationnelles requises par le moteur de synchronisation pour exécuter le processus de synchronisation. Informations opérationnelles incluent des indicateurs qui indiquent le type de mises à jour sont transférées sur l’objet intermédiaire. Si un objet intermédiaire a reçu de nouvelles informations d’identité à partir de la source de données connectée qui n’a pas encore été traitée, l’objet est indiquée comme **importation en attente**. Si un objet intermédiaire contient les nouvelles informations d’identité qui n’ont pas encore été exportées à la source de données connectées, il est marqué comme **Exporter en attente**.

Un objet intermédiaire peut être un objet importation ou exportation. Le moteur de synchronisation crée un objet d’importation à l’aide d’informations sur l’objet reçues de la source de données connectées. Lorsque le moteur de synchronisation reçoit des informations sur l’existence d’un nouvel objet qui correspond à l’un des types d’objet sélectionnés dans le connecteur, qu’il crée un objet d’importation de l’espace connecteur comme une représentation de l’objet dans la source de données connectées.

L’illustration suivante montre un objet d’importation qui représente un objet dans la source de données connectées.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Le moteur de synchronisation crée un objet de l’exportation à l’aide des informations sur l’objet dans le méta-verse. Exporter des objets sont exportés vers la source de données connectée pendant la session de communication suivante. Du point de vue du moteur de synchronisation, exporter des objets n’existent pas dans la source de données connectée encore. Par conséquent, l’attribut d’ancrage d’un objet exporter n’est pas disponible. Après avoir reçu l’objet du moteur de synchronisation, la source de données connectée crée une valeur unique pour l’attribut d’ancrage de l’objet.

L’illustration suivante montre comment un objet exportation est créé à l’aide des informations d’identité dans le méta-verse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Le moteur de synchronisation confirme l’exportation de l’objet en réimporter l’objet de la source de données connectées. Exporter des objets deviennent importer des objets lorsque le moteur de synchronisation les reçoit lors de l’importation suivante de cette source de données connectées.

### <a name="placeholders"></a>Espaces réservés
Le moteur de synchronisation utilise un espace de noms plat pour stocker des objets. Toutefois, certaines sources de données connectées telles que Active Directory utilisent un espace de noms hiérarchique. Pour transformer les informations à partir d’un espace de noms hiérarchique dans un espace de noms plat, moteur de synchronisation utilise des espaces réservés pour conserver la hiérarchie.

Chaque espace réservé représente un composant (par exemple, une unité d’organisation) d’un nom d’objet hiérarchiques qui n’ont pas été importé dans le moteur de synchronisation mais est nécessaire pour créer le nom hiérarchique. Ils remplissage vides créés par des références dans la source de données connectées à des objets qui ne sont pas les intermédiaires objets dans l’espace de connecteur.

Le moteur de synchronisation utilise également des espaces réservés pour stocker les objets référencés n’ont pas encore été importés. Par exemple, si la synchronisation est configurée pour inclure l’attribut gestionnaire pour l’objet *Abbie Beaune* et la valeur reçue est un objet qui n’a pas été importé encore, tels que *CN = Lee Sperry, CN = utilisateurs, DC = fabrikam, DC = com*, les informations du gestionnaire sont stockées comme espaces réservés dans l’espace de connecteur. Si l’objet du gestionnaire est importé ultérieurement, l’objet espace réservé est remplacé par l’intermédiaire objet qui représente le responsable.

### <a name="metaverse-objects"></a>Objets métaverse
Un objet de méta-verse contient l’affichage agrégé ce moteur de synchronisation comporte des objets de l’espace de connecteur mise en attente. Moteur de synchronisation crée des objets de méta-verse en utilisant les informations dans Importer des objets. Plusieurs objets espace de connecteur peuvent être liés à un objet de méta-verse unique, mais un objet espace connecteur ne peut pas être lié à plusieurs objets métaverse.

Objets métaverse ne peuvent pas être créés ou supprimés manuellement. Le moteur de synchronisation supprime automatiquement les objets métaverse qui n’ont pas un lien vers n’importe quel objet espace connecteur dans l’espace connecteur.

Pour mapper des objets au sein d’une source de données connectée à un type d’objet correspondant dans le méta-verse, moteur de synchronisation fournit un schéma extensible avec des types d’objets et les attributs associés prédéfinies. Vous pouvez créer de nouveaux types d’objets et les attributs pour les objets métaverse. Des attributs peuvent être à valeur unique ou à plusieurs valeurs, et les types d’attributs peuvent être des chaînes, des références, des nombres et des valeurs booléennes.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relations entre les objets intermédiaires et méta-verse
Dans l’espace de noms du moteur de synchronisation, le flux de données est activé par la relation entre des liens entre les objets intermédiaires et méta-verse. Un objet intermédiaire qui est lié à un objet de méta-verse s’appelle une **rejoint objet** (ou un **objet de connecteur**). Un objet intermédiaire qui n’est pas lié à un objet de méta-verse s’appelle une **disjoint objet** (ou un **objet déconnecteur**). Les termes rejoint et disjoint sont préférés à ne confondez pas avec les connecteurs responsables de l’importation et exportation de données à partir d’un annuaire connecté.

Espaces réservés ne sont jamais liés à un objet métaverse

Un objet joint comprend un objet intermédiaire et sa relation liée à un objet seul métaverse. Objets joints sont utilisées pour synchroniser les valeurs d’attribut entre un objet espace connecteur et un objet métaverse.

Lorsqu’un objet intermédiaire devient un objet joint lors de la synchronisation, les attributs peuvent circuler entre l’objet intermédiaire et l’objet du métaverse. Flux des attributs est bidirectionnelle et est configuré à l’aide de règles d’attribut importation et exportation attribut.

Un objet espace connecteur unique peut être lié à un seul objet métaverse. Toutefois, chaque objet métaverse peut être lié à plusieurs objets espace connecteur dans la même ou dans les espaces de connecteur différent, comme le montre l’illustration suivante.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

La relation entre l’objet intermédiaire et un objet de méta-verse liée est permanente et peut être supprimée uniquement par les règles que vous spécifiez.

Un un objet est un objet intermédiaire qui n’est pas lié à n’importe quel objet métaverse. L’attribut valeurs d’un objet indépendantes ne sont pas traitées les pages dans le métaverse. Les valeurs d’attribut de l’objet correspondant dans la source de données connectée ne sont pas mises à jour par le moteur de synchronisation.

En utilisant un objets, vous pouvez stocker les informations d’identité dans le moteur de synchronisation et traiter ultérieurement. Conserver un objet intermédiaire sous forme d’un objet dans l’espace de connecteur offre de nombreux avantages. Étant donné que le système a déjà présente les informations requises sur cet objet, il n’est pas nécessaire créer une représentation de cet objet lors de la prochaine importation à partir de la source de données connectées. De cette façon, moteur de synchronisation a toujours un instantané complet de la source de données connectée, même s’il n’existe aucune connexion actuelle à la source de données connectées. Objets indépendantes peuvent être convertis en objets joints et vice versa, selon les règles que vous spécifiez.

Un objet d’importation est créé sous forme d’un objet. Un objet exportation doit être un objet joint. La logique du système applique cette règle et supprime tous les objets exportation qui ne sont pas un objet joint.

## <a name="sync-engine-identity-management-process"></a>Processus de gestion des identités synchronisation moteur
Le processus de gestion des identités contrôles comment mettre à jour les informations d’identité entre les sources de données connectées différents. Gestion des identités se produisent dans trois processus :

- Importation
- Synchronisation
- Exporter

Pendant le processus d’importation, moteur de synchronisation évalue les informations d’identité entrants à partir d’une source de données connectées. Lorsque des modifications sont détectées, il crée de nouveaux objets intermédiaires ou met à jour des objets intermédiaires existants dans l’espace de connecteur pour la synchronisation.

Pendant le processus de synchronisation, moteur de synchronisation met à jour le métaverse pour refléter les modifications qui ont été apportées à l’espace de connecteur et met à jour de l’espace de connecteur pour refléter les modifications qui se sont produites dans le méta-verse.

Pendant le processus d’exportation, moteur de synchronisation pousse les modifications qui sont transférées sur les objets de reclassement et qui sont signalés en attente d’exportation.

L’illustration suivante montre où chacun des processus s’est produite en tant que flux d’informations identité à partir d’une source de données connectées à un autre.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processus d’importation
Pendant le processus d’importation, moteur de synchronisation évalue les mises à jour des informations d’identité. Moteur de synchronisation compare les informations d’identité provenance de la source de données connectées avec les informations d’identité relatives à un objet intermédiaire et détermine si l’objet intermédiaire requiert des mises à jour. S’il est nécessaire de mettre à jour l’objet intermédiaire avec de nouvelles données, l’objet intermédiaire est indiquée en attente d’importation.

Par l’intermédiaire des objets dans l’espace connecteur avant la synchronisation, moteur de synchronisation peut traiter uniquement les informations d’identité qui a changé. Ce processus offre les avantages suivants :

- **Synchronisation efficace**. La quantité de données traitées pendant la synchronisation est réduite.
- **Resynchronisation efficace**. Vous pouvez modifier la façon dont moteur de synchronisation traite les informations d’identité sans que vous vous reconnectez au moteur de synchronisation de la source de données.
- **Opportunité pour afficher un aperçu de la synchronisation**. Vous pouvez afficher un aperçu de la synchronisation pour vérifier que les hypothèses sur le processus de gestion des identités sont corrects.

Pour chaque objet spécifié dans le connecteur, le moteur de synchronisation tout d’abord essaie de localiser une représentation de l’objet dans l’espace de connecteur du connecteur. Moteur de synchronisation examine tous les objets intermédiaires dans l’espace de connecteur et tente de trouver un objet intermédiaire correspondant qui a un attribut de point d’ancrage correspondant. Si aucun objet intermédiaire existant n’a un attribut de point d’ancrage correspondant, moteur de synchronisation essaie de trouver un objet intermédiaire correspondant portant le même nom unique.

Lorsque le moteur de synchronisation détecte un objet intermédiaire qui correspond à par un nom unique, mais pas par un point d’ancrage, les comportements spéciaux suivants se produisent :

- Si l’objet situé dans l’espace de connecteur n’a aucun point d’ancrage, moteur de synchronisation supprime cet objet de l’espace de connecteur et marque l’objet métaverse qu'il est lié à sous forme de **Réessayer de mise en service lors de la prochaine synchronisation exécuter**. Il crée ensuite l’objet d’importation.
- Si l’objet situé dans l’espace de connecteur a un point d’ancrage, moteur de synchronisation suppose que cet objet a été renommé ou supprimé dans l’annuaire connecté. Il attribue un nom unique temporaire, de nouvel pour l’objet espace connecteur afin qu’il peut l’étape l’objet entrante. L’ancien objet est alors **transitoires**, en attente pour le connecteur à importer la renommer ou la suppression pour résoudre le problème.

Si le moteur de synchronisation localise un objet intermédiaire qui correspond à l’objet spécifié dans le connecteur, il détermine quel type d’appliquer ces modifications. Par exemple, moteur de synchronisation est peut-être renommer ou supprimer l’objet dans la source de données connectée, ou elle peut uniquement mettre à jour les valeurs des attributs de l’objet.

Objets de mise en attente avec des données mises à jour sont signalés en attente d’importation. Différents types d’en attente importations sont disponibles. Selon le résultat du processus d’importation, un objet de mise en attente dans l’espace connecteur comporte une des opérations suivantes en attente de types d’importation :

- **Aucun**. Aucune modification apportée à tous les attributs de l’objet intermédiaire n’est disponibles. Moteur de synchronisation ne marque pas de ce type en attente d’importation.
- **Ajouter**. L’objet intermédiaire est un nouvel objet importer dans l’espace connecteur. Moteur de synchronisation indicateurs ce type en attente d’importation pour traitement supplémentaire dans le méta-verse.
- **Mise à jour**. Moteur de synchronisation trouve un objet intermédiaire correspondant dans l’espace de connecteur et indicateurs de ce type comme en attente d’importation afin que les mises à jour les attributs peuvent être traités dans le méta-verse. Mises à jour incluent la modification du nom de l’objet.
- **Supprimer**. Moteur de synchronisation trouve un objet intermédiaire correspondant dans l’espace de connecteur et indicateurs de ce type comme en attente d’importation afin que l’objet joint peut être supprimé.
- **Ajouter/supprimer**. Moteur de synchronisation détecte un objet intermédiaire correspondant dans l’espace connecteur, mais les types d’objets ne correspondent pas. Dans ce cas, une modification est mis en place ajouter de supprimer. A supprimer-ajouter modification indique au moteur de synchronisation qu’une resynchronisation complète de cet objet doit avoir lieu car différents ensembles de règles appliquent à cet objet lorsque l’objet tapez modifications.

En définissant le statut d’importation en attente d’un objet intermédiaire, il est possible de réduire la quantité de données traitées pendant la synchronisation, car vous pouvez ainsi le système traiter uniquement les objets qui ont mis à jour les données de manière significative.

### <a name="synchronization-process"></a>Processus de synchronisation
La synchronisation se compose de deux processus connexes :

- Synchronisation entrante, lorsque le contenu du méta-verse est mis à jour en utilisant les données dans l’espace de connecteur.
- Synchronisation sortante, lorsque le contenu de l’espace de connecteur est mis à jour à l’aide de données dans le méta-verse.

En utilisant les informations dans l’espace de connecteur, le processus de synchronisation entrant crée dans le méta-verse l’affichage intégré des données qui sont stockées dans les sources de données connectées. Tous les objets intermédiaires ou uniquement ceux dont une en attente importer des informations sont regroupées selon la façon dont les règles sont configurées.

Les mises à jour du processus de synchronisation sortant exporter des objets lorsque les objets métaverse changent.

Synchronisation entrante crée la vue intégrée dans le méta-verse les informations d’identité reçus à partir de sources de données connectées. Moteur de synchronisation peut traiter les informations d’identité à tout moment en utilisant les dernières informations d’identité qu’il a à partir de la source de données connectées.

**Synchronisation entrante**

Synchronisation entrante inclut les processus suivants :

- **Mise en service** (également appelé **Projection** s’il est important de distinguer ce processus de mise en service de synchronisation sortante). Le moteur de synchronisation crée un nouvel objet métaverse basé sur un objet intermédiaire et les lie. Mise en service est une opération au niveau des objets.
- **Rejoindre**. Le moteur de synchronisation lie un objet intermédiaire à un objet métaverse existant. Une jointure est une opération au niveau des objets.
- **Flux des attributs importer**. Moteur de synchronisation met à jour les valeurs d’attribut, appelés flux des attributs, de l’objet dans le méta-verse. Flux des attributs importation est une opération au niveau des attributs qui nécessite un lien entre un objet intermédiaire et un objet de méta-verse.

Mise en service est le seul processus qui crée des objets dans le méta-verse. Mise en service affecte uniquement importer des objets qui sont des objets indépendantes. Pendant la mise en service, moteur de synchronisation crée un objet métaverse qui correspond au type d’objet de l’objet d’importation et établit un lien entre les deux objets, créant ainsi un objet joint.

Le processus de jointure établit également un lien entre importer des objets et un objet de méta-verse. La différence entre jointure et mise en service est que le processus de jointure nécessite que l’objet d’importation sont liés à un objet métaverse existant, où le processus de mise en service crée un nouvel objet métaverse.

Moteur de synchronisation essaie de rejoindre un objet d’importation pour un objet de méta-verse à l’aide de critères spécifié dans la configuration de la règle de synchronisation.

Lors du processus de mise en service et de jointure, moteur de synchronisation lie un un objet à un objet de méta-verse désigner rejoint. Après avoir réalisé ces opérations au niveau des objets, moteur de synchronisation peut mettre à jour les valeurs d’attribut de l’objet du métaverse associé. Ce processus est appelé flux des attributs importer.

Flux des attributs importation se produit sur tous les objets d’importation qui effectue les nouvelles données et sont liés à un objet métaverse.

**Synchronisation sortante**

Mises à jour de la synchronisation sortant exporter les objets lorsqu’un objet de méta-verse modifier mais n’est pas supprimé. L’objectif de synchronisation sortante consiste à évaluer si les modifications apportées aux objets métaverse nécessitent des mises à jour aux objets intermédiaires dans les espaces de connecteur. Dans certains cas, les modifications peuvent nécessiter cette mise en attente objets dans tous les espaces de connecteur être mis à jour. Objets de mise en attente qui sont modifiées sont signalés en attente d’exportation, afin de les exporter des objets. Ces exporter les objets sont plus tard repoussées à la source de données connectée pendant le processus d’exportation.

Synchronisation sortante comporte trois processus :

- **Mise en service**
- **Annulation**
- **Exporter des flux d’attributs**

Création et la suppression sont les deux opérations au niveau de l’objet. Annulation dépend de la mise en service, car uniquement la mise en service peut la lancer. Annulation se déclenche lors de la mise en service supprime le lien entre un objet de méta-verse et un objet exporter.

Mise en service est toujours déclenchée lorsque les modifications sont appliquées aux objets dans le méta-verse. Lorsque des modifications sont apportées aux objets du métaverse, moteur de synchronisation peut effectuer les tâches suivantes dans le cadre du processus de mise en service :

- Créer des objets joints, où un objet métaverse est lié à un objet exportation nouvellement créé.
- Renommer un objet joint.
- Disjoindre les liens entre un objet de méta-verse et mis en œuvre des objets, création d’un objet indépendantes.

Si la mise en service requiert moteur de synchronisation pour créer un nouvel objet connecteur, l’objet intermédiaire à laquelle l’objet du métaverse est liée est toujours un objet à exporter, car l’objet n’existe pas encore dans la source de données connectées.

Si la mise en service nécessite le moteur de synchronisation pour disjoindre un objet joint, création d’un objet indépendantes, annulation se déclenche. Le processus d’annulation supprime l’objet.

Au cours de l’annulation, la suppression d’un objet exporter ne supprime pas physiquement l’objet. L’objet est marqué comme **supprimé**, ce qui signifie que l’opération de suppression fonctionne à partir de l’objet.

Flux des attributs exporter également observé pendant le processus de synchronisation sortant, similaire à celle que flux des attributs importation se produit lors de la synchronisation entrante. Flux des attributs exportation se produit uniquement entre des objets métaverse et d’exportation qui sont jointes.

### <a name="export-process"></a>Processus d’exportation
Pendant le processus d’exportation, moteur de synchronisation examine tous les objets d’exportation qui sont signalés en attente d’exportation de l’espace connecteur, puis envoie des mises à jour à la source de données connectées.

Le moteur de synchronisation pour évaluer la réussite d’une exportation, mais elle ne peut pas déterminer suffisamment que le processus de gestion des identités est terminé. Objets de la source de données connectées peuvent toujours être modifiées par d’autres processus. Moteur de synchronisation n’a pas d’une connexion permanente à la source de données connectée, il n’est pas suffisant pour rendre les hypothèses sur les propriétés d’un objet dans la source de données connectée uniquement basée sur une notification de réussite de l’exportation.

Par exemple, un processus dans la source de données connectée peut modifier les attributs de l’objet leurs valeurs d’origine (autrement dit, de la source de données connectée pourrait écraser les valeurs immédiatement une fois que les données sont transférées par le moteur de synchronisation et appliquées avec succès dans la source de données connectées).

Les banques de moteur de synchronisation exporter et importer des informations d’état sur chaque objet intermédiaire. Si les valeurs des attributs qui sont spécifiées dans la liste d’inclusion attribut ont changé depuis la dernière exportation, le stockage d’importer et exporter des moteur de synchronisation vous permettent de conserver état pour réagir correctement. Moteur de synchronisation utilise le processus d’importation pour confirmer les valeurs des attributs qui ont été exportés à la source de données connectées. Comparaison entre les informations importées et exportées, comme le montre l’illustration suivante permet de moteur de synchronisation déterminer si l’exportation a réussi, ou si elle doit être répété.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Par exemple, si le moteur de synchronisation exporte attribut C, qui a la valeur 5, à une source de données connectées, il stocke C = 5 en mémoire état exporter. Chaque exportation supplémentaires sur cet objet entraîne une tentative d’exportation C = 5 à la source de données connectées à nouveau, car le moteur de synchronisation suppose que cette valeur n'a pas été permanence appliquée à l’objet (autrement dit, sauf si une valeur différente a été récemment importée à partir de la source de données connectées). La mémoire de l’exportation est désactivée lors de la réception C = 5 pendant une opération d’importation sur l’objet.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

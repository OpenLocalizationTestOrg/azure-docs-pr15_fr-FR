<properties
   pageTitle="Sécurité au niveau de la ligne avec Power BI incorporé"
   description="Plus d’informations sur la sécurité au niveau des lignes avec Power BI incorporé"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>Sécurité au niveau des lignes avec Power BI incorporé

Sécurité au niveau de ligne (RLS) peut servir à limiter l’accès utilisateur aux données particuliers au sein d’un rapport ou un jeu de données, ce qui permet à plusieurs différents utilisateurs pour utiliser le même rapport lors de toutes les données de différentes voir. Power BI incorporé prend désormais en charge les jeux de données configurés avec RLS.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Pour tirer parti des RLS, il est important de que comprendre les trois principaux concepts ; Les utilisateurs, les rôles et les règles. Jetons un coup aperçu à chaque :

**Utilisateurs** – il s’agit des utilisateurs finaux réels affichage de rapports. Dans Power BI incorporés, les utilisateurs sont identifiés par la propriété nom d’utilisateur dans un jeton d’application.

**Rôles** – utilisateurs appartiennent aux rôles. Un rôle est un conteneur pour les règles et il peut être nommé de quelque chose comme « Directeur commercial » ou « Commercial ». Dans Power BI incorporés, les utilisateurs sont identifiés par la propriété de rôles dans un jeton d’application.

**Règles** – règles des rôles, et ces règles sont les filtres réels devant être appliqué aux données. Cela peut être aussi simple que « pays = France » ou quelque chose de plus dynamique.

### <a name="example"></a>Exemple

Pour le reste de cet article, nous allons fournissent un exemple de rédaction RLS et puis par d’autres programmes que dans une application incorporée. Notre exemple utilise le fichier PBIX [Exemple d’analyse de vente au détail](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Notre exemple de vente au détail analyse montre des ventes pour toutes les banques dans une chaîne de distribution particulier. Sans RLS, quel que soit le secteur responsable se connecte et affiche le rapport, ils verront les mêmes données. Direction a déterminé chaque directeur régional doit afficher uniquement les ventes pour les banques qu’ils gérer, et pour ce faire, nous pouvons utiliser RLS.

RLS a été créé dans Power BI Desktop. Lorsque le jeu de données et le rapport sont ouverts, nous pouvons basculer en mode diagramme pour voir le schéma :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Voici quelques éléments à noter avec ce schéma :

-   Toutes les mesures, comme **Total des ventes**, sont stockés dans la table de faits **Sales** .
-   Il existe quatre tables de dimension connexes supplémentaires : **élément**, **l’heure**, **Store**et **secteur**.
-   Les flèches sur les lignes de relation indiquent de quelle façon filtres peuvent être transmis à partir d’une table à l’autre. Par exemple, si un filtre est placé dans les **temps [Date]**, dans le schéma actuel il uniquement filtrez les valeurs dans la table **Sales** . Aucune autre table ne serait affectés par ce filtre dans la mesure où toutes les flèches situées sur les lignes de relation pointent vers la table sales et pas absent (e).
-   Le tableau **secteur** indique qui est le responsable pour chaque secteur :

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Basé sur ce schéma, si nous appliquer un filtre à la colonne **Directeur régional** de la table secteur, et si ce filtre correspond à l’utilisateur la consultation du rapport, ce filtre filtre également vers le bas les tables **Store** et **Sales** pour afficher uniquement les données pour ce secteur particulier gestionnaire.

Voici comment :

1.  Sous l’onglet modélisation, cliquez sur **Gérer les rôles**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Créer un rôle appelé **Gestionnaire**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  Dans la table **secteur** Entrez l’expression DAX suivante : **[directeur régional] = USERNAME()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Pour vous assurer que les règles sont fonctionne, sous l’onglet **modélisation** , cliquez sur **Afficher en tant que rôles**, puis entrez les éléments suivants :  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Les rapports seront affichera données comme si vous ont été connecté en tant **qu’Andrew Ma**.

Appliquer le filtre, comme nous l’avons fait ici, filtre vers le bas de tous les enregistrements des tables **secteur**, **stocker**et **ventes** . Toutefois, en raison de la direction de filtre sur les relations entre les **ventes** et **l’heure**, tables **ventes** et **élément**et **élément** et **l’heure** ne seront pas filtrés vers le bas.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Qui peut être OK pour cette exigence, cependant, si nous ne souhaitez pas que les responsables pour afficher les éléments pour lesquels ils ne disposent des ventes, nous pouvons activer bidirectionnelle filtrage pour la relation et le filtre de sécurité dans les deux sens de flux. Cela peut être exécuté en modifiant la relation entre les **ventes** et d’un **élément**, comme suit :

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

À présent, filtres peuvent également se dérouler à partir de la table Sales à la table **d’élément** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Remarque** Si vous utilisez le mode DirectQuery pour vos données, vous devez activer le filtrage des bidirectionnelle entre en sélectionnant ces deux options :

1.  **Fichier** -> **Options et paramètres** -> **Fonctionnalités** -> **Activer croisée filtrage dans les deux sens pour DirectQuery**.
2.  **Fichier** -> **Options et paramètres** -> **DirectQuery** -> **Autoriser mesure non restreint en mode DirectQuery**.


Pour en savoir plus sur bidirectionnelle filtrage, téléchargez le livre blanc [bidirectionnelle filtrage dans SQL Server Analysis Services 2016 et Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) .

Il n’est plus visible tout le travail qui doit être effectuée dans Power BI Desktop, mais il existe une plus petite tâche qui doit être effectuée pour rendre la RLS règles que nous définies travail dans Power BI incorporé. Les utilisateurs sont authentifiés et autorisés par votre application et application jetons sont utilisés pour accorder l’accès à un rapport Power BI incorporé spécifique. Power BI incorporé n’a pas d’informations spécifiques sur le destinataire est votre utilisateur. Pour RLS pour l’utiliser, vous devez passer du contexte supplémentaire dans le cadre de votre jeton d’application :
-   **nom d’utilisateur** (facultatif) – utilisée avec RLS c’est une chaîne qui peut être utilisée pour aider à identifier l’utilisateur lors de l’application de règles RLS. Voir Utilisation de la sécurité au niveau avec Power BI incorporée de ligne
-   **rôles** – une chaîne contenant les rôles pour sélectionner lors de l’application de règles de sécurité au niveau de ligne. Si vous passez plusieurs rôles, ils doivent être passés comme un tableau de chaînes.

Si la propriété nom d’utilisateur est présente, vous devez également passer au moins une valeur dans les rôles.

Le jeton application complète doit ressembler à ceci :

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

À présent, avec tous les éléments ensemble, lorsqu’une personne se connecte à notre application pour afficher le rapport, elle seulement pourra afficher les données qu’ils sont autorisés à visualiser, telle que définie par notre sécurité au niveau des lignes.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Voir aussi
[Sécurité au niveau des lignes (RLS) avec Power](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)

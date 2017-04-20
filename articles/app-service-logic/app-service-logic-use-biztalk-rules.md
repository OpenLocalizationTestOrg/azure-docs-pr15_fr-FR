<properties
   pageTitle="En savoir plus sur et créer une application BizTalk règles API dans votre application logique | Microsoft Azure"
   description="Cette rubrique couvre les fonctionnalités du connecteur BizTalk règles et fournit des instructions sur l’utilisation de son"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>Règles de BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Des règles d’entreprise intègre les stratégies et les décisions qui contrôlent les processus d’entreprise. Ces politiques peuvent être définis auparavant dans manuels de procédure, les contrats ou accords ou existent en tant que des connaissances ou de l’expertise incorporés dans des employés. Ces politiques sont dynamiques et sujettes à des modifications du temps en raison de modifications offre pour les entreprises, réglementaires ou autres raisons.

Mise en œuvre de ces politiques dans langages de programmation traditionnels nécessite débute et l’heure significative et ne permet pas de non-développeurs de participer à la création et la maintenance des stratégies d’entreprise. Règles d’entreprise BizTalk fournit un moyen rapide implémenter ces stratégies et dissocier le reste du processus métier. Cela permet d’apporter des modifications nécessaires aux stratégies d’entreprise sans ayant un impact sur le reste du processus métier.

##<a name="why-rules"></a>Pourquoi des règles

Il existe 3 bonnes raisons d’utiliser des règles d’entreprise BizTalk processus métier :

* Dissocier la logique métier à partir de code de l’Application
- Permettre aux analystes d’avoir plus de contrôle sur la gestion de logique métier
+ Modifications apportées à la logique métier aller plus vite en production

##<a name="rules-concepts"></a>Concepts de règles

##<a name="vocabulary"></a>Vocabulaire

Un _vocabulaire_ est un ensemble de définitions constitué de noms conviviales pour les objets informatiques utilisés dans les actions et conditions de règle. Définitions de vocabulaire rendre ces règles plus faciles à lire et comprendre partager par les personnes dans un domaine particulier.

Vocabulaires sont conçus pour réduire l’écart entre sémantique métier et mise en œuvre. Par exemple, une liaison de données pour un statut d’approbation peut pointer vers une certaine colonne dans une certaine ligne dans une certaine base de données, représenté sous la forme d’une requête SQL. Plutôt que d’insérer ce type de représentation complexe dans une règle, vous pouvez créer à la place d’une définition de vocabulaire associée à cette liaison de données, avec un nom convivial de « État ». Par la suite, vous pouvez inclure « État » dans n’importe quel nombre de règles et le moteur de règles pouvez extraire les données correspondantes de la table.

##<a name="rule"></a>Règle

Les règles d’entreprise sont des instructions déclaratives qui régissent le fonctionnement des processus métiers. Une règle est composée d’une condition et d’actions. La condition est évaluée et si elle est vrai, le moteur de règles lance une ou plusieurs actions.
Règles dans le cadre de règles métier sont définies en utilisant le format suivant :

_IF_ _condition_ _Puis_ _action_

Prenons l’exemple suivant :

*Montant IF est inférieure ou égale à effectuer et les fonds disponibles*  
*PUIS diriger transaction et réception d’impression*

Cette règle détermine si une transaction est effectuée en appliquant la logique métier, sous la forme d’une comparaison de deux valeurs monétaires, sous la forme d’un montant de transaction et fonds disponibles.
Vous pouvez utiliser la règle d’entreprise pour créer, modifier et déployer des règles d’entreprise. Par ailleurs, vous pouvez effectuer les tâches précédentes par programme.

###<a name="conditions"></a>Conditions

Une condition est un expression (booléenne) qui se compose d’un ou plusieurs prédicats vrai/faux.
Dans notre exemple, les prédicat inférieur ou égal à sont appliquée à la quantité et les fonds disponibles. Cette condition évalue toujours true ou false.
Prédicats peuvent être combinées avec les opérateurs logiques et, ou et ne pas pour former une expression logique qui est potentiellement très volumineux, mais aura toujours la valeur true ou false.

###<a name="actions"></a>Actions

Les actions sont les conséquences fonctionnelles d’évaluation de la condition. Si une condition est remplie, une ou plusieurs actions correspondants sont lancées.
Dans notre exemple, « conduite transaction » et « imprimer réception » est les actions réalisées lorsqu’et uniquement lorsque la condition (dans ce cas, « si montant est inférieure ou égale à effectuer et les fonds disponibles ») est vraie.
Actions sont représentées dans l’infrastructure de règles d’entreprise en effectuant les opérations définies sur des documents XML.

##<a name="policy"></a>Stratégie

Une stratégie est un regroupement logique de règles. Vous rédigez une stratégie, enregistrez, testez et, lorsque vous êtes satisfait des résultats, l’utiliser dans un environnement de production.

###<a name="policy-composition"></a>Composition de stratégie

Vous pouvez composer les stratégies dans le portail de règles. Une stratégie peut contenir un jeu arbitraire très élevé de règles, mais en règle générale vous composez une stratégie de règles relatives à un domaine spécifique dans le cadre de l’application qui utilisent la stratégie.

###<a name="policy-testing"></a>Test de stratégie
Vous pouvez effectuer efficacement une série de tests de votre stratégie avant d’être utilisée dans un environnement de production. Le portail de règles vous permet de fournir des entrées à une stratégie, exécutez la stratégie et afficher sa sortie. La sortie inclut les journaux, l’exécution de règle, évaluation de la condition et les sorties qui en résulte.

##<a name="sample-scenario---insurance-claims"></a>Exemple de scénario - déclaration de sinistre
Examinons un exemple de scénario et passez en revue il comme nous composer la logique métier pour la même.

![Texte de remplacement][1]

Dans un scénario de déclaration de sinistre vraiment simple, le demandeur soumet son sinistre (via n’importe quel client comme site Web, téléphone application, etc.). Cette demande de réclamer obtient envoyée à unité de traitement de l’entreprise réclamer et en fonction du résultat de la transformation, la demande de remboursement peut être soit approuvé, rejeté ou envoyé le long pour poursuivre le traitement manuel.
L’unité de traitement de demande dans notre scénario serait celui englobant la logique métier pour le système. Examiner de plus près à cette unité, nous pouvons voir les rubriques suivantes :

![Texte de remplacement][2]

Laissez-nous maintenant utiliser des règles d’entreprise pour implémenter cette logique métier.


##<a name="creation-of-rules-api-app"></a>Création de règles Api application


1. Connexion au portail Azure
2. Sélectionnez New -> Marketplace ensuite chercher des *Règles BizTalk*
3. Sélectionnez règles BizTalk dans la liste des résultats. Ouvrir la carte de règles BizTalk
4. Sélectionnez le bouton *créer* ![le texte de remplacement][3]
1. Dans la nouvelle carte qui s’ouvre, entrez les informations suivantes :  
    1. Nom – donner un nom pour votre application de l’API de règles
    1. Plan de services d’application – sélectionner ou créer un nouveau Plan de Service de l’application
    1. Tarifs couche – choisir le niveau de tarification vous voulez résident dans cette application
    1. Groupe de ressources – sélectionner ou créer le groupe de ressources dans lequel l’application doit se trouver dans
    2. Abonnement, sélectionnez l’abonnement que vous souhaitez utiliser
    1. Emplacement – choisissez l’emplacement géographique dans lequel vous voulez que l’application à déployer.
4.  Sélectionnez *créer*. Votre application BizTalk règles API seraient créée dans quelques minutes.

##<a name="vocabulary-creation"></a>Création de vocabulaire
Après avoir créé une application BizTalk règles API, l’étape suivante serait de créer des vocabulaires. L’objectif est que le développeur est le plus courant personnage qu’il faut pour cet exercice. Voici comment faire cela au plus :


1. Menu de lancement votre BizTalk règles API application à partir du portail en accédant à parcourir -> applications API - ><Your Rules API App>. Cela obtiendrez vous au tableau de bord règles API application similaire à ci-dessous :

   ![Texte de remplacement][4]

2.Cliquez sélectionnez « Définitions de vocabulaire ». Cela vous permet d’afficher l’écran de création de vocabulaire 3 Sélectionnez « Ajouter » pour commencer à ajouter des définitions de vocabulaire.
Il existe 2 types de définitions de vocabulaire actuellement pris en charge – littéral et XML.

##<a name="literal-definition"></a>Définition littérale
1.  Après avoir cliqué sur « Ajouter », une nouvelle carte « Ajouter une définition » s’ouvre. Entrez les valeurs suivantes
  1.    Nom – seuls les caractères alphanumériques sont prévus sans caractères spéciaux. Cela doit également être unique à votre liste de définition de vocabulaire existante.
  2.    Description – champ facultatif.
  3.    Type de définition – 2 types sont pris en charge. Dans cet exemple, sélectionnez littéral
  4.    Type de données – Cela permet aux utilisateurs de sélectionner le type de données de la définition. Actuellement les types de 4 données sont prises en charge : j’ai.  Chaîne – ces valeurs doivent être entrées par des guillemets doubles (« exemple de chaîne »)  
    II. Booléenne – cela peut être vrai ou faux  
    III.    Nombre – peut être tout nombre décimal  
    IV. Date/heure –, cela signifie que la définition est de type date. Données doivent être entrées à l’aide de ce format : mm/jj/aaaa hh : mm : AM\PM  
  5. Entrée – il s’agit de l’endroit où vous entrez la valeur de la définition de votre. Les valeurs entrées ici doivent être conformes au type de données choisi. Vous pouvez soit entrer une valeur unique, un ensemble de valeurs séparées par des virgules ou une plage de valeurs à l’aide du mot clé *à*. Par exemple, vous pouvez entrer la valeur unique 1 ; un ensemble de 1, 2, 3 ; ou une plage de 1 à 5. Notez que la plage est uniquement prise en charge pour les nombres.
  6. Sélectionnez *OK*.

![Texte de remplacement][5]
##<a name="xml-definition"></a>Définition de XML
Si vous choisissez vocabulaire Type au format XML, les entrées suivantes doit être indiquée  
  un.    Schéma – en cliquant sur cela s’ouvre un nouvel utilisateur permettant de carte pour choisir dans la liste des schémas déjà téléchargés ou autorisés à télécharger un.
b.    XPATH – des entrées obtient déverrouillées uniquement après le choix d’un schéma à l’étape précédente. Cliquez sur ce problème pour afficher le schéma qui a été activé et permet à l’utilisateur sélectionner le nœud pour lequel une définition de vocabulaire doit être créé.  
  c.    FACT – cette entrée identifie l’objet de données serait fed au moteur de règles pour le traitement. Ceci est une propriété avancée et par défaut est défini sur le parent de l’expression XPATH sélectionné. FACT est particulièrement important pour les scénarios chaîne et collection de sites.

![Texte de remplacement][6]

### <a name="add-bulk"></a>Ajouter en bloc
Les étapes ci-dessus ont capturé l’expérience de création des définitions de vocabulaire. Une fois créé, les définitions de vocabulaire créé seront affichent sous forme de liste. Il existe des exigences pour être en mesure de générer plusieurs définitions à partir du même schéma au lieu de répéter les étapes ci-dessus chaque fois unique. Il s’agit de l’endroit où ajouter en bloc capacité redevient très utile.
Sélection de « Ajouter en bloc » vous accédez alors à une nouvelle carte. La première étape consiste à sélectionner le schéma pour laquelle plusieurs définitions doivent être créés. Si vous sélectionnez cette ouvre une nouvelle carte autorisant que vous choisissez parmi une liste des schémas déjà téléchargés ou autorisés à télécharger un.
À présent la propriété XPath obtient déverrouillée. Si vous sélectionnez cette ouvre la visionneuse des schémas où plusieurs nœuds pouvant être sélectionnés.
Les noms pour les définitions de plusieurs créées par défaut est le nom du nœud sélectionné. Elles peuvent toujours être modifiées après sa création.

![Texte de remplacement][7]

##<a name="policy-creation"></a>Création d’une stratégie
Une fois que le développeur a créé vocabulaires requis, l’objectif est que l’analyste d’entreprise serait l’une création des stratégies d’entreprise via le portail Azure.  
    1. dans l’application de règles créé, il est un objectif de stratégie en cliquant sur lequel l’utilisateur doit être placé dans la page de création d’une stratégie.  
    2. cette page affiche la liste des stratégies de cette application de règles particulier. L’analyste peut ajouter une nouvelle stratégie en simplement à taper un nom de stratégie et activer la touche tab à deux reprises. Plusieurs stratégies peuvent résider dans une application de l’API de règles unique.  
    3. sélection de la stratégie créée vous accédez alors à l’utilisateur de la page de détails de la stratégie d’où une peut voir les règles qui se trouvent dans la stratégie.  
    ![Texte de remplacement][8]
 4.  Sélectionnez « Ajouter » pour ajouter une nouvelle règle. Vous accédez alors à une nouvelle carte.

##<a name="rule-creation"></a>Création d’une règle
Une règle est collection d’instructions condition et une action. Les actions sont exécutées si la condition est vraie. Dans la carte de créer une règle, donnez un nom unique de la règle (pour cette stratégie) et une description (facultative).
La case Condition (si) pouvant être utilisée pour créer des instructions conditionnelles complexes. Voici les mots clés pris en charge :  
1.  Et – opérateur conditionnel  
2.  Ou – opérateur conditionnel  
3.  a\_pas\_existent  
4.  Il existe  
5.  faux  
6.  est\_égal\_à  
7.  est\_supérieure\_que  
8.  est\_supérieure\_à\_égal\_à  
9.  est\_dans  
10. est\_moins\_que  
11. est\_moins\_à\_égal\_à  
12. est\_pas\_dans  
13. est\_pas\_égal\_à  
14. Mod  
15. Vrai  

La zone Action(THEN) peut contenir plusieurs instructions, un par ligne, pour créer des actions qui doivent être exécutées. Voici les mots clés pris en charge :  
1.  égal à  
2.  faux  
3.  Vrai  
4.  arrêter  
5.  Mod  
6.  null  
7.  mise à jour  

Les zones condition et une action fournissent Intellisense pour vous aider à créer rapidement une règle. Cela peut être déclenchée en appuyant sur ctrl + espace ou en démarrant simplement à taper. Mots clés de la correspondance des caractères tapés seront automatiquement filtrés vers le bas et affichés. La fenêtre intellisense affiche tous les mots clés et les définitions de vocabulaire.
![Texte de remplacement][9]

##<a name="explicit-forward-chaining"></a>Transférer explicite chaîne
Règles BizTalk prend en charge, chaîne transférer si les utilisateurs souhaitent réévaluez règles en réponse à certaines actions explicites, ils peuvent cela déclencher à l’aide de certains mots clés. Les mots clés pris en charge sont les suivantes :  
   1.   mettre à jour <vocabulary definition> – ce mot clé évalue de nouveau toutes les règles qui utilisent la définition de vocabulaire spécifié dans son état.  
   2.   Arrêt – ce mot clé s’arrête toutes les exécutions de règle

##<a name="enabledisable-rules"></a>Activer/désactiver les règles
Chaque règle de la stratégie peut être activée ou désactivée. Toutes les règles sont activées par défaut. Règles désactivées pas être exécutées pendant l’exécution de stratégie. Activer/désactiver les règles peuvent être effectuées à partir de la carte de règle directement : les commandes sont disponibles dans la barre de commandes en haut de la carte, ou à partir de la stratégie, le menu contextuel (clic droit sur une règle) a la possibilité d’activer/désactiver.

##<a name="rule-priority"></a>Priorité de la règle
Toutes les règles d’une stratégie sont exécutées dans l’ordre. La priorité d’exécution est déterminée par l’ordre dans lequel ils apparaissent dans la stratégie. Cette priorité peut être modifiée en faisant simplement glisser la règle.

##<a name="test-policy"></a>Tester la stratégie
Vous pouvez tester vos stratégies à l’aide de la commande « Tester la stratégie » dans la carte de stratégie de Test. Dans cette carte, vous pouvez afficher une liste de définitions de vocabulaire qui sont utilisées dans la stratégie nécessitant une entrée d’utilisateur. Les utilisateurs peuvent ajouter manuellement des valeurs pour ces entrées pour leur scénario de test. Vous pouvez également les utilisateurs peuvent également choisir d’importer tester XMLs pour les entrées. Une fois toutes les entrées, le test peut être exécuté et les sorties pour chaque définition de vocabulaire seront affichera dans la colonne sortie pour faciliter la comparaison. Pour afficher les journaux conviviales analyste d’entreprise, cliquez sur « Afficher les journaux » pour afficher les journaux d’exécution. Pour enregistrer les journaux, l’option « Enregistrer la sortie » est disponible pour stocker toutes les données associées pour analyse indépendante de test.

## <a name="using-rules-in-logic-apps"></a>À l’aide de règles dans les applications de logique
Une fois que la stratégie a été créée et testée, il est désormais prêt à la consommation. Vous pouvez créer une nouvelle application logique en sélectionnant applications logique du côté gauche de la page d’accueil du portail. Une fois que votre application logique a été créée, la lancer, puis sélectionnez *déclencheurs et Actions*. Vous pouvez puis sélectionnez le modèle *créer à partir de zéro* . Suivez les étapes pour ajouter votre application BizTalk règles API à l’application logique. Une fois que cela est fait, il sera en mesure de choisir les règles API application (Action) à la cible. Les actions incluent la liste des stratégies qui doivent être exécutées. Choisissez une stratégie spécifique après laquelle les entrées requis pour la stratégie doit avoir. Les utilisateurs peuvent utiliser la sortie de l’application en aval de règles de l’API pour la prise de décisions supplémentaire.

## <a name="using-rules-via-apis"></a>À l’aide de règles via API
L’application de l’API de règles peut également être appelée à l’aide d’un large éventail d’API. Ainsi, les utilisateurs ne sont pas limités à uniquement à l’aide de la logique d’applications et peuvent utiliser les règles dans n’importe quelle application à l’aide d’autres appels. Les exacte API REST disponibles peut être affichés en cliquant sur la Loupe « API définition » dans le tableau de bord de règles.

![Texte de remplacement][10]

Voici un exemple de comment une peut utiliser cette API en langage C#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Notez que l’application de l’API règles ci-dessus a ses paramètres de sécurité définis sur « Public Anon ». Cela peut être défini à partir de l’API application à l’aide de-tous les paramètres -> Paramètres de l’Application -> niveau d’accès.

![Texte de remplacement][11]

## <a name="editing-vocabulary-and-policy"></a>Modifiez vocabulaire et stratégie
Un des principaux avantages de l’utilisation des règles d’entreprise est que les modifications apportées à la logique métier peuvent être transférées vers production beaucoup plus rapide. Toute modification apportée à vocabulaire et stratégies est appliquée immédiatement en production. Utilisateur doit simplement accéder à la définition de vocabulaire respectifs ou cette stratégie et apportez la modification afin que nous entrent en vigueur.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG

<properties
   pageTitle="Prise en main opérations gestion Suite Solution de sécurité et d’Audit | Microsoft Azure"
   description="Ce document vous aide à vous familiariser avec les opérations gestion Suite de sécurité et les fonctionnalités de solution d’Audit pour surveiller votre cloud hybride."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Prise en main opérations gestion Suite Solution de sécurité et d’Audit
Ce document vous aidera à commencer rapidement aux fonctionnalités de solution opérationnelle gestion de la sécurité Suite (OMS) et d’Audit par vous guider dans chaque option.

## <a name="what-is-oms"></a>Qu’est OMS ?
Suite de gestion des opérations Microsoft (OMS) est sur le nuage informatique solution de gestion qui vous permet de gérer et protéger vos localement et infrastructure en nuage. de Microsoft Pour plus d’informations sur OMS, consultez l’article [Opérations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Tableau de bord OMS sécurité et d’Audit

La solution OMS sécurité et d’Audit fournit une vue détaillée dans votre organisation état de la sécurité informatique avec les requêtes de recherche intégrée pour des problèmes remarquables qui nécessitent votre attention. Le tableau de bord de **sécurité et Audit** est l’écran d’accueil pour tous les éléments liés à la sécurité dans OMS. Il propose des claires sur l’état de sécurité de vos ordinateurs. Il inclut également la possibilité d’afficher tous les événements dans les dernières 24 heures, 7 jours, ou une autre image heure personnalisé. Pour accéder au tableau de bord de **sécurité et Audit** , procédez comme suit :

1. Dans le tableau de bord principale **Microsoft Operations Management Suite** cliquez sur vignette **paramètres** dans le volet gauche.
2. Dans la carte de **paramètres** , sous **Solutions** , cliquez sur option de **sécurité et Audit** .
3. Le tableau de bord de **sécurité et Audit** s’affichent :

    ![Tableau de bord OMS sécurité et d’Audit](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Si vous accédez à ce tableau de bord pour la première fois et vous n’avez pas appareils contrôlés par OMS, les mosaïques ne seront pas remplies avec des données obtenues de l’agent. Une fois que vous installez l’agent, cela peut prendre quelques instants pour remplir, par conséquent, ce que vous voyez initialement semble manquer des données qu’ils sont toujours télécharger dans le cloud.  Dans ce cas, il est normal certaines vignettes sans informations réels. Lire les [ordinateurs Windows de se connecter directement au OMS](https://technet.microsoft.com/library/mt484108.aspx) pour plus d’informations sur l’installation de l’agent OMS dans un système Windows et des [ordinateurs Linux se connecter à OMS](https://technet.microsoft.com/library/mt622052.aspx) pour plus d’informations sur la façon d’effectuer cette tâche dans un système Linux.

> [AZURE.NOTE] L’agent collecte les informations basées sur les événements actuels sont activées, par exemple le nom de l’ordinateur, IP adresse et nom d’utilisateur. Cependant aucun document/fichiers, de nom de base de données ou de données privées ne sont regroupées.   

Solutions sont un ensemble de règles d’acquisition logique, de visualisation et de données qui répondent aux défis clés des clients. Sécurité et Audit est une solution, d’autres personnes peuvent être ajoutés séparément. Consultez l’article [Ajouter solutions](https://technet.microsoft.com/library/mt674635.aspx) pour plus d’informations sur l’ajout d’une nouvelle solution.

Le tableau de bord OMS sécurité et d’Audit est organisée en quatre catégories principales :

- **Domaines de sécurité**: dans cette zone, vous pourrez plus découvrir les enregistrements de sécurité au fil du temps, accéder aux programmes malveillants évaluation, mettre à jour d’évaluation, la sécurité du réseau, identité et accéder aux informations, ordinateurs avec les événements de sécurité et accéder rapidement au tableau de bord centre de sécurité Azure.
- **Problèmes remarquables**: cette option vous permettra d’identifier rapidement le nombre de problèmes actifs et la gravité de ces problèmes.
- **Détection (Preview)**: vous permet d’identifier des modèles attaque par visualiser les alertes de sécurité qu’ils se produisent par rapport à vos ressources.
- **Informations sur les menaces**: vous pouvez identifier des modèles d’attaques en visualisant le nombre total de serveurs avec le trafic IP malveillant sortant, du type de menace malveillants et une carte montrant provenance ces adresses IP. 
- **Requêtes de sécurité communes**: cette option vous fournit la liste des requêtes de sécurité plus courantes que vous pouvez utiliser pour surveiller votre environnement. Lorsque vous cliquez sur l’une de ces requêtes, il ouvre la carte de **recherche** avec les résultats de cette requête.

> [AZURE.NOTE] Pour plus d’informations sur la façon dont OMS conserve vos données sécurisées, lisez que comment OMS protège vos données.

## <a name="security-domains"></a>Domaines de sécurité

Lorsque vous analysez des ressources, il est important pouvoir accéder rapidement à l’état actuel de votre environnement. Il est toutefois également importante doivent pouvoir effectuer le suivi des événements précédent qui s’est produite dans le passé qui peut entraîner une meilleure compréhension de quoi de neuf dans votre environnement à un moment donné dans le temps. 

> [AZURE.NOTE] conservation des données est selon la OMS plan de tarification. Pour plus d’informations, visitez le [Microsoft Operations Management Suite](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) tarifs page.

Scénarios d’enquête réponse et post-mortem incidents lui facilitera directement à partir des résultats disponibles dans la vignette **d’Enregistrements de sécurité au fil du temps** .

![Enregistrements de sécurité au fil du temps](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Lorsque vous cliquez sur cette vignette, la carte de **recherche** s’ouvre, affichant les résultats d’une requête pour **Les événements de sécurité** (Type = SecurityEvents) avec des données basées sur les sept derniers jours, comme indiqué ci-dessous :

![Enregistrements de sécurité au fil du temps](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

Le résultat de recherche est divisé en deux volets : le volet gauche vous donne une répartition du nombre d’événements de sécurité qui ont été trouvés, les ordinateurs dans lesquelles ces événements détectés, le nombre de comptes qui ont été découvertes dans ces ordinateurs et les types d’activités. Le volet droit vous fournit les résultats total et un affichage chronologique des événements de sécurité avec une activité nom et événements de l’ordinateur. Vous pouvez également cliquer sur **Afficher plus** pour afficher plus d’informations sur cet événement, tels que les données d’événement, l’ID de l’événement et la source d’événements.

> [AZURE.NOTE] Pour plus d’informations sur la requête de recherche OMS, lisez [OMS recherchez la référence](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Évaluation contre les logiciels malveillants

Cette option vous permet d’identifier rapidement des ordinateurs avec une protection insuffisante et ordinateurs qui ont été compromis par un logiciel malveillant. Lecture état évaluation des programmes malveillants et menaces détectées sur les serveurs analysés et puis les données sont envoyées au service OMS dans le cloud pour traitement. Serveur avec détecté menaces et serveurs avec une protection insuffisante sont présentées dans le programme malveillant évaluation tableau de bord, qui est accessible après avoir cliqué sur dans la vignette **Évaluation contre les logiciels malveillants** . 

![évaluation des programmes malveillants](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Comme n’importe quel autre dynamique disponibles dans le tableau de bord OMS, lorsque vous cliquez dessus, la carte de **recherche** s’ouvre avec le résultat de la requête. Pour cette option, si vous cliquez sur l’option **Pas Reporting** sous **État de Protection**, vous devrez le résultat de la requête qui affiche cette entrée unique qui contient le nom d’ordinateur et son rang, comme indiqué ci-dessous :

![résultat de la recherche](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *rang* est une note donnant à refléter l’état de la protection (activé, désactivé, mis à jour, etc.) et menaces qui se trouvent. HAVING comme un nombre contribue à rendre les agrégations.

Si vous cliquez dans la zone Nom de l’ordinateur, vous devez l’affichage chronologique de l’état de protection pour cet ordinateur. Ceci est très utile pour les scénarios dans laquelle vous devez comprendre si le contre les logiciels malveillants a été installé une seule fois et à un moment donné qu'il a été supprimé.   

### <a name="update-assessment"></a>Évaluation de mise à jour 

Cette option vous permet de déterminer rapidement l’exposition globale à des problèmes potentiels de sécurité et si ou comment critique ces mises à jour sont pour votre environnement. OMS solution de sécurité et d’Audit uniquement fournissent la visualisation de ces mises à jour, les données réelles proviennent de [Solutions de mises à jour système](https://technet.microsoft.com/library/mt484096.aspx), ce qui correspond à un autre module dans OMS. Voici un exemple les mises à jour :

![mises à jour système](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Pour plus d’informations sur la solution mises à jour, lisez [mettre à jour les serveurs avec la solution mises à jour système](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Identité et accès

Identité doit être le plan de contrôle pour votre entreprise, protéger votre identité doit être votre première priorité. Bien que par le passé ont été périmètre autour des organisations et ces périmètre était parmi les limites de défensifs principales, aujourd'hui avec davantage de données et autres applications passage au nuage l’identité devient le nouveau périmètre. 

> [AZURE.NOTE] actuellement les données repose uniquement sur des données de connexion événements de sécurité (événement ID 4624) dans les futures connexions Office 365 et données Azure AD seront également incluses.

En analysant vos activités identité, vous pourrez prendre des mesures proactives avant un incident prend réactives actions pour arrêter une tentative d’attaque ou un emplacement. Le tableau de bord **d’identité et Access** vous donne un aperçu de votre état d’identité, y compris le nombre d’échecs pour vous connecter, le compte d’utilisateur qui ont été utilisés au cours de ces tentatives, les comptes qui ont été verrouillés, les comptes avec modifier ou réinitialiser votre mot de passe et actuellement le nombre de comptes qui sont enregistrés dans. 

Lorsque vous cliquez dans la vignette **d’identité et accès** , vous verrez le tableau de bord suivant :

![identité et accès](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

Les informations disponibles dans ce tableau de bord peuvent immédiatement vous aider à identifier une activité suspecte potentielle. Par exemple, il existe 338 tentatives connecter en tant **qu’administrateur** et 100 % de ces tentatives a échoué. Cela peut être dû une attaque par rapport à ce compte. Si vous cliquez sur ce compte, vous obtenez plus d’informations qui peuvent vous aider à déterminer la ressource cible pour cette attaque potentielle :

![résultats de la recherche](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

Le rapport détaillé fournit des informations importantes sur cet événement, y compris : l’ordinateur cible, le type d’ouverture de session (dans ce cas de connexion réseau), l’activité (dans ce cas casse 4625) et une barre de planning complet de chaque tentative. 

### <a name="computers"></a>Ordinateurs

Cette vignette peut servir à accéder à tous les ordinateurs qui ont activement des événements de sécurité. Lorsque vous cliquez dans cette vignette vous verrez la liste des ordinateurs avec des événements de sécurité et le nombre d’événements sur chaque ordinateur :

![Ordinateurs](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Vous pouvez continuer votre enquête en cliquant sur chaque ordinateur et passez en revue les événements de sécurité qui ont été signalés.

### <a name="azure-security-center"></a>Centre de sécurité Azure

Cette vignette est en fait un raccourci permettant d’accéder à tableau de bord centre de sécurité Azure. Pour plus d’informations sur cette solution, lisez [mise en route d’Azure le centre de sécurité](../security-center/security-center-get-started.md) .

## <a name="notable-issues"></a>Problèmes remarquables

L’objectif principal de ce groupe d’options consiste à fournir une vue rapide des problèmes que vous avez dans votre environnement, en les classer dans critique, avertissement et information. La vignette du type de problème actif il s’agit d’une visualisation de ces problèmes, mais il ne vous permet pas pour Explorer plus d’informations sur les, pour que vous devez utiliser la partie inférieure de cette vignette qui comporte le nom du problème (nom), le nombre d’objets avait cela se produire (nombre) et combien il est important (gravité).

![Problèmes remarquables](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Vous pouvez voir que ces problèmes ont été déjà traités dans différentes parties du groupe **Domaines de sécurité** , qui renforce l’objectif de cet affichage : visualiser des questions les plus importantes dans votre environnement à partir d’un emplacement unique.

## <a name="detections-preview"></a>Détection (Preview)

L’objectif principal de cette option consiste à autoriser informatiques d’identifier rapidement des menaces potentielles pour leur environnement via et la gravité de cette menace.

![Menace Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Cette option peut également être utilisée pendant une investigation de réponse aux incidents pour effectuer l’évaluation et obtenir plus d’informations sur l’attaque.

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation OMS Incident de réponse, regardez [comment tirer parti le centre de sécurité Azure & Microsoft Operations Management Suite pour une réponse aux incidents](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).

## <a name="threat-intelligence"></a>Informations sur les menaces

La nouvelle section de menace intelligence de la solution de sécurité et d’Audit visualise les modèles attaque possible de différentes manières : le nombre total de serveurs avec le trafic IP malveillant sortant, du type de menace malveillants et une carte montrant provenance ces adresses IP. Vous pouvez interagir avec la carte et cliquez sur les adresses IP pour plus d’informations.

Pinos jaunes sur la carte indiquent le trafic entrant à partir des adresses IP malveillant. Il n’est pas dialogue pour les serveurs qui sont exposés à internet pour voir le trafic malveillant entrant, mais nous vous recommandons de consulter ces tentatives pour vous assurer qu’aucune d’elles a réussi. Ces indicateurs sont basés sur les journaux IIS, WireData et le pare-feu Windows ouvre une session.  

![Menace Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Requêtes de sécurité courantes

La liste des requêtes de sécurité communes disponibles peut être utile pour accéder rapidement aux informations sur la ressource et de le personnaliser en fonction des besoins de votre environnement. Ces requêtes courants sont :

- Toutes les activités de sécurité
- Activités de sécurité de l’ordinateur « computer01.contoso.com » (remplacer par votre propre nom de l’ordinateur)
- Activités de sécurité de l’ordinateur « computer01.contoso.com » compte « Administrateur » (remplacer par vos propres noms de compte et de l’ordinateur)
- Ouvrez une session sur l’activité par ordinateur
- Comptes qui s’est arrêté de Microsoft contre les logiciels malveillants sur n’importe quel ordinateur
- Ordinateurs où le processus de contre les logiciels malveillants Microsoft a été terminé
- Ordinateurs où « hash.exe » a été exécutée (remplacer par nom de processus différent)
- Tous les noms de processus qui ont été exécutées
- Ouvrez une session sur l’activité par compte
- Comptes connectés à distance sur l’ordinateur « computer01.contoso.com » (remplacer par votre propre nom de l’ordinateur)

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez abordé solution OMS sécurité et d’Audit. Pour en savoir plus sur la sécurité OMS, consultez les articles suivants :

- [Vue d’ensemble de la gestion des Suite (OMS) opérations](operations-management-suite-overview.md)
- [Surveiller et répondre aux alertes de sécurité dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)
- [Suivi des ressources dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-monitoring-resources.md)

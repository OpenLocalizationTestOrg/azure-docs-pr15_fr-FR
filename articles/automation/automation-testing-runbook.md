<properties 
    pageTitle="Tester une procédure opérationnelle dans Azure automatisation | Microsoft Azure"
    description="Avant de publier une procédure opérationnelle dans Azure Automation, vous pouvez le tester pour vous assurer que cela fonctionne comme prévu.  Cet article décrit comment tester une procédure opérationnelle et afficher sa sortie."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="testing-a-runbook-in-azure-automation"></a>Tester une procédure opérationnelle dans Azure Automation
Lorsque vous testez une procédure opérationnelle, l’exécution de la [version brouillon](automation-creating-importing-runbook.md#publishing-a-runbook) et les actions qu’il exécute sont terminées. Aucun historique de travail n’est créé, mais les flux de [sortie](automation-runbook-output-and-messages.md#output-stream) et [d’avertissement et erreur](automation-runbook-output-and-messages.md#message-streams) sont affichent dans le Test de volet de sortie. Messages dans le [flux de commentaires](automation-runbook-output-and-messages.md#message-streams) sont affichés dans le volet de sortie uniquement si la [variable $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) est définie sur Continue.

Même si la version brouillon est exécutée, le runbook exécute le flux de travail en règle générale, toujours et effectue une action sur des ressources de l’environnement. Pour cette raison, vous devez tester uniquement procédures opérationnelles en ressources hors production.

La procédure pour chaque [type de runbook](automation-runbook-types.md) de test est identique, et il n’existe aucune différence en phase de test entre l’éditeur de texte et de l’éditeur graphique dans le portail Azure.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Pour tester une procédure opérationnelle dans le portail Azure

Vous pouvez travailler avec n’importe quel [type de runbook](automation-runbook-types.md) dans le portail Azure.

1. Ouvrez le brouillon de la runbook dans [l’éditeur de texte](automation-editing-a-runbook.md#Portal) ou [l’éditeur de graphique](automation-graphical-authoring-intro.md).
2. Cliquez sur le bouton **Test** pour ouvrir la carte de Test.
3. Si la runbook comporte des paramètres, ils sont répertoriés dans le volet gauche de l’endroit où vous pouvez fournir des valeurs à utiliser pour le test.
4. Si vous souhaitez exécuter le test dans un [Environnement hybride Runbook concerné](automation-hybrid-runbook-worker.md), puis modifier **Les paramètres d’exécution** collaborateur **Hybride** et sélectionnez le nom du groupe cible.  Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le cloud.
5. Cliquez sur le bouton **Démarrer** pour démarrer le test.
6. Si la runbook est [Flux de travail de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou de [graphique](automation-runbook-types.md#graphical-runbooks), vous pouvez arrêter ou suspendre est en cours de test avec les boutons situé sous le volet de sortie. Lorsque vous interrompez le runbook, il termine l’activité actuelle avant interrompus. Une fois que le runbook est suspendue, vous pouvez arrêter ou redémarrez-le.
7. Vérifiez que le résultat de la procédure opérationnelle dans le volet résultat.


## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment créer ou importer un runbook, voir [Création ou en important un runbook dans Azure Automation](automation-creating-importing-runbook.md)
- Pour plus d’informations sur la création de graphiques, voir [la création de graphique dans Azure Automation](automation-graphical-authoring-intro.md)
- Pour commencer à utiliser les procédures opérationnelles de flux de travail de PowerShell, voir [Mon premier runbook de flux de travail PowerShell](automation-first-runbook-textual.md)
- Pour en savoir plus sur la configuration runboks pour renvoyer les messages d’état et les erreurs, y compris les pratiques recommandées, voir [Runbook sortie et messages dans Azure Automation](automation-runbook-output-and-messages.md)
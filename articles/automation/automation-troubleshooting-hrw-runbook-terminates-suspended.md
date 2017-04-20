<properties
   pageTitle="Hybride Runbook collaborateur : Un travail runbook se termine avec l’état suspendu | Microsoft Azure"
   description="Erreur de fin de la tâche symptômes causes et solutions pour hybride Runbook collaborateur."
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
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Hybride Runbook collaborateur : Un travail runbook se termine avec l’état suspendu

## <a name="summary"></a>Résumé

Votre runbook est suspendue peu de temps après vous tentez d’exécuter trois fois. Il existe des conditions qui peuvent interrompre la runbook d’effectuer cette opération, le message d’erreur lié n’inclut pas les autres informations indiquant la raison pour laquelle. Cet article fournit des étapes de dépannage pour les problèmes de l’exécution runbook hybride Runbook concerné.

Si votre problème Azure n’est pas traité dans cet article, consultez les forums Azure sur [MSDN et le débordement de pile](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou [ @AzureSupport sur Twitter](https://twitter.com/AzureSupport). En outre, vous pouvez créer une demande de support Azure en sélectionnant **obtenir une assistance technique** sur le site [Azure prend en charge](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Manifestation

L’exécution du Runbook échoue, l’erreur renvoyée est, « l’action de travail « Activer » Impossible d’exécuter, car le processus d’arrêt inattendu. L’action de travail a été lancée 3 fois. »


## <a name="cause"></a>Cause

Il existe plusieurs causes possibles pour l’erreur : 

  1. Le collaborateur hybride est derrière un proxy ou un pare-feu
  2. L’ordinateur que collaborateur hybride est en cours d’exécution est inférieur à la configuration matérielle [Configuration requise](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) 
  3. Les procédures opérationnelles ne peut pas vous authentifier avec les ressources locales


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Cause 1 : Hybride Runbook collaborateur est derrière un proxy ou un pare-feu

L’ordinateur que collaborateur Runbook hybride est en cours d’exécution est derrière un pare-feu ou serveur proxy et accès réseau sortant peut être non autorisé ou configuré correctement.

### <a name="solution"></a>Solution

Vérifiez que l’ordinateur a accès sortantes à *. cloudapp.net sur les ports 443, 9354 et 30000 30199. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Cause 2 : Ordinateur comporte moins de configuration minimale requise

Ordinateurs qui exécutent le travail Runbook hybride doivent satisfaire la configuration minimale requise avant désigner pour héberger cette fonctionnalité. Dans le cas contraire, en fonction de l’utilisation des ressources d’autres processus en arrière-plan et les conflits causés par procédures opérationnelles lors de l’exécution, l’ordinateur sera soient surutilisées et provoque des retards de travail runbook ou dépassements de délai. 

### <a name="solution"></a>Solution 

Tout d’abord, vérifiez l’ordinateur désigné pour exécuter la fonctionnalité hybride Runbook collaborateur répond à la configuration minimale requise.  Dans ce cas, surveiller l’utilisation du processeur et la mémoire pour identifier toute corrélation entre les performances du processus de travail de Runbook hybride et Windows.  S’il existe de mémoire ou de pression du processeur, cela peut indiquer la nécessité de mettre à niveau ou ajoutez des processeurs supplémentaires ou augmentez la mémoire pour corriger la critique ressource et résoudre l’erreur. Vous pouvez également sélectionner une ressource cluster différent prenant en charge la configuration minimale requise et les proportions lorsque la charge de travail indique une augmentation est nécessaire.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Cause 3 : Procédures opérationnelles ne peut pas vous authentifier avec les ressources locales

### <a name="solution"></a>Solution

Consultez le journal des événements **Microsoft SMA** pour un événement correspondant avec une description *Win32 processus quitté avec code [4294967295]*.  La cause de cette erreur est que vous n’avez pas configuré l’authentification dans les procédures opérationnelles ou spécifié les informations d’identification exécuter en tant que pour le groupe de travail hybride.  Veuillez consulter [Runbook autorisations](automation-hybrid-runbook-worker.md#runbook-permissions) pour confirmer que vous avez correctement configuré l’authentification pour les procédures opérationnelles.  


 


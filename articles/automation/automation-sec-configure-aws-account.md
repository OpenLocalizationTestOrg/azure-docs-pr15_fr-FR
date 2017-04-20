<properties
   pageTitle="Configurer l’authentification avec les Services Web Amazon | Microsoft Azure"
   description="Cet article décrit comment créer et valider les informations d’identification AWS pour procédures opérationnelles dans Automation Azure Gestion des ressources AWS."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="authentification AWS, configurez aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Authentifier procédures opérationnelles avec les Services Web Amazon
Automatisation des tâches courantes avec les ressources dans Amazon Web Services (AWS) peut être effectuée avec Automation procédures opérationnelles dans Azure.  Vous pouvez automatiser de nombreuses tâches de AWS à l’aide de procédures opérationnelles Automation comme vous le pouvez avec ressources dans Azure.  Tout ce qui est requis sont deux opérations :

* Un abonnement AWS et un jeu d’informations d’identification.  Spécifiquement votre touche d’accès rapide AWS et clé secrète.  Pour plus d’informations, consultez l’article [à l’aide d’informations d’identification AWS](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Un abonnement Azure et un compte de Automation.  Pour plus d’informations sur la configuration d’un compte Azure automatisation, veuillez consulter l’article [Configurer l’exécuter en tant que compte Azure](../automation/automation-sec-configure-azure-runas-account.md).  

Pour vous authentifier avec AWS, vous devez spécifier un ensemble d’informations d’identification AWS s’authentifier les procédures opérationnelles en cours d’exécution à partir d’Azure Automation. Si vous possédez déjà un compte Automation créé et que vous voulez utiliser pour vous authentifier avec AWS, vous pouvez suivre les étapes décrites dans la section suivante.  Si vous voulez dédiés un compte pour les ressources de procédures opérationnelles ciblé AWS, vous devez d’abord créer une nouvelle [Automation exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md) (ignorer l’option pour créer une entité de service) et puis suivez les étapes ci-dessous.

## <a name="configure-automation-account"></a>Configurer un compte Automation
Pour l’automatisation Azure communiquer avec AWS, vous devez d’abord récupérer vos informations d’identification AWS et les stocker en tant qu’actifs dans Azure Automation.  Effectuez les opérations suivantes présentées dans le document AWS [Gestion des touches d’accès rapide pour votre compte AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) pour créer une touche d’accès et copier le **Code de clé de Access** et une **Touche d’accès rapide Secret** (vous pouvez également télécharger votre fichier de clé pour stocker dans un emplacement sûr).

Une fois que vous avez créé et copié vos clés de sécurité AWS, vous devrez créer une ressource d’informations d’identification avec un compte Azure Automation et en toute sécurité les stocker les référencer avec les procédures opérationnelles.  Suivez les étapes décrites dans la section **pour créer une nouvelle information d’identification** dans l’article [actifs d’informations d’identification dans Azure Automation](../automation/automation-certificates.md/###To create a new credential with the Azure portal) et entrez les informations suivantes :

1. Dans la zone **nom** , entrez **AWScred** ou une valeur appropriée suivant vos normes d’attribution de noms.  
2. Dans la zone **nom d’utilisateur** , tapez votre **ID d’accès** et votre **Code Secret touche d’accès** dans la zone **mot de passe** et **Confirmer le mot de passe** .   

## <a name="next-steps"></a>Étapes suivantes

- L’article de la solution [automatisation déploiement d’un ordinateur virtuel dans les Services Web Amazon](../automation/automation-scenario-aws-deployment.md) pour apprendre à créer des procédures opérationnelles pour automatiser des tâches dans AWS Reivew.

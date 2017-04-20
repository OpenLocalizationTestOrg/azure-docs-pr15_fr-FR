<properties
   pageTitle="Test de stylet | Microsoft Azure"
   description="L’article fournit une vue d’ensemble de la pénétration (pentest) processus de test et comment effectuer pentest par rapport à vos applications en cours d’exécution dans Azure infrastructure."
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
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Test de stylet

Un des avantages sur l’utilisation de Microsoft Azure de test des applications et de déploiement est que vous n’avez pas besoin d’assembler une infrastructure en local pour développer, tester et déployer vos applications. Toute l’infrastructure est pris en charge par les services de plateforme Microsoft Azure. Vous n’avez pas à vous soucier gèrent, acquisition et « montage en rack et empilage » votre propre matériel local.

C’est très pratique, mais vous avez besoin pour vous assurer que vous effectuez votre sécurité normale vérification au préalable. Parmi les choses que vous devez faire est pénétration tester les applications que vous déployez dans Azure.

Vous connaissez peut-être déjà que Microsoft effectue [des tests de pénétration de l’environnement Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Cela nous permet d’améliorer nos plateforme et guides de nos actions en termes d’amélioration des contrôles de sécurité, présentation de nouveaux contrôles de sécurité et l’amélioration de nos processus de sécurité.

Nous ne stylet tester votre application pour vous, mais nous comprendre que vous voulez et besoin pour effectuer des tests sur vos propres applications de stylet. Qui est une bonne chose, car vous pouvez améliorer la sécurité de vos applications, vous aidez à renforcer le réseau Azure entière.

Lorsque vous stylet Testez vos applications, il peut ressembler à une attaque pour nous. Nous [surveiller en permanence](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) pour attaquer des motifs et démarrer un processus de réponse aux incidents si nous avons besoin de. Il ne vous aider et vous ne plus facilement nous nous déclencher une réponse à un incident en raison de votre propre échéance stylet diligence test.

Que faire ?

Lorsque vous êtes prêt à stylet tester vos applications hébergé Azure, vous devez nous faire connaître. Une fois que nous savez que vous allez effectuer des tests spécifiques, nous ne par inadvertance arrêter (par exemple, bloquer l’adresse IP que vous testez à partir de), dans la mesure où vos tests sont conformes à la Azure stylet test termes et conditions.
Vous pouvez effectuer des tests standards sont les suivantes :

- Tests sur vos points de terminaison pour révéler les [Ouvrir Web Application sécurité projet (OWASP avoir) principales 10 vulnérabilités](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Les tests aléatoires](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) vos points de terminaison
- [Analyse des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

Un seul type de test que vous ne pouvez pas effectuer est tout type d’attaque de [Refus de Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Cela inclut le lancement d’une attaque DoS elle-même ou effectuer des tests connexes qui peuvent déterminer, montrer ou simuler n’importe quel type d’attaque DoS.

Êtes-vous prêt à commencer à utiliser stylet Testez vos applications hébergées dans Microsoft Azure ? Si c’est le cas, puis accédez à [Vue d’ensemble des tests de pénétration](https://security-forms.azure.com/penetration-testing/terms) de page (et cliquez sur le bouton Créer un test demander en bas de la page. Vous trouverez également plus d’informations sur le stylet test des conditions et des liens utiles sur la façon dont vous pouvez le signaler défauts de sécurité liées à Azure ou tout autre service de Microsoft.

<properties
    pageTitle="Meilleures pratiques pour le Service application Azure"
    description="Découvrez les meilleures pratiques et résolution des problèmes de Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Meilleures pratiques pour le Service application Azure

Cet article résume les meilleures pratiques pour utiliser le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Emplacement partagé
Lorsque les ressources Azure vous rédigez une solution comme une application web et une base de données se trouvent dans différentes régions les effets pouvant sont les suivants :

*  Latence accrue de communication entre les ressources
*  Frais monétaires pour les données sortantes transférer entre-région comme indiqué dans la [page tarification Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Colocation dans la même région est recommandée pour les ressources Azure vous rédigez une solution comme une application web et un compte de base de données ou de stockage utilisé pour stocker le contenu ou les données. Lorsque vous créez des ressources, que vous devez vous assurer qu’ils sont dans la même région Azure, sauf si vous avez spécifiques de l’entreprise ou concevez raison ne doit ne pas être. Vous pouvez déplacer une application de Service d’application à la même région en tant que votre base de données grâce à l' [application Service clonant fonctionnalité](app-service-web-app-cloning-portal.md) actuellement disponibles pour les applications de Plan de Service d’application Premium.   

## <a name="memoryresources"></a>Lorsque les applications consomment plus de mémoire que prévu
Lorsque vous remarquez une application consomme plus de mémoire que prévu comme indiqué par le biais de surveillance ou recommandations service envisagez la [fonctionnalité correcteur application Service automatique](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Une des options pour la fonctionnalité Correcteur automatique effectue des actions personnalisées basées sur un seuil de mémoire. Actions s’étalent spectre des notifications par courrier électronique à des enquêtes via le vidage de la mémoire pour atténuation sur place en recyclant le processus de travail. Correcteur automatique peut être configuré via web.config et une interface utilisateur conviviale conformément à ce billet de blog pour l' [Extension du Site application Service prise en charge](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Lorsque les applications consomment de l’UC plus que prévu
Lorsque vous remarquez qu'une application consomme plus d’UC que prévu ou des expériences d’UC répétés autant de fois indiqué par le biais de surveillance ou recommandations service envisagez l’évolution verticale ou horizontale le plan de services d’application. Si votre application est menés, l’évolution verticale est la seule option, tandis que si votre application est hors sans état, mise à l’échelle vous offre davantage de flexibilité et plus susceptible de vous échelle. 

Pour plus d’informations sur les applications de « sans état » et « menés », vous pouvez visionner cette vidéo : [planification d’une Application à plusieurs niveaux Scalable de bout en bout sur Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Pour plus d’informations sur les options de mise à l’échelle et autoscaling application Service lire : [échelle une application Web dans le Service d’application Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Lorsque saturation de ressources de support
Une raison courante pour les connexions TCP sortantes épuiser consiste à utiliser des bibliothèques de clients qui ne sont pas implémentées réutiliser les connexions TCP, ou dans le cas d’un protocole de niveau supérieur tels que HTTP - persistante ne pas être promue. Consultez la documentation pour chacun des bibliothèques référencées par les applications de votre Plan de Service de l’application pour vous assurer qu’ils sont configurés ou accessible dans votre code pour une réutilisation efficace de connexions sortantes. Suivez également les recommandations bibliothèque documentation création approprié et release ou de nettoyage afin d’éviter les connexions de perd. Lors de ces enquêtes bibliothèques client impact sur l’avancement peut être réduits en faisant évoluer à plusieurs instances.  

## <a name="appbackup"></a>Lorsque votre application de sauvegarde démarre ignore
Les deux raisons les plus courantes pourquoi échoue sauvegarde d’application est : paramètres de stockage non valide et la configuration de base de données non valides. En général, ces échecs sont effectuées lorsqu’il existe des modifications apportées aux ressources de stockage ou base de données ou des modifications pour savoir comment accéder à ces ressources (par exemple, les informations d’identification mis à jour pour la base de données sélectionnée dans les paramètres de sauvegarde). En règle générale, des sauvegardes s’exécutent sur un planning et nécessitent un accès à l’espace de stockage (pour sortir sauvegardés des fichiers) et les bases de données (pour copier et lecture du contenu à inclure dans la sauvegarde). Le résultat de ne parvient pas à accéder à une de ces ressources peuvent être des échecs de sauvegarde cohérentes. 

Lorsque des échecs de sauvegarde se produisent, veuillez consulter résultats les plus récents pour comprendre le type de panne qui se passe. Dans le cas d’échecs d’accès de stockage, veuillez consulter et mettre à jour les paramètres de stockage utilisés dans la configuration de sauvegarde. Dans le cas d’échecs d’accès de base de données, passez en revue et mettre à jour vos chaînes de connexion dans le cadre de paramètres de l’application ; Continuez à mettre à jour votre configuration de la sauvegarde pour inclure correctement les bases de données nécessaires. Pour plus d’informations sur la sauvegarde de l’application, consultez la documentation de [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md) .

## <a name="nodejs"></a>Lorsque les nouvelles applications Node.js sont déployées sur Azure Application Service
Azure configuration par défaut de Service d’application pour les applications Node.js est destinée à mieux répondre aux besoins des applications courantes. Si la configuration de votre application Node.js susceptible de bénéficier des réglages personnalisés pour améliorer les performances ou d’optimiser l’utilisation des ressources pour les ressources processeur/mémoire/réseau, vous pourriez consulter notre meilleures pratiques et les étapes de dépannage. Cet article de la documentation décrit les paramètres iisnode, vous devrez peut-être configurer votre application Node.js, décrit les différents scénarios ou problèmes que votre application peut être en vis-à-vis et montre comment résoudre ces problèmes : [meilleures pratiques et guide de dépannage pour les applications de nœud Service d’application Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   



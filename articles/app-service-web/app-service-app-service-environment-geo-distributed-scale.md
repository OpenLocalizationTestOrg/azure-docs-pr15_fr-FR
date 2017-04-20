<properties 
    pageTitle="Geo distribué échelle aux environnements de Service d’application" 
    description="Découvrez comment mettre à l’échelle horizontalement des applications à l’aide de geo Student avec le trafic responsable et environnements de Service d’application." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Geo distribué échelle aux environnements de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Scénarios d’application qui nécessitent très haute échelle peuvent dépasser la capacité des ressources cluster disponible pour un seul déploiement d’une application.  Vote applications, événements sportifs et loisirs télévisée événements sont tous les exemples de scénarios qui requièrent échelle très haute. Respecter les contraintes de grande échelle en faisant évoluer horizontalement applications, avec plusieurs déploiements d’application effectuées dans une région unique, ainsi qu’entre les régions, pour répondre aux exigences de charge extrême.

Environnements de Service d’application sont une plate-forme idéale pour horizontal mise à l’échelle.  Une fois une application Service environnement configuration a été sélectionnée qui peut prendre en charge un taux demande connus, les développeurs peuvent déployer environnements de Service d’application supplémentaires de manière « découpage » pour atteindre une capacité de charge pointe souhaité.

Supposons par exemple une application en cours d’exécution sur une configuration de l’application Service environnement a été testée pour gérer les demandes de 20K par seconde.  Si la capacité de charge pointe souhaitée est 100 Ko RPS, environnements de Service d’application cinq (5) peut être créés et configurés pour vous assurer que l’application peut gérer la charge projetée maximale.

Dans la mesure où les clients access en général des applications à l’aide d’un domaine personnalisé (ou vanity), les développeurs ont besoin d’un moyen de distribuer des demandes d’application sur toutes les instances de l’environnement de Service d’application.  Un excellent moyen d’y parvenir consiste à résoudre le domaine personnalisé à l’aide d’un [profil Azure le trafic Manager][AzureTrafficManagerProfile].  Le profil de trafic Manager peut être configuré pour pointez sur tous les environnements de Service d’application individuels.  Gestionnaire de trafic gérera automatiquement distribution clients sur tous les environnements de Service d’application basée sur la paramètres dans le profil Manager le trafic d’équilibrage de charge.  Cette approche fonctionne indépendamment si toutes les environnements de Service d’application sont déployées dans une seule région Azure ou déployés dans le monde dans plusieurs régions Azure.

En outre, étant donné que les clients accéder aux applications via le domaine personnel, clients ne savent pas du nombre d’environnements de Service d’application une application en cours d’exécution.  Par conséquent les développeurs peuvent rapidement et facilement ajouter et supprimer, environnements de Service d’application en fonction de charge le trafic observé.

Le diagramme conceptuel ci-dessous décrit une application horizontalement répartie sur trois environnements de Service d’application dans une région unique.

![Architecture conceptuelle][ConceptualArchitecture] 

Le reste de cette rubrique décrit les étapes à suivre avec la configuration d’une topologie distribuée pour l’application d’exemple à l’aide de plusieurs environnements de Service d’application.

## <a name="planning-the-topology"></a>Planification de la topologie ##
Avant de créer un encombrement application distribué, il est utile de quelques éléments d’informations à l’avance.

- **Domaine personnalisé pour l’application :**  Quel est le nom de domaine personnalisé que les utilisateurs utiliseront pour accéder à l’application ?  Pour l’application exemple le nom de domaine personnalisé est *www.scalableasedemo.com*
- **Domaine le trafic Manager :**  Un nom de domaine doit être choisi lors de la création d’un [profil Azure le trafic Manager][AzureTrafficManagerProfile].  Ce nom est associé le suffixe *trafficmanager.net* à enregistrer une entrée de domaine est gérée par le Gestionnaire de trafic.  Pour l’application exemple, le nom choisi est *scalable ase-démo*.  Par conséquent, le nom de domaine complet qui est géré par le Gestionnaire de trafic est *scalable ase-demo.trafficmanager.net*.
- **Stratégie pour suivre l’évolution de l’encombrement de l’application :**  L’encombrement de l’application est réparti sur plusieurs environnements de Service d’application d’une région spécifique ?  Plusieurs régions ?  Une mélange-correspondance des deux approches ?  La décision doit être basée sur les attentes d’origine le trafic du client, ainsi que le degré peut s’adapter le reste d’une application prenant en charge l’infrastructure principale.  Par exemple, avec une application sans état 100 %, une application peut être très adaptée à l’aide d’une combinaison de plusieurs environnements de Service d’application par région Azure, multipliée par les environnements de Service d’application déployée dans plusieurs régions Azure.  Avec 15 + publiques Azure régions disponibles, clients peuvent véritablement générer un encombrement monde hyper-échelle application.  Pour l’application exemple utilisée pour cet article, trois environnements de Service d’application ont été créés dans une seule région Azure (Sud États-Unis).
- **Convention d’affectation pour les environnements de Service d’application :**  Chaque environnement de Service d’application nécessite un nom unique.  Au-delà d’un ou deux environnements de Service d’application, il est utile d’avoir une convention d’appellation pour aider à identifier chaque environnement de Service d’application.  Pour l’application exemple une convention d’appellation simple a été utilisée.  Les noms des trois environnements de Service d’application sont *fe1ase*, *fe2ase*et *fe3ase*.
- **Convention d’affectation pour les applications :**  Dans la mesure où plusieurs instances de l’application seront déployées, un nom est nécessaire pour chaque instance de l’application déployée.  Une fonctionnalité peu connue mais très pratique d’environnements de Service d’application est que le même nom de l’application peut être utilisé dans différents environnements de Service d’application.  Dans la mesure où chaque environnement de Service d’application comporte un suffixe de domaine unique, les développeurs peuvent choisir réutiliser exactement le même nom d’application dans chaque environnement.  Par exemple un développeur peut avoir applications nommées comme suit : *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc..  Pour l’application exemple bien que chaque instance de l’application est également un nom unique.  Les noms d’instance de l’application utilisés sont *webfrontend1*, *webfrontend2*et *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>La configuration du profil le trafic Manager ##
Une fois que plusieurs instances d’une application sont déployés sur plusieurs environnements de Service d’application, les instances d’application individuels peuvent être enregistrés avec le Gestionnaire de trafic.  Pour l’application d’exemple Gestionnaire de trafic profil est nécessaire pour *scalable ase-demo.trafficmanager.net* qui permet d’acheminer des clients à toutes les instances de l’application déployée suivants :

- **webfrontend1.fe1ase.p.azurewebsites.net :**  Une instance de l’application exemple déployée sur le premier environnement de Service d’application.
- **webfrontend2.fe2ase.p.azurewebsites.net :**  Une instance de l’application exemple déployée sur le deuxième environnement de Service d’application.
- **webfrontend3.fe3ase.p.azurewebsites.net :**  Une instance de l’application exemple déployée dans l’environnement de Service d’application tiers.

Le moyen le plus simple d’enregistrer plusieurs points de terminaison de Service d’application Azure, tout en cours d’exécution dans la **même** région Azure, est avec le Powershell [prend en charge le trafic Azure ressource Gestionnaire][ARMTrafficManager].  

La première étape consiste à créer un profil Azure le trafic responsable.  Le code suivant montre comment le profil a été créé pour l’application exemple :

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Notez comment le paramètre *RelativeDnsName* a été défini au *format SVG ase-démo*.  Voici comment le nom de domaine *scalable ase-demo.trafficmanager.net* est créé et associé à un profil Manager le trafic.

Le paramètre *TrafficRoutingMethod* définit la stratégie le trafic Manager utilisera pour déterminer comment répartir client charge sur tous les points de terminaison disponibles d’équilibrage de charge.  Dans cet exemple la *Weighted* méthode choisie.  Cela générera de demandes des clients sont réparties entre tous les points de terminaison application enregistrée en fonction des pondérations relatives associées à chaque point de terminaison. 

Avec le profil créé, chaque instance de l’application est ajoutée au profil comme point de terminaison Azure native.  Le code suivant extrait une référence à chaque application web frontal, puis ajoute chaque application comme point de terminaison le trafic gestionnaire par le biais du paramètre *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Remarquez est un appel à *Ajouter AzureTrafficManagerEndpointConfig* pour chaque instance d’application individuels.  Le paramètre *TargetResourceId* dans chaque commande Powershell fait référence à un des trois instances de l’application déployée.  Le profil de trafic Manager sera répartir la charge sur tous les points de trois terminaison inscrits dans le profil.

Toutes les trois points de terminaison utilisent la même valeur (10) pour le paramètre *d’épaisseur* .  Ainsi, le trafic Gestionnaire propagation demandes des clients dans toutes les instances d’application trois relativement uniformément. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Pointant de domaine personnalisé de l’application au niveau du domaine le trafic Manager ##
La dernière étape nécessaire consiste à pointer le domaine personnalisé de l’application au niveau du domaine gestionnaire le trafic.  Pour l’application d’exemple, cela signifie pointant *www.scalableasedemo.com* au *format SVG ase-demo.trafficmanager.net*.  Cette étape doit être effectuée de domaines qui gère le domaine personnalisé.  

À l’aide des outils de gestion de domaine de votre bureau d’enregistrement, un enregistrement CNAME enregistrements doivent être créées qui pointe le domaine personnalisé au niveau du domaine gestionnaire le trafic.  L’image ci-dessous montre un exemple de l’aspect de cette configuration CNAME :

![CNAME de domaine personnalisé][CNAMEforCustomDomain] 

Bien que ne pas traités dans cette rubrique, n’oubliez pas que chaque instance individuelle application doit comporter le domaine personnalisé enregistré avec lui également.  Dans le cas contraire si une demande de rend sur une instance de l’application et l’application n’a pas de domaine personnalisé enregistré auprès de l’application, la requête échoue.  

Dans cet exemple, le domaine personnalisé est *www.scalableasedemo.com*et chaque instance de l’application est associé un domaine personnalisé.

![Domaine personnalisé][CustomDomain] 

Pour un récapitulatif de d’enregistrement d’un domaine personnalisé avec les applications de Service d’application Azure, consultez l’article suivant sur [l’enregistrement de domaines personnalisés][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Évaluez la topologie distribué ##
Le résultat final de la configuration du Gestionnaire de trafic et DNS est que les demandes de *www.scalableasedemo.com* flux au sein de l’ordre suivant :

1. Un navigateur ou un périphérique effectue une recherche DNS pour *www.scalableasedemo.com*
2. L’entrée CNAME auprès du bureau d’enregistrement de domaine entraîne la recherche DNS est redirigé vers le Gestionnaire de trafic Azure.
3. Une recherche DNS est créée pour *scalable ase-demo.trafficmanager.net* par rapport à un des serveurs Azure le trafic Gestionnaire DNS.
4. En fonction de la stratégie (le paramètre *TrafficRoutingMethod* utilisé précédemment lorsque vous créez le profil de trafic Manager) d’équilibrage de charge, le trafic Gestionnaire sélectionnez un points de terminaison configuré et renvoyer le nom de domaine complet de ce point de terminaison vers le navigateur ou l’appareil.
5.  Étant donné que le nom de domaine complet du point de terminaison est l’Url d’une instance de l’application en cours d’exécution dans un environnement de Service d’application, le navigateur ou le périphérique demandera un serveur Microsoft Azure DNS pour résoudre le nom de domaine complet vers une adresse IP. 
6. Le navigateur ou le périphérique envoie la demande HTTP/S à l’adresse IP.  
7. La demande arrivera à l’un des instances de l’application en cours d’exécution sur l’un des environnements de Service d’application.

L’image de la console ci-dessous montre une recherche DNS de domaine personnalisé de l’application exemple correctement résolu à une instance de l’application en cours d’exécution sur l’un des trois exemples application Service environnements (dans ce cas, le deuxième des trois environnements de Service d’application) :

![Recherche DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informations et liens supplémentaires ##
Tous les articles et comment-du pour les environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Documentation sur le Powershell [prend en charge le trafic Azure ressource Gestionnaire][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 

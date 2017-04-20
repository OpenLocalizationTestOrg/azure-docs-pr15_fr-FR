<properties 
pageTitle="Déploiement HANA HANA/4 S ou bande passante/4 sur un ordinateur virtuel Azure | Microsoft Azure" 
description="Déploiement HANA/4 S ou HANA de bande passante/4 sur un ordinateur virtuel Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 


# <a name="deploying-s4-hana-or-bw4-hana-on-microsoft-azure"></a>Déploiement HANA/4 S ou HANA de bande passante/4 sur Microsoft Azure 

Cet article décrit comment déployer HANA S/4 sur Microsoft Azure via SAP Cloud matériel bibliothèque 3.0.
Les captures d’écran illustrent les processus étape par étape. Déploiement d’autres solutions SAP HANA tels que la bande passante/4 HANA fonctionne de la même manière du point de vue processus. Simplement ait à sélectionner une autre solution.

Tout d’abord SAP Cloud matériel bibliothèque (licence d’accès client SAP) accédez [ici](https://cal.sap.com/). Il existe un blog à partir de SAP sur les nouveaux [SAP Cloud matériel bibliothèque 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Les captures d’écran suivantes montrent comment déployer HANA S/4 sur Microsoft Azure étape par étape. Le processus fonctionne de la même façon pour d’autres solutions likeBW/4 HANA.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

La première image affiche toutes les solutions SAP appelez HANA qui sont disponibles sur Microsoft Azure.
Exemplarily le « SAP S/4 HANA locaux Édition » (solution au bas de la capture d’écran) a été choisie pour accéder à travers le processus.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Tout d’abord un compte SAP licence d’accès client doit être créé. Il existe actuellement deux possibilités pour Azure - standard Azure et Azure sur continent Chine qui est géré par 21Vianet partenaire.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Puis ait à entrer l’ID de l’abonnement Azure que vous pouvez trouver sur le portail Azure - également voir davantage vers le bas comment l’obtenir. Par la suite un certificat de gestion Azure doit être téléchargé.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

Dans la nouvelle Azure un portail trouve l’élément « Abonnements » dans la partie gauche. Cliquez dessus pour afficher tous les abonnements actifs pour vos utilisateurs.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Sélectionnez un des abonnements, puis choisissez « Gestion des certificats » qui explique il sont un nouveau concept à l’aide de « entités de service » pour le nouveau modèle Azure le Gestionnaire de ressources.
Licence d’accès client SAP n’est pas encore adapté pour ce nouveau modèle et nécessite toujours le modèle « classique » et le portail Azure ancien pour l’utiliser avec les certificats de gestion.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Ici, un peut voir le portail Azure ancien. Le téléchargement d’un certificat de gestion donne SAP appelez les autorisations pour créer des machines virtuelles au sein d’un compte client. Onglet le trouverez sous les abonnements » » l’ID de l’abonnement qui doit figurer dans le portail de licence d’accès client SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Sous l’onglet deuxième, il est alors possible télécharger le certificat de gestion qui a été téléchargé avant de licence d’accès client SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Une petite boîte de dialogue s’affiche pour sélectionner le fichier de certificat téléchargé.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Une fois le certificat a été téléchargé la connexion entre la licence d’accès client SAP et le client abonnement Azure peut être testée au sein de la licence d’accès client SAP. Un petit message doit apparaître qui indique que la connexion est valide.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Après l’installation d’un compte ait sélectionner une solution qui doit être déployée et créer une instance.
Avec le mode « base », il est très simple. Entrez un nom de l’instance, choisissez une région Azure et de définir le mot de passe maître pour la solution.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Après un certain temps selon la taille et la complexité de la solution (une estimation est donnée par la licence d’accès client SAP), il est affiché comme « actif » et prête à être utilisée. Il est très simple.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Certains des détails de la solution une peuvent voir quel type de machines virtuelles ont été déployés. Dans ce cas trois Azure machines virtuelles de différentes tailles et objectif ont été créés.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Dans le portail Azure, les machines virtuelles sont accessibles en commençant par le même nom d’instance qui a été attribué dans la licence d’accès client SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Maintenant, il est possible de se connecter à la solution via le bouton de connexion dans le portail de licence d’accès client SAP. La boîte de dialogue peu contient un lien vers un guide de l’utilisateur qui décrit toutes les informations d’identification par défaut pour l’utiliser avec la solution.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Une autre option consiste à ouvrir une session sur le client machine virtuelle Windows commence par exemple l’interface utilisateur SAP préconfiguré.








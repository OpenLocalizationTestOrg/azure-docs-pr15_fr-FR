<properties 
pageTitle="Déploiement de SAP IDES EHP7 SP3 pour SAP planification 6.0 sur Microsoft Azure | Microsoft Azure" 
description="Déploiement de SAP IDES EHP7 SP3 pour SAP planification 6.0 sur Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Déploiement de SAP IDES EHP7 SP3 pour SAP planification 6.0 sur Microsoft Azure 

Cet article décrit comment déployer IDES SAP en cours d’exécution avec SQL Server et le système d’exploitation Windows sur Microsoft Azure via SAP Cloud matériel bibliothèque 3.0. Les captures d’écran illustrent les processus étape par étape. Déploiement d’autres solutions dans la liste fonctionne de la même manière du point de vue processus. Simplement ait à sélectionner une autre solution.

Tout d’abord SAP Cloud matériel bibliothèque (licence d’accès client SAP) accédez [ici](https://cal.sap.com/). Il existe un blog à partir de SAP sur les nouveaux [SAP Cloud matériel bibliothèque 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Les captures d’écran suivantes montrent comment déployer IDES SAP sur Microsoft Azure étape par étape. Le processus fonctionne de la même façon pour d’autres solutions.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

La première image affiche toutes les solutions qui sont disponibles sur Microsoft Azure. La mise en surbrillance solution fonctionnant sous Windows SAP IDES qui est uniquement disponible sur Azure a été choisie pour accéder à travers le processus.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Tout d’abord un compte SAP licence d’accès client doit être créé. Il existe actuellement deux possibilités pour Azure - standard Azure et Azure sur continent Chine qui est géré par 21Vianet partenaire.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Puis ait à entrer l’ID de l’abonnement Azure que vous pouvez trouver sur le portail Azure - également voir davantage vers le bas comment l’obtenir. Par la suite un certificat de gestion Azure doit être téléchargé.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

Dans la nouvelle Azure un portail trouve l’élément « Abonnements » dans la partie gauche. Cliquez dessus pour afficher tous les abonnements actifs pour vos utilisateurs.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Sélectionnez un des abonnements, puis choisissez « Gestion des certificats » qui explique il sont un nouveau concept à l’aide de « entités de service » pour le nouveau modèle Azure le Gestionnaire de ressources.
Licence d’accès client SAP n’est pas encore adapté pour ce nouveau modèle et nécessite toujours le modèle « classique » et le portail Azure ancien pour l’utiliser avec les certificats de gestion.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Ici, un peut voir le portail Azure ancien. Le téléchargement d’un certificat de gestion donne SAP appelez les autorisations pour créer des machines virtuelles au sein d’un compte client. Onglet le trouverez sous les abonnements » » l’ID de l’abonnement qui doit figurer dans le portail de licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

Sous l’onglet deuxième, il est alors possible télécharger le certificat de gestion qui a été téléchargé avant de licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Une petite boîte de dialogue s’affiche pour sélectionner le fichier de certificat téléchargé.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Une fois le certificat a été téléchargé la connexion entre la licence d’accès client SAP et le client abonnement Azure peut être testée au sein de la licence d’accès client SAP. Un petit message doit apparaître qui indique que la connexion est valide.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Après l’installation d’un compte ait sélectionner une solution qui doit être déployée et créer une instance.
Avec le mode « base », il est très simple. Entrez un nom de l’instance, choisissez une région Azure et de définir le mot de passe maître pour la solution.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Après un certain temps selon la taille et la complexité de la solution (une estimation est donnée par la licence d’accès client SAP), il est affiché comme « actif » et prête à être utilisée. Il est très simple.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

Certains des détails de la solution une peuvent voir quel type de machines virtuelles ont été déployés. Dans ce cas est une machine virtuelle Azure unique de taille D12 qui a été créé par la licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

Dans le portail Azure, la machine virtuelle se trouve en commençant par le même nom d’instance qui a été attribué dans la licence d’accès client SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Maintenant, il est possible de se connecter à la solution via le bouton de connexion dans le portail de licence d’accès client SAP. La boîte de dialogue peu contient un lien vers un guide de l’utilisateur qui décrit toutes les informations d’identification par défaut pour l’utiliser avec la solution.
[Ici](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) représente le lien vers le guide pour la solution IDES.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Une autre option consiste à se connecter à la machine virtuelle Windows commence par exemple l’interface utilisateur SAP préconfiguré.






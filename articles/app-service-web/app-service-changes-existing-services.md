<properties
    pageTitle="Azure Application Service et son impact sur les services Azure existants"
    description="Explique comment le nouveau Service d’application Azure et ses fonctionnalités ont une incidence sur les services existants dans Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Azure Application Service et services Azure existants

Cet article présente les modifications apportées aux services Azure existants dans le cadre de la modification de réunir plusieurs services Azure en [Azure Application Service](https://azure.microsoft.com/services/app-service/), une nouvelle offre intégrée.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Vue d’ensemble

[Azure Application Service](https://azure.microsoft.com/services/app-service/) est un service cloud nouveau et unique qui permet aux développeurs de créer des web et des applications mobiles pour n’importe quelle plate-forme et n’importe quel appareil. Application de Service est une solution intégrée conçue pour rationaliser les fonctions de codage répétées, intégrer entreprise et systèmes SaaS et automatiser des processus métiers tout en répondant à vos besoins en matière de sécurité, la fiabilité et extensibilité élevées.

Application Service réunit les suivants Azure services existants - [sites Web](https://azure.microsoft.com/services/websites/), [Services Mobile](https://azure.microsoft.com/services/mobile-services/)et [Biztalk](https://azure.microsoft.com/services/biztalk-services/) un seul service combiné, lors de l’ajout de nouvelles fonctionnalités puissantes.  Application Service vous permet d’héberger les types d’application suivants :

-   Applications Web
-   Applications mobiles
-   Applications API
-   Applications de logique

Le tableau suivant explique comment les services Azure carte au Service d’application et les types d’application disponibles qu’il contient.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Service Azure existant</th>
<th align="left", style="width:10%">Service application Azure</th>
<th align="left", style="width:80%">Ce qui a changé</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sites Web Azure</td>
<td align="left">Applications Web</td>
<td align="left"><li>Les sites Web Azure, application Service est strictement limité par la modification du nom des sites Web aux applications Web.
<p><li>Toutes les instances existantes des sites Web sont désormais des applications Web dans le Service d’application.</p>
<p><li>Vous pouvez accéder à votre site Web existant via le <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portail Azure</a>, où vous trouverez tous vos sites existants sous <em>Applications Web</em>.</p>
<p><li><em>Web d’hébergement planifier</em> est à présent <em>Plan de Service d’application</em>. Un <em>Plan de Service d’application</em> peut héberger n’importe quel type d’application de Service d’application, telles que des applications Web, Mobile, logique ou API.</p>
<p><li>Disponibilité est en général Azure Application Service Web Apps.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">En savoir plus sur les applications Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Services mobiles Azure</td>
<td align="left">Applications mobiles</td>
<td align="left"><p><li>Services mobiles continuer à être utilisés comme un service autonome et restent entièrement prise en charge.</p>
<p><li>Applications Mobile est un type d’application dans le Service d’application, qui intègre toutes les fonctionnalités de Services mobiles et bien plus encore.</p>
<p><li>Il est facile de <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">migrer à partir de Services mobiles aux applications mobiles</a>.</p>
<p><li>Dans le cadre du Service d’application, applications Mobile obtenir nouvelles fonctionnalités au-delà de Services mobiles, tels que l’intégration avec les systèmes de SaaS et en local les intermédiaires emplacements, WebJobs, options d’échelle mieux et autres éléments.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">En savoir plus sur les applications Mobile</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Applications API</td>
<td align="left">
<p><li>Applications API est un nouveau type d’application dans le Service d’application qui vous permet de créer et d’utiliser des API dans le cloud facilement.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">En savoir plus sur les applications de l’API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Applications de logique</td>
<td align="left">
<p><li>Logique applications est un nouveau type d’application dans le Service d’application qui vous permet d’automatiser facilement les processus d’entreprise.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">En savoir plus sur les applications logique</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Services BizTalk Azure</td>
<td align="left">Applications BizTalk API</td>
<td align="left">
<li><p>Services BizTalk continuer à être utilisés comme un service autonome et restent entièrement prise en charge.</p>
<li><p>Toutes les fonctionnalités des Services BizTalk sont intégrées à l’application de Service en tant qu’utilisateurs de l’activation de l’API applications pour effectuer d’intégration et des scénarios d’intégration B2B avec n’importe lequel des types d’application dans le Service d’application</p>
<li><p>Les applications logique vous pouvez désormais automatiser des processus d’entreprise à l’aide d’une expérience de conception visuelle pour créer des flux de travail</p></td>
</tr>
</tbody>
</table>

Pour en savoir plus, visitez le site [documentation du Service d’application](https://azure.microsoft.com/documentation/services/app-service/).

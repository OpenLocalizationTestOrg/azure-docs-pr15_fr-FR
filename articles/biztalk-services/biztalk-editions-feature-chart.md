<properties
    pageTitle="En savoir plus sur les fonctionnalités dans les éditions BizTalk Services | Microsoft Azure"
    description="Comparer les fonctions des éditions BizTalk Services : libre, développeur, basique, Standard et Premium. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>BizTalk Services : Graphique des éditions

Les Services BizTalk Azure propose plusieurs éditions. Utilisez cet article pour déterminer quelle édition correspond à votre scénario et des besoins.


## <a name="compare-the-editions"></a>Comparer les éditions

**Libérer (Preview)**

Peut créer et gérer les connexions hybride. Une connexion hybride est un moyen facile de se connecter un site Web Azure à un système local, tel que SQL Server.

**Pour les développeurs**

Inclut les connexions hybride, messages et EDI le traitement des messages avec un portail de gestion de partenaire commercial facile à utiliser et la prise en charge des schémas EDI courantes et traitement EDI riche sur X12 et AS2. Pouvez créer des scénarios messages courants connexion services dans le cloud à l’aide des protocoles HTTP/S, reste, FTP, WCF et SFTP pour lire et écrire des messages.  Utiliser la connectivité aux systèmes métier en local avec prêts à l’emploi SAP Oracle e-Business, base de données Oracle, Siebel, cartes et SQL Server. Utiliser un environnement centré sur développeur avec Visual Studio tools pour le déploiement et de développement facile. Limité à des fins de développement et de test uniquement avec aucun Service (contrat de niveau).

**Base**

Inclut la plupart des capacités développeur avec augmentation des connexions de connexions hybrides, des ponts, accords EDI et BizTalk carte Pack messages. Offre également la disponibilité et l’option Mettre à l’échelle avec un contrat de niveau de Service (SLA).

**Standard**

Inclut toutes les fonctionnalités de base des augmentations de connexions connexions hybrides, des ponts, accords EDI et BizTalk carte Pack messages. Offre également la disponibilité et l’option Mettre à l’échelle avec un contrat de niveau de Service (SLA).

**Premium**

Inclut toutes les fonctionnalités Standard avec augmentation des connexions de connexions hybrides, des ponts, accords EDI et BizTalk carte Pack messages. Comprend également l’archivage, haute disponibilité et l’option Mettre à l’échelle avec un contrat de niveau de Service (SLA).


## <a name="editions-chart"></a>Graphique des éditions
Le tableau suivant répertorie les différences.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Libérer (Preview)</th>
        <th>Pour les développeurs</th>
        <th>Base</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Prix de départ</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Services d’Azure BizTalk tarifs</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure calculateur de prix</a></td>
</tr>
<tr>
<td><strong>Configuration minimale par défaut</strong></td>
<td>1 unité gratuite</td>
<td>Unité 1 pour les développeurs</td>
<td>1 unité de base</td>
<td>1 unité standard</td>
<td>Unité 1 premium</td>
</tr>
<tr>
<td><strong>Échelle</strong></td>
<td>Aucune échelle</td>
<td>Aucune échelle</td>
<td>Oui, par incréments de 1 unité de base</td>
<td>Oui, par incréments de 1 unité Standard</td>
<td>Oui, par incréments de 1 unité Premium</td>
</tr>
<tr>
<td><strong>Nombre maximal autorisé mise à l’échelle</strong></td>
<td>Aucune échelle</td>
<td>Aucune échelle</td>
<td>Jusqu'à 8 unités</td>
<td>Jusqu'à 8 unités</td>
<td>Jusqu'à 8 unités</td>
</tr>
<tr>
<td><strong>Ponts de messages par unité</strong></td>
<td>Non incluse</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclut le contrats de plateforme sécurisée</td>
<td>Non incluse</td>
<td>Inclus. 10 accords unitaire.</td>
<td>Inclus. 50 accords unitaire.</td>
<td>Inclus. 250 accords unitaire.</td>
<td>Inclus. contrats de 1000 unitaire.</td>
</tr>
<tr>
<td><strong>Connexions hybride par unité</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transfert de données de connexion hybride (Go) par unité</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Connexions au Service de carte BizTalk aux systèmes métier en local</strong></td>
<td>Non incluse</td>
<td>connexion de 1</td>
<td>2 connexions</td>
<td>5 connexions</td>
<td>25 connexions</td>
</tr>
<tr>
<td align="left"><strong>Systèmes/protocoles pris en charge :</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus des services (petite)</li>
<li>Objets Blob Azure</li>
<li>API REST</li>
</ul>
</td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Disponibilité</strong>
<br/><br/>
Pour Service (contrat de niveau), consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services tarification</a>.
</td>
<td>Non incluse</td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Sauvegarde et restauration</strong></td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Suivi</strong></td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>L’archivage</strong><br/><br/>
Inclut Non répudiation de réception (NRR) et le téléchargement des messages suivis</td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Non incluse</td>
<td>Non incluse</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Utilisation de code personnalisé</strong></td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Utilisation des transformations, y compris XSLT personnalisé</strong></td>
<td>Non incluse</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
</table>

> [AZURE.NOTE] Pour la résilience contre les défaillances matérielles, haute disponibilité suppose que vous disposiez de plusieurs machines virtuelles au sein d’une seule unité BizTalk.


## <a name="faqs"></a>Forum aux questions

#### <a name="what-is-a-biztalk-unit"></a>Qu’est une unité BizTalk ?
Une « unité » est le niveau atomique d’un déploiement d’Azure BizTalk Services. Chaque édition est fourni avec une unité qui possède la quantité de mémoire et la capacité de cluster différent. Par exemple, une unité a cluster plus que pour les développeurs, Standard a plus cluster de base et ainsi de suite. Lorsque vous redimensionnez un BizTalk Service, vous mettre à l’échelle en termes d’unités.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Quelle est la différence entre les Services BizTalk et machine virtuelle BizTalk Azure ?
Services BizTalk fournit une architecture de plateforme-as-a-Service (PaaS) true pour créer des solutions d’intégration dans le cloud. Avec le modèle PaaS, vous concentrez-vous entièrement sur la logique d’application et de la gestion de l’infrastructure toutes laissez à Microsoft, notamment :

- Sans avoir à gérer ou corriger machines virtuelles.
- Microsoft garantit la disponibilité.
- Vous pouvez contrôler échelle à la demande en demandant simplement plus ou moins capacité via le portail d’Azure.

BizTalk Server Azure machines virtuelles en fonctionnement fournit une architecture Infrastructure-as-a-Service (IaaS). Vous créez des machines virtuelles et les configurez exactement comme votre environnement local, ce qui facilite l’exécuter les applications existantes dans le cloud avec aucune modification de code. Avec IaaS, vous êtes toujours responsable de configurer les ordinateurs virtuels, gérer les machines virtuelles (par exemple, installé des logiciels et correctifs du système d’exploitation) et architecture de l’application de disponibilité.

Si vous cherchez à la création de nouvelles solutions intégration réduire votre travail de gestion d’infrastructure, utiliser les Services BizTalk. Si vous aimeriez pour migrer rapidement vos solutions existantes BizTalk ou si vous recherchez un environnement à la demande développer et tester les applications BizTalk Server, vous pouvez utiliser BizTalk Server sur une Machine virtuelle Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Quelle est la différence entre le Service de carte BizTalk et connexions hybride ?
Le Service de carte BizTalk est utilisé par un BizTalk Service Azure. Le Service de carte BizTalk utilise le module carte BizTalk pour vous connecter à un système de ligne d’entreprise (métier) en local. Une connexion hybride fournit un moyen facile et pratique de connecter des applications Azure, telles que la fonctionnalité Web Apps dans Azure Application Service et Azure Mobile Services, pour une ressource locale.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Que signifie « Hybride connexion transfert de données (Go) par unité » ? S’agit-il par minute/heure/jour/semaine/mois ? Que se passe-t-il lorsque la limite est atteinte ?

Le coût de connexion hybride unitaire dépend de l’Édition Services BizTalk. En bref, coûts dépendent de la quantité de données que vous transférez. Par exemple, transfert de données de 10 Go quotidiennement les coûts inférieur à transfert quotidiennement 100 Go. [Calculatrice de tarification](https://azure.microsoft.com/pricing/calculator/?scenario=full) pour les Services BizTalk permet de déterminer les coûts spécifiques. En règle générale, les limites sont appliquées quotidiennement. Si vous dépassez la limite, tout excédent est facturée au taux de 1 $ par Go.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Lorsque je crée un contrat dans les Services BizTalk, pourquoi le nombre de ponts a-t-elle vers le haut par deux au lieu d’un seul ?

Chaque contrat se compose de deux ponts différents : un pont de communication envoyer côté et un pont de communication côté réception.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Que se passe-t-il lorsque j’atteint la limite de quota sur le nombre de ponts ou accords ?

Vous n’êtes pas déployer les nouveaux ponts ni en créer de tout nouveaux accords. Pour déployer plus, vous devez évoluer à plus d’unités du service BizTalk ou mettre à niveau vers une édition supérieure.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Comment migrer d’un niveau de Services BizTalk à l’autre ?

L’édition gratuite ne peuvent pas être migrés ou 'agrandies » vers un autre niveau et ne peuvent pas être sauvegardés et restaurés vers un autre niveau. Si vous avez besoin d’une autre couche, créez un nouveau BizTalk Service à l’aide de la nouvelle couche. Les objets créés à l’aide de l’édition gratuite, y compris les connexions hybride devront être recréés dans le nouveau BizTalk Service. 

Pour les éditions restantes, utilisez la sauvegarde et restauration de la migration de vos objets d’un niveau à l’autre. Par exemple, sauvegarder vos objets dans la couche Standard et puis de les restaurer vers le niveau Premium. [Services BizTalk : sauvegarder et restaurer](biztalk-backup-restore.md) décrit les chemins de migration pris en charge et répertorie les objets sont sauvegardés. Notez que les connexions hybride ne sont pas sauvegardées. Après la sauvegarde et restauration vers une nouvelle couche, vous puis recréez les connexions hybride.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>Est le Service de carte BizTalk inclus dans le service ? Comment recevoir le logiciel ?

Oui, le Service de carte BizTalk avec le module carte BizTalk sont inclus dans le SDK des Services BizTalk Azure [Télécharger](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Étapes suivantes

Pour créer des Services BizTalk Azure dans le portail Azure, accédez à [Services BizTalk : mise en service à l’aide du portail Azure](biztalk-provision-services.md). Pour commencer la création d’applications, accédez aux [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Ressources supplémentaires
- [Services BizTalk : Mise en service à l’aide du portail Azure](biztalk-provision-services.md)<br/>
- [Services BizTalk : Graphique de l’état de la mise en service](biztalk-service-state-chart.md)<br/>
- [BizTalk Services : Onglets de tableau de bord, surveiller et échelle](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [Les Services BizTalk : Sauvegarder et restaurer](biztalk-backup-restore.md)<br/>
- [Services BizTalk : la limitation](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services : Nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md)<br/>
- [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

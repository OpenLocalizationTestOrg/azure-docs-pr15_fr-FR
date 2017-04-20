<properties
   pageTitle="Gestion des identités dans Azure | Microsoft Azure"
   description="Explique et compare les différentes méthodes disponibles pour la gestion des identités dans des systèmes hybride qui s’étalent sur la limite de cloud/le locaux avec Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Gestion des identités dans Azure

Dans la plupart des systèmes d’entreprise en fonction de Windows, vous allez utiliser Active Directory (AD) pour fournir des services de gestion des identités à vos applications. AD fonctionne bien dans un environnement local, mais lorsque vous étendez votre infrastructure réseau dans le cloud vous avez quelques décisions importantes à prendre en ce qui concerne la gestion des identités. Doit développer vos domaines locaux pour incorporer des machines virtuelles dans le cloud ? Vous devez créer des domaines dans le cloud et le cas échéant, comment ? Vous devez implémenter votre propre forêt dans le cloud ou devez vous devez utiliser d’Azure Active Directory (DAS) ?

Cet article décrit certaines des options courantes pour les défis posées par ce scénario, et vous aide à que déterminer quelle solution mieux répondre à vos besoins selon vos besoins.

## <a name="using-azure-active-directory"></a>Utilisation d’Azure Active Directory

Vous pouvez utiliser AAD pour créer un domaine AD dans le cloud et lien vers un local domaine Active Directory. AAD vous permettent de configurer authentification unique (SSO) permet aux utilisateurs exécutant des applications accédées via le cloud.

[! [0]][0]

DAS est un moyen simple d’implémenter un domaine de sécurité dans le cloud. Il est utilisé par de nombreuses applications Microsoft, tel que Microsoft Office 365. 

Avantages de l’utilisation AAD :

- Il n’est pas nécessaire de maintenir une infrastructure Active Directory dans le cloud. DAS est entièrement gérée et gérés par Microsoft.

- AAD fournit les mêmes informations d’identité qui sont disponible en local.

- Authentification peut se produire dans Azure, réduction de la nécessité d’applications externes et les utilisateurs à contacter le domaine local.

Points à prendre en considération lorsque vous utilisez AAD :

- Services d’identité sont limités à des utilisateurs et groupes. Il n’est pas la possibilité pour authentifier les comptes de service et de l’ordinateur.

- Vous devez configurer la connectivité avec votre domaine local à garder à l’annuaire AAD synchronisé. 

- Vous êtes responsable de la publication d’applications que les utilisateurs peuvent accéder dans le cloud via AAD.

Pour plus d’informations, lisez [Implémentation d’Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>Utilisation d’Active Directory dans le cloud lié à une forêt locale

Vous pouvez héberger les Services d’annuaire Active Directory (AD DS) en local, mais dans un scénario hybride où se trouvent les éléments d’une application dans Azure, il peut être plus efficace pour répliquer cette fonctionnalité et le référentiel AD dans le cloud. Cette approche permet de réduire la latence provoquée par l’envoi d’authentification et les demandes d’autorisation locales à partir du cloud l’à AD DS en cours d’exécution en local. 

[! [1]][1]

Cette approche nécessite que vous créez votre propre domaine dans le cloud et participer à la forêt locale. Vous créez des machines virtuelles pour héberger les services AD DS.

Avantages de l’utilisation d’un domaine distinct dans le cloud :

- Permet de s’authentifier utilisateur, le service et ordinateur comptes en local et dans le cloud.

- Permet d’accéder aux mêmes informations d’identité qui sont disponible en local.

- Il est inutile de gérer une forêt AD distincte ; le domaine dans le cloud peut appartenir à la forêt locale.

- Vous pouvez appliquer la stratégie de groupe par des objets de stratégie de groupe en local pour le domaine dans le cloud.

Considérations sur l’utilisation d’un domaine distinct dans le cloud :

- Vous devez créer et gérer vos propres serveurs AD DS et votre domaine dans le cloud.

- Il peut y avoir une latence de synchronisation entre les serveurs de domaine dans le cloud et les serveurs exécutant en local.

Pour plus d’informations sur la façon de configurer cette architecture, voir [Extension d’Active Directory Directory Services (ajoute) vers Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>Utilisation d’Active Directory avec une forêt

Une organisation qui s’exécute Active Directory (AD) locaux peut avoir une forêt comprenant de nombreux domaines différents. Vous pouvez utiliser des domaines pour fournir la séparation entre les différentes zones fonctionnelles doivent être séparés, éventuellement pour des raisons de sécurité, mais vous pouvez partager des informations entre des domaines en établissant des relations de gestion de la confidentialité.

[! [2]][2]

Une organisation qui utilise des domaines distincts peut bénéficier de Azure en déplaçant vers un ou plusieurs de ces domaines dans une forêt distincte dans le cloud. Par ailleurs, une organisation peut souhaiter conserver toutes les ressources de cloud logiquement distinctes des ces maintenu en local et stocker des informations sur les ressources du cloud dans leur propre répertoire, dans le cadre d’une forêt également contenue dans le cloud.

Avantages de l’utilisation d’une forêt distincte dans le cloud :

- Vous pouvez mettre en œuvre des identités en local et séparez les identités Azure seule.

- Il est inutile de répliquer le locales forêt AD vers Azure, réduire les effets de latence du réseau.

Considérations relatives à :

- Authentification pour les identités en local dans le nuage effectue réseau supplémentaire *sauts* vers la local serveurs Active Directory.

- Vous devez mettre en œuvre vos propres serveurs AD DS et forêt dans le cloud et établir les relations d’approbation appropriée entre forêts.

Le document [Création d’une forêt de ressources dans Azure Active Directory Directory Services (ajoute)] [ adds-forest-in-azure] décrit comment mettre en œuvre cette approche plus en détail.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>À l’aide d’Active Directory Federation Services (ADFS) avec Azure

ADFS peut s’exécuter en local, mais dans un scénario hybride où se trouvent les applications dans Azure il peut être plus efficace d’implémenter cette fonctionnalité dans le cloud, comme illustré ci-dessous.

[! [3]][3]

Cette architecture est particulièrement utile pour :

- Solutions qui utilisent fédérés d’autorisation pour exposer des applications web aux organisations partenaires.

- Systèmes prenant en charge l’accès à partir de navigateurs web en cours d’exécution en dehors du pare-feu de votre entreprise.

- Systèmes qui permettent aux utilisateurs d’accéder aux applications web en vous connectant à partir d’appareils externes conseillers tels qu’ordinateurs distants blocs-notes et autres appareils mobiles. 

Avantages de l’utilisation des services ADFS avec Azure :

- Vous pouvez tirer parti des applications prenant en charge les revendications.

- Il offre la possibilité d’approuver les partenaires externes pour l’authentification.

- Il assure la compatibilité avec grand ensemble de protocoles d’authentification.

Considérations sur l’utilisation des services ADFS avec Azure :

- Il vous demande d’implémenter vos propres serveurs ajoute, AD FS et Proxy AD FS de l’Application Web dans le cloud.

- Cette architecture peut être complexe à configurer.

Pour plus d’informations, lisez [Implémentation Active Directory Federation Services (ADFS) dans Azure][adfs-in-azure].

## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes expliquent comment mettre en œuvre les architectures décrites dans cet article.

- [Implémentation d’Azure Active Directory][implementing-aad]
- [Extension de Services Active Directory (ajoute) vers Azure][extending-adds]
- [Création d’une forêt de ressources Services : (Active Directory Directory ajoute) dans Azure][adds-forest-in-azure]
- [Mise en œuvre des Services de fédération Active Directory (AD FS) dans Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Architecture d’identité cloud à l’aide d’Azure Active Directory"
[1]: ./media/guidance-identity/figure2.png "Architecture de réseau hybride avec Active Directory sécurisée"
[2]: ./media/guidance-identity/figure3.png "Architecture de réseau hybride avec distinct domaines AD et forêts sécurisée"
[3]: ./media/guidance-identity/figure4.png "Architecture de réseau hybride avec les services ADFS sécurisée"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md
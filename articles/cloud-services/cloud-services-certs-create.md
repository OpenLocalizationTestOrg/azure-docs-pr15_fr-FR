<properties 
    pageTitle="Cloud Services et gestion des certificats | Microsoft Azure" 
    description="Découvrez comment créer et utiliser des certificats avec Microsoft Azure" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Vue d’ensemble des certificats pour les Services en nuage Azure
Certificats sont utilisés dans Azure des services en nuage ([certificats de service](#what-are-service-certificates)) et pour l’authentification avec la direction de l’API ([certificats de gestion](#what-are-management-certificates) lors de l’aide du portail classique Azure et processeur pas). Cette rubrique fournit une vue d’ensemble les deux types de certificats, comment [créer](#create) et [déployer](#deploy) vers Azure.

Les certificats utilisés dans Azure sont de type x.509 v3 certificats et peuvent être signés par un autre certificat approuvé ou elles peuvent être auto-signé. Un certificat auto-signé est signé par son propre créateur et pour cette raison, ne sont pas approuvés par défaut. La plupart des navigateurs peuvent ignorer. Certificats auto-signé doivent uniquement être utilisés par vous-même lors du développement et test de vos services cloud. 

Certificats utilisés par Azure peuvent contenir un privé ou une clé publique. Les certificats ont une empreinte numérique qui permet de les identifier sans ambiguïté. Cette empreinte permettant d’identifier le certificat un service cloud doit utiliser dans le [fichier de configuration](cloud-services-configure-ssl-certificate.md) Azure. 

## <a name="what-are-service-certificates"></a>Que sont les certificats du service ?
Certificats de service sont joints aux services cloud et sécuriser la communication vers et depuis le service. Par exemple, si vous avez déployé un rôle web, vous pouvez fournir un certificat qui peut s’authentifier un point de terminaison HTTPS exposée. Certificats de service, définis dans la définition de votre service, sont automatiquement déployés sur l’ordinateur virtuel qui exécute une instance de votre rôle. 

Vous pouvez télécharger les certificats de service au portail classique Azure à l’aide du portail classique Azure ou à l’aide de l’API de gestion de Service. Certificats de service sont associées à un service cloud spécifique et affectées à un déploiement du fichier de définition du service.

Certificats de service pouvant être gérées séparément à partir de vos services et peuvent être gérés par des personnes différentes. Par exemple, un développeur peut télécharger un package de service qui fait référence à un certificat un responsable informatique a précédemment téléchargé dans Azure. Un responsable informatique peut gérer et renouveler ce certificat modification de la configuration du service sans avoir besoin de télécharger un package de service nouveau. Cela est possible, car le nom logique pour le certificat et son nom du magasin et son emplacement sont spécifiés dans le fichier de définition du service, tandis que l’empreinte numérique du certificat spécifié dans le fichier de configuration de service. Pour mettre à jour le certificat, il n’est plus nécessaire télécharger un nouveau certificat et modifiez la valeur de l’empreinte numérique dans le fichier de configuration de service.

## <a name="what-are-management-certificates"></a>Que sont les certificats de gestion ?
Certificats de gestion permettent de vous authentifier avec l’API de gestion de Service fournie par Azure classique. De nombreux programmes et outils (tels que Visual Studio ou le Kit de développement Azure) utilisera ces certificats pour automatiser la configuration et déploiement de différents services Azure. Ils ne sont pas vraiment liés aux services cloud. 

>[AZURE.WARNING] Fais attention ! Ces types de certificats autoriser les personnes qui authentifie avec eux pour gérer l’abonnement qu'auquel elles sont associées. 

### <a name="limitations"></a>Limitations
Il existe une limite de 100 certificats gestion par abonnement. Il existe également une limite de 100 certificats gestion pour tous les abonnements sous ID d’utilisateur de. l’administrateur d’un service spécifique Si l’ID utilisateur pour l’administrateur de compte a déjà été utilisé pour ajouter des 100 gestion des certificats et il est nécessaire pour les certificats plus, vous pouvez ajouter un administrateur de co-création pour ajouter les certificats supplémentaires. 

Avant d’ajouter plus de 100 certificats, voir si vous pouvez réutiliser un certificat existant. À l’aide de coadministrateurs complique potentiellement inutiles votre processus de gestion des certificats.


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Créer un nouveau certificat auto-signé
Vous pouvez utiliser n’importe quel outil disponible pour créer un certificat auto-signé dans la mesure où elles sont conformes à ces paramètres :

* Un certificat X.509.
* Contient une clé privée.
* Créée pour l’échange de clés (fichier .pfx).
* Nom de sujet doit correspondre le domaine utilisé pour accéder au service cloud. 
    > Vous ne pouvez pas acquérir un domaine de certificat de la cloudapp.net (ou pour toute Azure liées) SSL ; nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, **contoso.net**pas **contoso.cloudapp.net**.
* Nombre minimal de chiffrement 2048 bits.
* **Certificat de service uniquement**: certificat côté Client doit se trouver dans le magasin de certificats *personnels* .

Il existe deux méthodes simples pour créer un certificat dans Windows, avec la `makecert.exe` utilitaire ou IIS.

### <a name="makecertexe"></a>Makecert.exe

Cet utilitaire a été déconseillé et ne sont plus présentée ici. Consultez [cet article MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968) pour plus d’informations.

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Si vous souhaitez utiliser le certificat avec une adresse IP au lieu d’un domaine, utilisez l’adresse IP dans le paramètre - NomDNS.


Si vous souhaitez utiliser ce [certificat avec le portail de gestion](../azure-api-management-certs.md), exporter vers un fichier **.cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Il existe de nombreuses pages sur internet qui expliquent comment effectuer cette opération avec IIS. [Ici](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) est un excellent que j’ai trouvées que j’ai pensent explique bien. 

### <a name="java"></a>Java
Vous pouvez utiliser Java pour [créer un certificat](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### <a name="linux"></a>Linux
[Cet](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) article décrit comment créer des certificats avec SSH.

## <a name="next-steps"></a>Étapes suivantes

[Télécharger votre certificat du service sur le portail classique Azure](cloud-services-configure-ssl-certificate.md) (ou le [portail Azure](cloud-services-configure-ssl-certificate-portal.md)).

Télécharger un [certificat de gestion de l’API](../azure-api-management-certs.md) dans le portail classique Azure.

>[AZURE.NOTE] Le portail Azure n’utilise pas de certificats de gestion de pour accéder à l’API mais utilise à la place des comptes d’utilisateur.

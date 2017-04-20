<properties 
    pageTitle="Configurez SSL pour un service cloud (standard) | Microsoft Azure" 
    description="Découvrez comment spécifier un point de terminaison HTTPS pour un rôle web et comment télécharger un certificat SSL pour sécuriser votre application." 
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
    ms.date="10/04/2016"
    ms.author="adegeo"/>




# <a name="configuring-ssl-for-an-application-in-azure"></a>Configuration de SSL pour une application dans Azure

> [AZURE.SELECTOR]
- [Portail Azure](cloud-services-configure-ssl-certificate-portal.md)
- [Portail classique Azure](cloud-services-configure-ssl-certificate.md)

Secure Socket Layer (SSL) de chiffrement est la méthode plus couramment utilisée de sécuriser les données envoyées sur internet. Cette tâche courante explique comment spécifier un point de terminaison HTTPS pour un rôle web et comment télécharger un certificat SSL pour sécuriser votre application.

> [AZURE.NOTE] Les procédures dans cette tâche s’appliquent aux Services Cloud Azure ; pour les Services d’application, voir [cet](../app-service-web/web-sites-configure-ssl-certificate.md) article.

Cette tâche utilise un déploiement de production. Vous trouverez des informations sur l’utilisation d’un déploiement intermédiaire à la fin de cette rubrique.

Lisez [cet](cloud-services-how-to-create-deploy.md) article tout d’abord si vous n’avez pas encore créé un service cloud.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]


## <a name="step-1-get-an-ssl-certificate"></a>Étape 1 : Obtenir un certificat SSL

Pour configurer SSL pour une application, vous devez tout d’abord obtenir un certificat SSL signé par une autorité de certification (CA), un tiers approuvé qui émet des certificats à cet effet. Si vous n’en avez pas déjà, vous devez obtenir une société qui vend des certificats SSL.

Le certificat doit répondre aux exigences suivantes pour les certificats SSL dans Azure :

-   Le certificat doit contenir une clé privée.
-   Le certificat doit être créé pour l’échange de clés, exportable vers un fichier d’échange d’informations personnelles (.pfx).
-   Nom de sujet du certificat doit correspondre le domaine utilisé pour accéder au service cloud. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le domaine cloudapp.net. Vous devez vous procurer un nom de domaine personnalisé à utiliser pour accéder à votre service. Lorsque vous demandez un certificat à partir d’une autorité de certification, nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, si votre nom de domaine est **contoso.com** demander un certificat à partir de votre autorité de certification pour * **. contoso.com** ou * *www.contoso.com**.
-   Le certificat doit utiliser un minimum de chiffrement 2048 bits.

À des fins de test, vous pouvez [créer](cloud-services-certs-create.md) et utiliser un certificat auto-signé. Un certificat auto-signé n’est pas authentifié par une autorité de certification et pouvez utiliser le domaine cloudapp.net comme l’URL de site Web. Par exemple, la tâche suivante utilise un certificat auto-signé dans lequel le nom commun (NC) utilisé dans le certificat est **sslexample.cloudapp.net**.

Ensuite, vous devez inclure d’informations sur le certificat dans votre définition de service et les fichiers de configuration de service.

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Étape 2 : Modifier les fichiers de définition et la configuration du service

Votre application doit être configurée pour utiliser le certificat, et un point de terminaison HTTPS doit être ajouté. Par conséquent, la définition de service et les fichiers de configuration de service doivent être mis à jour.

1.  Dans votre environnement de développement, ouvrez le fichier de définition de service (CSDEF), ajouter une section de **certificats** dans la section **WebRole** et inclure les informations suivantes sur le certificat (et les certificats intermédiaires) :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="My"
                             permissionLevel="limitedOrElevated" />
                <!-- IMPORTANT! Unless your certificate is either
                self-signed or signed directly by the CA root, you
                must include all the intermediate certificates
                here. You must list them here, even if they are
                not bound to any endpoints. Failing to list any of
                the intermediate certificates may cause hard-to-reproduce
                interoperability problems on some clients.-->
                <Certificate name="CAForSampleCertificate"
                             storeLocation="LocalMachine"
                             storeName="CA"
                             permissionLevel="limitedOrElevated" />
            </Certificates>
        ...
        </WebRole>

    La section **certificats** définit le nom de notre certificat, son emplacement et le nom de la banque où elle se trouve.
    
    Autorisations (`permisionLevel` attribut) peut être défini à l’une des valeurs suivantes :

  	| Valeur d’autorisation  | Description |
  	| ----------------  | ----------- |
  	| limitedOrElevated | **(Par défaut)** Tous les processus de rôle peuvent accéder à la clé privée. |
  	| avec élévation de privilèges          | Uniquement les processus élevés peuvent accéder à la clé privée.|

2.  Dans votre fichier de définition de service, ajoutez un élément **InputEndpoint** dans la section **points de terminaison** pour activer HTTPS :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Dans votre fichier de définition de service, ajoutez un élément **de liaison** dans la section **Sites** . Cette section ajoute une liaison HTTPS pour établir une correspondance entre le point de terminaison de votre site :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Toutes les modifications nécessaires au fichier de définition du service ont été achevées, mais vous avez encore besoin d’ajouter les informations du certificat vers le fichier de configuration de service.

4.  Dans votre fichier de configuration de service (CSCFG), ServiceConfiguration.Cloud.cscfg, ajoutez une section de **certificats** dans la section **rôle** , remplacez la valeur d’empreinte numérique d’exemple ci-dessous avec celui de votre certificat :

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
                <Certificate name="CAForSampleCertificate"
                    thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(L’exemple précédent utilise **sha1** pour l’algorithme empreinte numérique. Spécifier la valeur appropriée pour l’algorithme de votre certificat d’empreinte numérique).

À présent que la définition de service et les fichiers de configuration de service ont été mis à jour, créez un package votre déploiement pour le téléchargement vers Azure. Si vous utilisez **cspack**, n’utilisez pas l’indicateur **/generateConfigurationFile** , comme qui remplace les informations de certificat que vous avez inséré.

## <a name="step-3-upload-a-certificate"></a>Étape 3 : Télécharger un certificat

Votre package de déploiement a été mis à jour pour utiliser le certificat, puis un point de terminaison HTTPS a été ajouté. Maintenant vous pouvez télécharger le package et le certificat sur Azure grâce au portail classique Azure.

1. Connectez-vous au [portail classique Azure][]. 
2. Cliquez sur **Services de Cloud** dans le volet de navigation gauche.
3. Cliquez sur le service en nuage de votre choix.
4. Cliquez sur l’onglet **certificats** .

    ![Cliquez sur l’onglet certificats](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Cliquez sur le bouton **Télécharger** .

    ![Télécharger](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Indiquez le **fichier**, **votre mot de passe**, puis cliquez sur **achevé** (la coche).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Étape 4 : Se connecter à l’instance de rôle à l’aide de HTTPS

Maintenant que votre déploiement est en cours d’exécution dans Azure, vous pouvez vous connecter à l’aide de HTTPS.

1.  Dans le portail classique Azure, sélectionnez votre déploiement, puis cliquez sur le lien sous **URL du Site**.

    ![Déterminer l’URL du site][2]

2.  Dans votre navigateur web, modifier le lien pour utiliser **https** au lieu de **http**et puis accédez à la page.

    >[AZURE.NOTE] Si vous utilisez un certificat auto-signé, lorsque vous naviguez vers un point de terminaison HTTPS associée le certificat auto-signé qu'affiche une erreur de certificat dans le navigateur. L’utilisation d’un certificat signé par une autorité de certification approuvée élimine ce problème ; en attendant, vous pouvez ignorer l’erreur. (Une autre option consiste à ajouter le certificat auto-signé au magasin de certificats de l’autorité de certification approuvée de l’utilisateur).

    ![SSL exemple de site web][3]

Si vous voulez utiliser SSL pour un déploiement intermédiaire au lieu d’un déploiement de production, vous devez tout d’abord déterminer l’URL utilisée pour le déploiement intermédiaire. Déployez votre service cloud à l’environnement intermédiaire sans inclure un certificat ou les informations sur le certificat. Une fois déployé, vous pouvez déterminer l’URL basée sur GUID, qui figure dans le champ de **L’URL du Site** du portail classique Azure. Créer un certificat avec le nom commun (NC) égal à l’URL basée sur GUID (par exemple, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Le portail classique Azure permet d’ajouter le certificat à votre service cloud intermédiaire. Ensuite, ajoutez les informations de certificat à vos fichiers CSDEF et CSCFG, réorganiser votre application et mettre à jour votre déploiement intermédiaire pour utiliser le nouveau lot.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration générale de votre service cloud](cloud-services-how-to-configure.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* [Gérer votre service cloud](cloud-services-how-to-manage.md).


  [Portail classique Azure]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  

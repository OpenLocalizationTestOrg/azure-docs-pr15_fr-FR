<properties 
    pageTitle="La sécurisation de l’authentification par certificat Azure API de gestion des services principale à l’aide de client" 
    description="Découvrez comment sécuriser les services principale à l’aide d’authentification par certificat client Gestion des API Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>La sécurisation de l’authentification par certificat Azure API de gestion des services principale à l’aide de client

Gestion des API offre la possibilité pour sécuriser l’accès au service d’une API principale à l’aide de certificats clients. Ce guide explique comment gérer les certificats dans le portail de publisher API et comment configurer une API pour utiliser un certificat pour accéder à son service principale.

Pour plus d’informations sur la gestion des certificats à l’aide de l’API REST de gestion des API, voir [entité certificat API REST de gestion de l’API Azure][].

## <a name="prerequisites"> </a>Conditions préalables

Ce guide vous montre comment configurer votre instance de service de gestion de l’API pour utiliser l’authentification par certificat client pour accéder au service principale pour une API. Avant de suivre les étapes décrites dans cette rubrique, vous devez être votre service principale configuré pour l’authentification de certificat client ([pour configurer l’authentification par certificat dans les sites Web Azure reportez-vous à cet article][]) et avoir accès au certificat et le mot de passe pour le certificat pour le téléchargement dans le portail de gestion de l’API de publisher.

## <a name="step1"> </a>Télécharger un certificat client

Pour commencer, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail API Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **sécurité** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **certificats clients**.

![Certificats clients][api-management-security-client-certificates]

Pour télécharger un nouveau certificat, cliquez sur **Télécharger le certificat**.

![Télécharger le certificat][api-management-upload-certificate]

Accédez à votre certificat, puis entrez le mot de passe pour le certificat.

>Le certificat doit être au format **.pfx** . Certificats auto-signé sont autorisées.

![Télécharger le certificat][api-management-upload-certificate-form]

Cliquez sur **Télécharger** pour télécharger le certificat.

>Le mot de passe du certificat est validée pour le moment. S’il est incorrect, un message d’erreur s’affiche.

![Certificat téléchargé][api-management-certificate-uploaded]

Une fois que le certificat est téléchargé, il apparaît sous l’onglet **certificats clients** . Si vous avez plusieurs certificats, créer une note de l’objet, ou les quatre derniers caractères de l’empreinte numérique, qui sont utilisés pour sélectionner le certificat lorsque vous configurez une API pour utiliser des certificats, comme décrit dans la section [configurer une API pour utiliser un certificat client pour l’authentification de passerelle][] suivante.

>Pour désactiver validation de la chaîne certificat lorsque vous utilisez, par exemple, un certificat auto-signé, suivez les étapes décrites dans ce forum aux questions sur l' [élément](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Supprimer un certificat client

Pour supprimer un certificat, cliquez sur **Supprimer** en regard du certificat souhaité.

![Supprimer le certificat][api-management-certificate-delete]

Cliquez sur **Oui, supprimez-la** pour confirmer.

![Confirmer la suppression][api-management-confirm-delete]

Si le certificat est utilisé par une API, un écran d’avertissement s’affiche. Pour supprimer le certificat que vous devez d’abord supprimer le certificat d’API qui sont configurés pour l’utiliser.

![Confirmer la suppression][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurer une API pour utiliser un certificat client pour l’authentification de passerelle

Cliquez sur **API** dans le menu de **Gestion de l’API** dans la partie gauche, cliquez sur le nom de l’API de votre choix, puis cliquez sur l’onglet **sécurité** .

![Sécurité de l’API][api-management-api-security]

Sélectionnez **les certificats Client** dans la liste déroulante **avec les informations d’identification** .

![Certificats clients][api-management-mutual-certificates]

Sélectionnez le certificat souhaité dans la liste déroulante de **certificat Client** . S’il existe plusieurs certificats que vous pouvez consulter l’objet ou les quatre derniers caractères de l’empreinte numérique comme indiqué dans la section précédente pour déterminer le certificat correct.

![Sélectionner un certificat][api-management-select-certificate]

Cliquez sur **Enregistrer** pour enregistrer la modification de configuration à l’API.

>Cette modification prend effet immédiatement, et les appels aux opérations de cette API utilise le certificat pour l’authentification sur le serveur principal.

![Enregistrer les modifications de l’API][api-management-save-api]

>Lorsqu’un certificat est spécifié pour l’authentification de passerelle pour le service principale d’une API, il fait alors partie de la stratégie de cette API et peut être affiché dans l’éditeur de stratégie.

![Stratégie de certificat][api-management-certificate-policy]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les autres méthodes pour sécuriser votre service de serveur principal, tel que HTTP partagé secrète authentification de base ou, voir la vidéo suivante.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[Azure entité API gestion reste API certificat]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Pour configurer l’authentification par certificat dans les sites Web Azure reportez-vous à cet article]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurer une API pour utiliser un certificat client pour l’authentification de passerelle]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 

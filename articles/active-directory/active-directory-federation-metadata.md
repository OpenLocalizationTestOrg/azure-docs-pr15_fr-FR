<properties
    pageTitle="Métadonnées de fédération AD Azure | Microsoft Azure"
    description="Cet article décrit le document de métadonnées de fédération Azure Active Directory publie des services qui acceptent les jetons Azure Active Directory."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="federation-metadata"></a>Métadonnées de fédération

Azure Active Directory (AD Azure) publie un document de métadonnées de fédération des services configuré pour accepter les jetons de sécurité Azure AD problèmes. Le format de document de métadonnées de fédération est décrite dans la [Version Web Services Federation Language (Web-Federation) 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), qui s’étend de [métadonnées pour la version 2.0 OASIS sécurité SAML Assertion Markup Language ()](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Spécifiques au client et points de terminaison de métadonnées indépendante du client

Azure AD publie des points de terminaison spécifiques au client et indépendant du client.

Points de terminaison spécifiques au client sont conçues pour un client particulier. Les métadonnées de fédération spécifiques au client incluent des informations sur le client, y compris les informations de l’émetteur et point de terminaison spécifiques au client. Les applications qui limiter l’accès à un seul client utilisent des points de terminaison de client spécifiques.

Points de terminaison de client indépendante fournissent des informations qui sont communes à tous les Azure AD clients. Ces informations s’applique aux clients hébergés sur *login.microsoftonline.com* et sont partagées par les clients. Points de terminaison de client indépendante sont recommandés pour les applications clients multiples, dans la mesure où ils ne sont pas associés à un client particulier.

## <a name="federation-metadata-endpoints"></a>Points de terminaison de métadonnées fédération

Azure AD publie des métadonnées de fédération en `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Pour les **points de terminaison spécifiques au client**, le `TenantDomainName` peut être un des types suivants :

- Un nom de domaine enregistré d’une annonce Azure du client, telles que : `contoso.onmicrosoft.com`.
- L’immuable client ID du domaine, tel que `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Pour les **points de terminaison de client indépendante**, la `TenantDomainName` est `common`. Ce document répertorie uniquement les éléments de métadonnées de fédération qui sont communes à tous les clients Azure AD qui sont hébergés en login.microsoftonline.com.

Par exemple, un point de terminaison spécifiques au client peut-être être `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Le point de terminaison client indépendante est [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Vous pouvez afficher le document de métadonnées de fédération en tapant cette URL dans un navigateur.

## <a name="contents-of-federation-metadata"></a>Contenu de fédération de métadonnées

La section suivante fournit les informations nécessaires par les services qui utilisent les jetons émis par Azure AD.

### <a name="entity-id"></a>ID de l’entité

La `EntityDescriptor` élément contient un `EntityID` attribut. La valeur de la `EntityID` attribut représente l’émetteur, c'est-à-dire le service jeton de sécurité (STS) qui a émis le jeton. Il est important valider l’émetteur lorsque vous recevez un jeton.

Les métadonnées suivante montre un exemple spécifiques au client `EntityDescriptor` élément avec une `EntityID` élément.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Vous pouvez remplacer l’ID de client dans le point de terminaison client indépendante avec votre ID client pour créer un client-spécifique `EntityID` valeur. La valeur résultante sera identique à l’émetteur de jetons. La stratégie permet à une application cliente multiples valider l’émetteur pour un client donné.

Les métadonnées suivante montre un exemple client indépendante `EntityID` élément. Veuillez noter que la `{tenant}` est un littéral, pas un espace réservé.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificats de signature de jetons

Lorsqu’un service reçoit un jeton émis par un client Azure AD, la signature du jeton doit être validée avec une clé de signature est publiée dans le document de métadonnées de fédération. Les métadonnées de fédération incluent la partie publique des certificats que les clients utilisent pour la signature de jetons. Les octets brutes certificat s’affichent dans le `KeyDescriptor` élément. Le jeton de certificat de signature est valide pour la signature uniquement lorsque la valeur de la `use` attribut est `signing`.

Un document de métadonnées de fédération publié par Azure AD peut avoir plusieurs clés de signature, par exemple quand Azure AD se prépare à mettre à jour le certificat de signature. Lorsqu’un document de métadonnées de fédération comprend plusieurs certificats, un service qui valide les jetons doit prendre en charge tous les certificats dans le document.

Les métadonnées suivante montre un exemple `KeyDescriptor` élément avec une clé de signature.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

La `KeyDescriptor` élément s’affiche à deux endroits dans le document de métadonnées fédération ; dans la section fédération-spécifiques au Web et la section SAML spécifique. Les certificats publiés dans les deux sections sera la même.

Dans la section spécifique Web-fédération, un lecteur de métadonnées Web-Federation lit les certificats à partir d’un `RoleDescriptor` élément avec le `SecurityTokenServiceType` type.

Les métadonnées suivante montre un exemple `RoleDescriptor` élément.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Dans la section SAML spécifiques, un lecteur de métadonnées Web-Federation lit les certificats à partir d’un `IDPSSODescriptor` élément.

Les métadonnées suivante montre un exemple `IDPSSODescriptor` élément.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Il n’existe pas de différences au format de certificats spécifiques au client et client indépendants.

### <a name="ws-federation-endpoint-url"></a>URL de point de terminaison de la fédération Web

Les métadonnées de fédération incluent l’URL est utilisations Azure AD pour unique connexion et déconnexion dans le protocole Web fédération unique. Ce point de terminaison s’affiche dans le `PassiveRequestorEndpoint` élément.

Les métadonnées suivante montre un exemple `PassiveRequestorEndpoint` élément pour un point de terminaison client spécifiques.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Du point de terminaison client indépendante, l’URL de la fédération Web apparaît dans le point de terminaison Web-Federation, comme le montre l’exemple suivant.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de point de terminaison de protocole SAML

Les métadonnées de fédération incluent l’URL par Azure AD pour unique connexion et déconnexion dans protocole SAML 2.0 unique. Ces points de terminaison s’affichent dans le `IDPSSODescriptor` élément.

L’URL de connexion et déconnexion s’affichent dans le `SingleSignOnService` et `SingleLogoutService` éléments.

Les métadonnées suivante montre un exemple `PassiveResistorEndpoint` un point de terminaison de client spécifiques.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

De même les points de terminaison pour les points de terminaison de protocole SAML 2.0 courants sont publiées dans les métadonnées de fédération client indépendante, comme le montre l’exemple suivant.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

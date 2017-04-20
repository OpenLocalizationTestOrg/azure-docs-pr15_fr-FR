<properties
    pageTitle="Authentification de Service à Service Azure AD à l’aide de OAuth2.0 | Microsoft Azure"
    description="Cet article décrit comment utiliser les messages HTTP pour implémenter l’authentification au service de l’aide du flux de grant OAuth2.0 client informations d’identification."
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

# <a name="service-to-service-calls-using-client-credentials"></a>Service pour les appels de service à l’aide des informations d’identification client

Du jeton 2.0 Client informations d’identification Grant flux permet à un service web (un *client confidentielles*) à utiliser ses propres informations d’identification pour authentifier lors de l’appel d’un autre service web, au lieu de l’emprunt d’identité d’un utilisateur. Dans ce scénario, le client est généralement un service web intermédiaire, d’un service de processus ou d’un site web.

## <a name="client-credentials-grant-flow-diagram"></a>Informations d’identification client accorder diagramme de flux

Le diagramme suivant explique comment les informations d’identification client accorder des flux fonctionne dans Azure Active Directory (AD Azure).

![Flux accorder des informations d’identification des clients OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. L’application cliente s’authentifie sur le point de terminaison d’émission du jeton Azure AD et les demandes d’un jeton d’accès.
2. Le point de terminaison d’émission du jeton Azure AD émet le jeton d’accès.
3. Le jeton d’accès est utilisé pour authentifier à la ressource sécurisée.
4. Données de la ressource sécurisée sont renvoyées à l’application web.

## <a name="register-the-services-in-azure-ad"></a>Enregistrer les Services dans Azure Active Directory

Enregistrer le service d’appel et le service de réception dans Azure Active Directory (AD Azure). Pour obtenir des instructions détaillées, voir [Ajout, mise à jour et suppression d’une application](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Demander un jeton d’accès

Pour demander un jeton d’accès, utilisez une requête HTTP POST au spécifique au client point de terminaison Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Demande de jeton d’accès au service de service

Une demande de service à service accès jeton contient les paramètres suivants.

| Paramètre | | Description |
|-----------|------|------------|
| response_type | Obligatoire | Spécifie le type de réponse demandé. Dans un flux accorder des informations d’identification de Client, la valeur doit être **client_credentials**.|
| identifiant_client | Obligatoire | Spécifie l’id de client Azure AD du service web appelant. Pour trouver l’ID de client de l’application appelant, dans le portail de gestion Azure et cliquez sur **Active Directory**, cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**.|
| client_secret | Obligatoire |  Entrer une clé enregistrée pour le service web d’appel dans Azure AD. Pour créer une clé, dans le portail de gestion Azure, cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**. |
| ressource | Obligatoire | Entrez l’URI ID d’application du service web réception. Pour trouver l’URI ID de l’application, dans le portail de gestion Azure, cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**. |

## <a name="example"></a>Exemple

Le billet HTTP suivante demande un jeton d’accès pour le service web https://service.contoso.com/. La `client_id` identifie le service web qui demande le jeton d’accès.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Réponse jeton d’accès au service-Service

Une réponse de succès contient une réponse JSON OAuth 2.0 avec les paramètres suivants.

| Paramètre   | Description |
|-------------|-------------|
|access_token |Le jeton d’accès demandés. Le service web appelant peut utiliser ce jeton pour s’authentifier auprès du service web de réception. |
|access_type  | Indique la valeur de type de jeton. Le seul type qui prend en charge Azure AD est **PORTEUR**. Pour plus d’informations sur les jetons porteur, voir la [OAuth 2.0 autorisation Framework : utilisation jeton porteur (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | Combien de temps le jeton d’accès est valide (en secondes).|
|expires_on   |Heure à laquelle le jeton d’accès arrive à expiration. La date est représentée par le nombre de secondes à partir du 1970-01-01T0:0:0Z UTC jusqu'à ce que le délai d’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons mis en cache. |
|ressource     | URI ID d’application du service web réception. |

## <a name="example"></a>Exemple

L’exemple suivant montre une réponse de succès à une demande d’un jeton d’accès à un service web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Voir aussi

* [OAuth 2.0 dans Azure Active Directory](active-directory-protocols-oauth-code.md)

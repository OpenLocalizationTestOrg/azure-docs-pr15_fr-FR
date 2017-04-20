<properties
    pageTitle="Se ins après plusieurs échecs"
    description="Un rapport qui indique les utilisateurs qui ont connecté correctement après que plusieurs consécutives échec se dans tentatives."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Signe-ins après plusieurs échecs
Ce rapport indique les utilisateurs qui ont connecté correctement après que plusieurs consécutives échec se dans tentatives. Causes possibles :

- Utilisateur a oublié son mot de passe</li><li>L’utilisateur est victime d’un mot de passe réussie deviner brute force attaque

Résultats de ce rapport montre le nombre de consécutives connexion échecs apportées avant le succès de se connecter et un horodatage associé à la première succès de se connecter.

**Paramètres de rapport**: vous pouvez configurer le nombre minimal de signe échecs consécutifs dans tentatives qui doivent se produire avant d’être affiché dans le rapport. Lorsque vous apportez des modifications à ce paramètre, il est important de noter que ces modifications ne seront pas appliquées à n’importe quel signer échec existant ins apparaissant actuellement dans votre état existant. Toutefois, ils seront appliquées à toutes les connexions futures. Modifications apportées à ce rapport peuvent uniquement être effectuées par les administrateurs sous licence.


![Se ins après plusieurs échecs](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

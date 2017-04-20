<properties
    pageTitle="Déployer une machine virtuelle à l’aide d’un mot de passe stockée dans l’archivage sécurisé Azure pile clé | Microsoft Azure"
    description="Découvrez comment déployer une machine virtuelle à l’aide d’un mot de passe stockée dans l’archivage sécurisé Azure pile clé"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Déployer une machine virtuelle en récupérant le mot de passe stockée dans l’archivage sécurisé clé

Lorsque vous avez besoin de passer d’une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que code secret utilisé dans un archivage sécurisé clé pile Azure et faire référence à la valeur dans d’autres modèles Azure le Gestionnaire de ressources. Vous incluez uniquement une référence aux données secrètes dans votre modèle afin que le mot de passe n’est jamais exposée. Vous n’avez pas besoin d’entrer manuellement la valeur pour le code secret chaque fois que vous déployez les ressources. Vous spécifiez les utilisateurs ou les identités de service peuvent accéder le code secret.

## <a name="reference-a-secret-with-static-id"></a>Faire référence à un code secret avec l’ID statique

Vous référencez le mot de passe à partir d’un fichier de paramètres qui passe des valeurs à votre modèle. Vous référencez le code secret en passant l’identificateur de ressource de l’archivage sécurisé clé et le nom du code secret. Dans cet exemple, le code secret l’archivage sécurisé clé doit déjà exister. Vous utilisez une valeur statique pour son ID de ressource.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]Le paramètre qui accepte le code secret doit être une *securestring*.

## <a name="next-steps"></a>Étapes suivantes
[Déployer une application exemple avec l’archivage sécurisé clé](azure-stack-kv-sample-app.md)

[Déployer une machine virtuelle avec un certificat de l’archivage sécurisé clé](azure-stack-kv-push-secret-into-vm.md)


<properties
    pageTitle="Procédure à suivre en cas d’un Azure service interruption de service affecte l’archivage sécurisé de clé Azure | Microsoft Azure"
    description="Découvrez comment procéder dans le cas d’une interruption de service Azure qui affecte l’archivage sécurisé de clé Azure."
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure redondance et disponibilité de l’archivage sécurisé clé

L’archivage sécurisé clé Azure propose plusieurs couches de redondance pour vous assurer que vos clés et secrets restent disponibles pour votre application même si les composants individuels du service échouent.

Le contenu de votre l’archivage sécurisé clé est répliqué dans la région et à une zone secondaire au moins 150 miles absent (e), mais dans le même emplacement géographique. Cette option conserve grande longévité de vos clés et secrets.

Si les composants individuels au sein du service de l’archivage sécurisé clés échouent, des composants de substitution dans la zone étape pour répondre à votre requête pour vous assurer qu’il n’existe aucune dégradation de fonctionnalité. Vous n’avez pas besoin aucune action déclencher ceci. Il s’effectue automatiquement et sera transparent pour l’utilisateur.

Dans l’événement rare qu’une région Azure entière n’est pas disponible, les requêtes que vous apportez de l’archivage sécurisé de clé Azure dans cette zone sont routés automatiquement (*basculé*) à une zone secondaire. Lorsque la zone primaire redevient disponible, demandes sont routés précédent (*bascule*) à la zone principale. Là encore, vous n’avez pas besoin aucune action, car cela s’effectue automatiquement.

Il existe quelques inconvénients importants :

* En cas de basculement de la région, il peut prendre quelques minutes pour que le service de basculer. Demandes qui sont effectuées pendant cette période peuvent échouer jusqu'à ce que le basculement est terminée.
* Après qu’un basculement est terminé, votre l’archivage sécurisé clé est en mode lecture seule. Demandes prises en charge dans ce mode sont :
 * Liste clés chambres fortes
 * Obtenir des propriétés de chambres fortes clés
 * Liste secrets
 * Obtenir des secrets
 * Touches de la liste
 * Obtenir des clés (propriétés de)
 * Chiffrer
 * Déchiffrer
 * Renvoyer à la ligne
 * Renvoi
 * Vérifier
 * Signe
 * Sauvegarde
* Une fois un basculement bascule, tous les types de requête (y compris les requêtes d’écriture lu *et* ) sont disponibles.

<properties
   pageTitle="Gérer les enregistrements DNS inverses pour vos services Azure à l’aide d’Azure infrastructure du langage commun | Microsoft Azure"
   description="Comment gérer les enregistrements DNS inverses ou enregistrements PTR pour les services Azure à l’aide de l’infrastructure du langage commun Azure dans le Gestionnaire de ressources"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Comment gérer les enregistrements DNS inverses pour vos services Azure à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validation d’enregistrement DNS inverse
Pour vous assurer qu'un tiers ne peut pas créer des enregistrements DNS inverses mappage à vos domaines DNS, Azure permet uniquement la création d’un enregistrement DNS inverse où une des opérations suivantes est vraie :

- Le « ReverseFqdn » est identique à « Nom de domaine complet » pour la ressource adresse IP publique pour laquelle elle a été spécifiée ou le nom « complet » pour n’importe quelle adresse IP publics dans le même abonnement, par exemple, « ReverseFqdn » est « contosoapp1.northus.cloudapp.azure.com. ».

- Le « ReverseFqdn » transférer résout le nom ou l’adresse IP de l’adresse IP pour laquelle il a été spécifié, ou à toute adresse IP publique « Nom de domaine complet » ou IP dans le même abonnement par exemple, « ReverseFqdn » est « app1.contoso.com. » qui est un alias CName pour « contosoapp1.northus.cloudapp.azure.com. »

Contrôles de validation sont exécutées uniquement lorsque la propriété DNS inverse d’une adresse IP publique est définie ou modifiée. Ré-validation périodique n’est pas exécutée.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Ajouter DNS inverse aux adresses IP Public existants
Vous pouvez ajouter DNS inverse à une adresse IP publique existante en utilisant le jeu d’adresses ip public azure réseau :

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Si vous souhaitez ajouter DNS inverse à une adresse IP publique existante qui n’a pas encore un nom DNS, vous devez également spécifier un nom DNS. Vous pouvez ajouter y parvenir à l’aide de l’ensemble de public ip réseau azure :

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Créer une adresse IP publique avec DNS inversée
Vous pouvez ajouter une nouvelle adresse IP Public avec la propriété DNS inverse spécifiée en utilisant le réseau azure public-ip créer :

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Affichage inversée DNS pour les adresses IP publiques existant
Vous pouvez afficher la valeur configurée pour une adresse IP publique existante à l’aide du diaporama public ip azure réseau :

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Supprimer DNS inverse d’adresses IP publiques existant
Vous pouvez supprimer une propriété DNS inverse d’une adresse IP Public existant à l’aide azure réseau public ip ensemble. Pour cela en définissant la valeur de propriété ReverseFqdn vide :

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

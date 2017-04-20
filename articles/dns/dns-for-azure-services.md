<properties
  pageTitle="À l’aide de DNS Azure avec d’autres services Azure | Microsoft Azure"
  description="Comprendre comment utiliser Azure DNS pour résoudre nom pour d’autres services Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>À l’aide de DNS Azure avec d’autres services Azure

Azure DNS est un hébergé gestion et nom de service de résolution DNS. Cela permet de créer des noms DNS publics pour les autres applications et services que vous avez déployé dans Azure. Créer un nom d’un service Azure dans votre domaine personnalisé est aussi simple que l’ajout d’un enregistrement du type correct pour votre service.

* Pour les adresses IP allouées dynamiquement, vous devez créer un enregistrement CNAME DNS qui correspond au nom DNS Azure créé pour votre service. Normes DNS vous empêchent d’utiliser un enregistrement CNAME pour le sommet zone.
* Pour les adresses IP statiques, vous pouvez créer un enregistrement DNS A à l’aide de n’importe quel nom, y compris un nom de _domaine naked_ au sommet zone.

Le tableau suivant présente les types d’enregistrements pris en charge pouvant être utilisées pour différents services Azure. Comme vous pouvez le voir dans cette table, Azure DNS prend uniquement en charge les enregistrements DNS pour les ressources réseau via Internet. Azure DNS ne peut pas être utilisé pour la résolution du nom des adresses internes, privées.

| Service Azure | Interface réseau | Description |
|---------------|-------------------|-------------|
| Passerelle d’application | Adresse IP publique frontale | Vous pouvez créer un enregistrement DNS A ou CNAME. |
| Équilibrage de charge | Adresse IP publique frontale | Vous pouvez créer un enregistrement DNS A ou CNAME. Équilibrage de charge peut avoir une adresse IP Public IPv6 qui est affectée dynamiquement. Par conséquent, vous devez créer un enregistrement CNAME pour une adresse IPv6. |
| Gestionnaire de trafic | Nom public | Vous ne pouvez créer un enregistrement CNAME qui correspond au nom trafficmanager.net affecté à votre profil le trafic Manager. Pour plus d’informations, voir [comment le trafic Manager fonctionne](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Service de nuage | Adresse IP publique | Pour les adresses IP statiques, vous pouvez créer un enregistrement DNS A. Pour les adresses IP allouées dynamiquement, vous devez créer un enregistrement CNAME qui correspond au nom _cloudapp.net_ . Cette règle s’applique aux machines virtuelles créés dans le portail classique, car ils sont déployés comme un service cloud. Pour plus d’informations, voir [configurer un nom de domaine personnalisé dans les Services en nuage](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Service d’application | Adresse IP externe | Pour les adresses IP externes, vous pouvez créer un enregistrement DNS A. Dans le cas contraire, vous devez créer un enregistrement CNAME qui correspond au nom azurewebsites.net. Pour plus d’informations, voir [mapper un nom de domaine personnalisé pour une application Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Machines virtuelles du Gestionnaire de ressources | Adresse IP publique | Machines virtuelles du Gestionnaire de ressources peuvent contenir des adresses IP publiques. Une machine virtuelle avec une adresse IP Public peut également être derrière un équilibrage de charge. Vous pouvez créer un enregistrement DNS A ou CNAME à l’adresse. Ce nom personnalisé peut être utilisé pour ignorer l’adresse VIP sur l’équilibrage de charge. |
| Machines virtuelles classiques | Adresse IP publique | Machines virtuelles classiques créé à l’aide de PowerShell ou infrastructure du langage commun peut être configuré avec une adresse virtuelle (réservée) statique ou dynamique. Vous pouvez créer un CNAME DNS ou un enregistrement, respectivement. |

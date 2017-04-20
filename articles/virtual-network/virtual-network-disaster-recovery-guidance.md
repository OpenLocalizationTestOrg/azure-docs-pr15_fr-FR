<properties
    pageTitle="Procédure à suivre en cas d’une interruption de service Azure ayant un impact sur les réseaux virtuels Azure | Microsoft Azure"
    description="Découvrez la procédure à suivre en cas d’une interruption de service Azure ayant un impact sur les réseaux virtuels Azure."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Réseau virtuel – continuité des activités

##<a name="overview"></a>Vue d’ensemble

Un réseau virtuel (VNet) est une représentation logique de votre réseau dans le cloud. Il vous permet de définir votre propre espace d’adressage IP privé et segmenter le réseau en sous-réseaux. VNets sert une limite de confiance pour héberger les ressources de votre cluster comme Machines virtuelles Azure et les Services de Cloud (rôles web/travail). Un VNet permet de communication IP privée directe entre les ressources hébergé qu’il contient. Un réseau virtuel peut également être lié à un réseau local via une des options hybride tel qu’une passerelle VPN ou ExpressRoute.
 
Un VNet est créé dans le cadre d’une région. Vous pouvez créer VNets avec le même espace d’adressage dans deux régions différentes (c'est-à-dire que nous est et l’ouest nous mais ne peut pas se connecter les uns directement). 

##<a name="business-continuity"></a>Continuité des activités

Vous pouvez rencontrer plusieurs manières que votre application peut être interrompue. Une région donnée pourrait être coupée complètement en raison d’un sinistre naturel ou un incident partiel en raison d’une défaillance de plusieurs appareils/services. L’impact sur le service VNet est différent dans chacune de ces situations.

**Q : que faire en cas de panne pour une région entière ? par exemple, si une région est complètement démarcation en raison de sinistre naturel ? Que se passe-t-il pour les réseaux virtuels hébergé dans la région ?**

R : le réseau virtuelle et les ressources dans la région concernée reste inaccessible pendant la période de l’interruption de service.

![Diagramme de réseau virtuel simple](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q : que faire pour faire recréer le même réseau virtuel dans une zone différente ?**

R : virtual Network (VNet) est assez léger ressource. Vous pouvez appeler API Azure pour créer un VNet avec le même espace d’adressage dans une zone différente. Pour recréer le même environnement qui a été présent dans la région concernée, vous devez effectuer des appels d’API pour redéployez votre Cloud Services (rôles web/travail) et les Machines virtuelles que vous aviez. Vous devrez également tourner une passerelle VPN et vous connecter à votre réseau local, si vous aviez connectivité locaux (par exemple, comme dans un déploiement hybride).

Les instructions pour la création d’un VNet sont trouvent [ici](./virtual-networks-create-vnet-arm-pportal.md). 

**Q : une copie d’un VNet dans une région donnée peut être recréé dans une autre région avance ?**

R : Oui, vous pouvez créer deux VNets à l’aide de la même espace d’adressage IP privé et ressources dans deux régions différentes à l’avance. Si un client a été d’hébergement ouvert services dans la VNet sur internet, ils peuvent configurer le trafic Manager à geo-acheminer le trafic vers la zone qui est actif. Cependant, un client ne peut pas se connecter deux VNets avec le même espace d’adressage à leur réseau local sous peine de causer des problèmes de routage. Au moment de sinistre et perte d’un VNet dans une zone géographique, un client peut se connecter à l’autre VNet dans les zones disponibles avec des correspondants espace d’adressage à leur réseau local.

Les instructions pour la création d’un VNet sont trouvent [ici](./virtual-networks-create-vnet-arm-pportal.md).

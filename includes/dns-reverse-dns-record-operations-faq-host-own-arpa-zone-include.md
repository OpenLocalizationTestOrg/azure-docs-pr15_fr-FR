<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>FAQ - qui héberge votre zone ARPA dans le système DNS Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Puis-je héberger zones ARPA pour mon blocs IP affectée par le fournisseur de services Internet sur DNS Azure ?
Oui. Qui héberge les zones ARPA pour vos propres plages d’adresses IP dans le système DNS Azure est entièrement pris en charge.

[Créez](dns-getstarted-create-dnszone.md)simplement la zone dans le système DNS Azure, puis travailler avec votre fournisseur de services Internet pour [déléguer la zone](dns-domain-delegation.md).  Vous pouvez ensuite gérer les enregistrements PTR pour chaque inversée de la même manière que d’autres types d’enregistrements.

Vous pouvez également [Importer une zone de recherche inversée existante à l’aide de l’infrastructure du langage commun Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Combien coûte d’hébergement mon coût zone ARPA ?
Bloc dans le système DNS Azure qui héberge la zone ARPA pour votre adresse IP affectée par le fournisseur de services Internet est facturée au [taux standard de DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puis-je héberger zones ARPA pour les adresses IPv4 et IPv6 dans le système DNS Azure ?
Oui.
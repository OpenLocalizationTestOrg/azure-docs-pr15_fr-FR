## <a name="what-is-reverse-dns"></a>Quelles sont les DNS inverse ?

Enregistrements DNS conventionnelles activer un mappage à partir d’un nom DNS (par exemple, « www.contoso.com ») à une adresse IP (par exemple, 64.4.6.100).  Le système DNS inverse permet la traduction d’une adresse IP (64.4.6.100) vers un nom (« www.contoso.com »).

Enregistrements DNS inverses sont utilisés dans diverses situations. Par exemple, les enregistrements DNS inverses sont largement utilisés dans la lutte contre le courrier indésirable en vérifiant l’expéditeur d’un message électronique.  Serveur de messagerie récepteur récupérer l’enregistrement DNS inverse de l’adresse IP du serveur d’envoi et vérifiez si cet hôte est autorisé à envoyer un courrier électronique du domaine d’origine. (Notez toutefois que [Azure calculer les services ne prennent pas en charge envoi messages électroniques à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Comment inversée DNS works

Enregistrements DNS inverses sont hébergés dans les zones DNS spéciales, appelés zones « ARPA ».  Ces zones forment une hiérarchie DNS distincte en parallèle avec la hiérarchie classique hébergeant des domaines tel que « contoso.com ».

Par exemple, l’enregistrement DNS « www.contoso.com » est activée à l’aide d’un enregistrement DNS « A » avec le nom « www » dans la zone contoso.com.  Cet enregistrement A pointe vers l’adresse IP correspondante, dans ce cas 64.4.6.100.  La recherche inversée est implémentée séparément, à l’aide d’un enregistrement « PTR » nommé « 100 » dans la zone « 6.4.64.in-addr.arpa » (Notez que les adresses IP sont annulées dans les zones ARPA).  Cet enregistrement PTR, si elle a été configurée correctement, pointe vers le nom www.contoso.com.

Lorsqu’une organisation est affectée à un bloc d’adresse IP, ils également acquièrent droite pour gérer la zone ARPA correspondante. Les zones ARPA correspondant aux blocs d’adresses IP utilisées par Azure sont hébergés et gérés par Microsoft. Votre fournisseur de services Internet peut héberger la zone ARPA pour vos propres adresses IP pour vous, ou vous autorise héberger la zone ARPA dans un service DNS de votre choix, par exemple Azure DNS.

>[AZURE.NOTE] Les recherches DNS directes et inversées sont implémentées en hiérarchies DNS distinctes, en parallèle. La recherche pour « www.contoso.com » inversée **pas** hébergé dans la zone « contoso.com », au lieu de cela, il est hébergé dans la zone ARPA pour le bloc d’adresse IP correspondant.

Pour plus d’informations sur les DNS inverses, voir [Inversée DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Prise en charge Azure pour DNS inversée

Azure prend en charge deux scénarios distincts relatives pour inverser DNS :

1. Qui héberge la zone ARPA correspondant à votre bloc d’adresse IP.
2. Et vous pouvez configurer l’enregistrement DNS inverse pour l’adresse IP affectée à votre service Azure.

Pour prendre en charge le DNS ancien, Azure peut servir pour héberger vos zones ARPA et gérer les enregistrements PTR pour chaque inversée DNS.  Le processus de création de la zone ARPA, la configuration de la délégation et la configuration des enregistrements PTR est identique à celle des zones DNS standard.  Les seules différences sont que la délégation doit être configurée via votre fournisseur de services Internet au lieu de votre bureau d’enregistrement DNS, et seulement le type d’enregistrement PTR doit être utilisé.

Pour prendre en charge ce dernier, Azure permet de vous permettent de configurer la recherche inversée pour les adresses IP allouées à votre service.  Cette recherche inversée est configurée par Azure comme enregistrement PTR dans la zone ARPA correspondante.  Ces zones ARPA, correspondant à toutes les plages d’adresses IP utilisées par Azure, sont hébergés par Microsoft. **Le reste de cet article décrit ce scénario en détail.**

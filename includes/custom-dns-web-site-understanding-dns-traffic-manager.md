Le système DNS (Domain Name) est utilisé pour identifier les éléments sur internet. Par exemple, lorsque vous entrez une adresse dans votre navigateur, ou cliquez sur un lien dans une page web, elle utilise DNS pour traduire du domaine en une adresse IP. L’adresse IP est quelque sorte une adresse postale, mais il n’est pas très humaine employez. Par exemple, il est beaucoup plus facile à retenir un nom DNS tel que **contoso.com** une largeur supérieure à retenir une adresse IP que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur les *enregistrements*. Enregistrements associent un *nom*tel que **contoso.com**, une adresse IP ou un autre nom DNS. Lorsqu’une application, par exemple un navigateur web, recherche d’un nom dans le système DNS, il trouve l’enregistrement et utilise quelle que soit elle pointe vers l’adresse. Si la valeur qu’il pointe vers une adresse IP, le navigateur utilise cette valeur. Si elle pointe vers un autre nom DNS, l’application doit faire résolution à nouveau. Pour finir, toute la résolution de nom se termine par une adresse IP.

Lorsque vous créez un site Web Azure, un nom DNS est automatiquement affecté au site. Ce nom prend la forme de ** &lt;yoursitename&gt;. azurewebsites.net**. Lorsque vous ajoutez votre site Web comme un point de terminaison Azure le trafic Manager, votre site Web est ensuite accessible via la ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domaine.

> [AZURE.NOTE] Lorsque votre site Web est configuré comme point de terminaison le trafic Manager, vous allez utiliser la **. trafficmanager.net** adresse lors de la création des enregistrements DNS.

> Vous ne pouvez utiliser les enregistrements CNAME avec le Gestionnaire de trafic

Il existe également plusieurs types d’enregistrements, chacune avec leurs fonctions et limitations, mais les sites Web configurés pour comme points de terminaison du trafic Manager, nous uniquement en charge d’un ; Enregistrements *CNAME* .

###<a name="cname-or-alias-record"></a>Enregistrement CNAME ou Alias

Un enregistrement CNAME associe un nom DNS *spécifique* , par exemple **mail.contoso.com** ou **www.contoso.com**, un autre nom de domaine (canonique). Dans le cas d’Azure sites Web à l’aide du trafic Manager, le nom de domaine canonique est la ** &lt;myapp >. trafficmanager.net** nom de domaine de votre profil le trafic Manager. Une fois créé, l’enregistrement CNAME crée un alias pour la ** &lt;myapp >. trafficmanager.net** nom de domaine. L’entrée CNAME résoudra à l’adresse IP de votre ** &lt;myapp >. trafficmanager.net** nom de domaine automatiquement, donc si l’adresse IP du site Web est modifiée, vous n’avez pas à exécuter l’action.

Une fois que le trafic atteint dans le Gestionnaire de trafic, il achemine puis le trafic vers votre site Web, à l’aide de la méthode pour qu'il est configuré d’équilibrage de charge. Il s’agit complètement transparente pour les visiteurs de votre site Web. Ils verront uniquement le nom de domaine personnalisé dans leur navigateur.

> [AZURE.NOTE] Certains bureaux autorise uniquement mapper des sous-domaines lors de l’utilisation d’un enregistrement CNAME, par exemple **www.contoso.com**et non les noms de racine, tel que **contoso.com**. Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d’enregistrement, <a href="http://en.wikipedia.org/wiki/CNAME_record">l’entrée Wikipédia dans l’enregistrement CNAME</a>ou le document <a href="http://tools.ietf.org/html/rfc1035">IETF de noms de domaine - implémentation et spécification</a> .

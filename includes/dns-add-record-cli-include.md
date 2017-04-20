#### <a name="create-an-a-record-set-with-single-record"></a>Créer un enregistrement A avec un enregistrement unique

Pour créer un jeu d’enregistrements, utilisez `azure network dns record-set create`. Spécifier le groupe de ressources, nom de la zone, jeu d’enregistrements nom relatif, type d’enregistrement et temps de vie (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Après avoir créé la A enregistrer ensemble, ajoutez l’adresse IPv4 à l’enregistrement avec la valeur `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Créer un enregistrement AAAA définir avec un enregistrement unique

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Créer un enregistrement CNAME définir avec un enregistrement unique

Enregistrements CNAME autorisent uniquement une valeur de chaîne unique.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Créer un enregistrement MX définir avec un enregistrement unique

Dans cet exemple, nous utilisons le nom du jeu d’enregistrements "@" à créer l’enregistrement MX au sommet zone (dans ce cas, « contoso.com »). Il s’agit courant pour les enregistrements MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Créer un enregistrement de serveur de noms avec un enregistrement unique

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Créer un enregistrement PTR définir avec un enregistrement unique  
Dans ce cas « mon-arpa-zone.com' représente la zone ARPA représentant votre plage IP.  Chaque enregistrement PTR dans cette zone correspond à une adresse IP au sein de cette plage IP.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Créer un enregistrement SRV définir avec un enregistrement unique

Si vous créez un enregistrement SRV dans la racine de la zone, vous pouvez spécifier « _service » et « _protocol » dans le nom de l’enregistrement. Il est inutile d’inclure "@" dans le nom de l’enregistrement.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Créer un enregistrement TXT définir avec enregistrement unique

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"

## <a name="about-records"></a>À propos des enregistrements

Chaque enregistrement DNS a un nom et un type. Enregistrements sont organisées en différents types selon les données qu’ils contiennent. Type le plus courant est un enregistrement « A » qui associe un nom à une adresse IPv4. Un autre type est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

DNS Azure prend en charge tous les courantes types d’enregistrements DNS, y compris A, AAAA, CNAME, MX, NS, PTR, SOA, SRV et TXT. Notez que :
- Jeux d’enregistrements SOA est automatiquement créés dans chaque zone, ils ne peuvent pas être créés séparément.
- Enregistrements SPF doivent être créées en utilisant le type d’enregistrement TXT. Pour plus d’informations, consultez [cette page](http://tools.ietf.org/html/rfc7208#section-3.1).

Dans le système DNS Azure, les enregistrements sont spécifiées en utilisant des noms relatives. Un nom de domaine « complet » (FQDN) inclut le nom de la zone, tandis que n’est pas le cas d’un nom « relatif ». Par exemple, le nom de l’enregistrement relatif « www » dans la zone « contoso.com » donne le nom complet de l’enregistrement www.contoso.com.

## <a name="about-record-sets"></a>À propos des jeux d’enregistrements

Parfois, vous devez créer plusieurs enregistrements DNS avec un nom et type donnés. Par exemple, supposons que le site web « www.contoso.com » est hébergé sur deux différentes adresses IP. Le site Web nécessite deux enregistrements A, une pour chaque adresse IP. Il s’agit d’un exemple d’un jeu d’enregistrements :

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

DNS Azure gère les enregistrements DNS à l’aide de jeux d’enregistrements. Un jeu d’enregistrements est l’ensemble des enregistrements DNS dans une zone qui ont le même nom et sont du même type. La plupart des jeux d’enregistrements contiennent un seul enregistrement, mais exemples comme celui-ci, dont un jeu d’enregistrements contient plusieurs enregistrements, ne sont pas rares.

Source de nom et CNAME jeux d’enregistrements sont des exceptions. Les normes DNS ne permettent pas plusieurs enregistrements avec le même nom pour les types suivants.

La durée de vie, ou TTL, indique la durée pendant laquelle chaque enregistrement est mis en cache par les clients avant de nouveau interrogée. Dans cet exemple, la durée de vie est 3 600 secondes ou 1 heure. La durée de vie spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement, pour la même valeur est utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.

#### <a name="wildcard-record-sets"></a>Ensembles d’enregistrements génériques

DNS Azure prend en charge les [enregistrements génériques](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Ils sont renvoyés pour une requête avec un nom correspondant (sauf s’il existe une correspondance plus proche à partir d’un jeu d’enregistrements non génériques). Jeux d’enregistrements génériques est pris en charge pour tous les types d’enregistrements à l’exception NS et SOA.  

Pour créer un jeu d’enregistrements génériques, utilisez le nom du jeu d’enregistrements «\*». Ou, utilisez un nom avec l’étiquette «\*«, par exemple, »\*.foo ».

#### <a name="cname-record-sets"></a>Jeux d’enregistrements CNAME

Jeux d’enregistrements CNAME ne peut pas coexister avec d’autres jeux d’enregistrements du même nom. Par exemple, vous ne pouvez pas créer un enregistrement CNAME définie avec le nom relatif « www » et un enregistrement A avec le nom « www » relatif en même temps. Étant donné que le sommet fuseau (nom = ‘@’) contient toujours le NS et SOA jeux qui ont été créés lors de la zone de la création d’enregistrements, vous ne pouvez pas créer un enregistrement CNAME définir au sommet zone. Ces contraintes proviennent les normes DNS et ne sont pas les limitations du service DNS Azure.

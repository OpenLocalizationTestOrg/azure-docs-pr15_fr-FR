Une zone DNS est utilisée pour héberger les enregistrements DNS pour un domaine particulier. Avant de commencer l’hébergement de votre domaine, vous devez créer une zone DNS. Tout enregistrement DNS créé pour un domaine particulier sera à l’intérieur d’une zone DNS pour le domaine. 

Par exemple, le domaine « contoso.com » peut contenir un nombre d’enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). 


## <a name="names"></a>À propos des noms de zone DNS
 
- Le nom de la zone doit être unique dans le groupe de ressources et la zone ne doit pas déjà exister. Dans le cas contraire, l’opération échouera.

- Le même nom de la zone peut être réutilisé dans un groupe de ressources différent ou un autre abonnement Azure. 

- Dans laquelle plusieurs zones partagent le même nom, chaque instance doivent être affectée adresses serveur de noms différentes, et qu’une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, voir [délégué un domaine à Azure DNS](../articles/dns/dns-domain-delegation.md).
<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>FAQ - inversée DNS pour votre adresse IP affectée Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Combien inverser le coût des enregistrements DNS ?
Ils sont gratuits !  Il est sans frais supplémentaires pour les enregistrements DNS inverses ou des requêtes.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Les enregistrements DNS inverses pour mon adresse affectée Azure Public IP résoudra à partir d’internet ?
Oui. Une fois que vous définissez la propriété DNS inverse pour votre adresse IP publique, Azure gère toutes les zones DNS nécessaires pour s’assurer que l’enregistrement DNS inverse résout pour tous les utilisateurs d’internet et délégation DNS.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Un enregistrement DNS inverse par défaut est créé pour mes adresses IP Public ?
Non. DNS inverse est une fonctionnalité d’inscription. Aucun enregistrement DNS inverse par défaut n’est créées si vous choisissez de ne pas les configurer.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Quel est le format du nom de domaine complet (FQDN) ?
Noms de domaine complets sont spécifiées dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Que se passe-t-il si les contrôles de validation pour le DNS inverse que j’ai spécifié échouent ?
Où le contrôle pour DNS inversée vérifie échouer, l’opération de gestion du service échouera. Corrigez la valeur DNS inverse selon les besoins et réessayez.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Puis-je gérer DNS inverse pour mon site Web Azure ?
DNS inverse n’est pas prise en charge pour les sites Web Azure. DNS inverse est pris en charge pour le Machines virtuelles Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Puis-je configurer plusieurs enregistrements DNS inverses pour mon adresse IP Public ?
Non. Azure prend en charge un enregistrement DNS inverse unique pour chaque adresse IP publique. Chaque adresse IP publique toutefois peut avoir son propre enregistrement DNS inverse.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Puis-je configurer des enregistrements DNS inverses pour une adresse IP Public IPv6 ?
Non.  Pour l’instant, les enregistrements DNS inverses sont prises en charge pour les adresses IP IPv4 publiques uniquement.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Puis-je configurer un enregistrement DNS inverse pour mon adresse IP publique sans avoir un DomainNameLabel spécifié ?
Non. Pour tirer parti des enregistrements DNS inverses pour vos adresses IP Public, vous devez spécifier la propriété DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Puis-je envoyer les messages électroniques vers les domaines externes à partir de mes services Azure calculer ?
Non. [Azure calculer les services ne prennent pas en charge envoi messages électroniques à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).

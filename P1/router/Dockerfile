# Image router : base officelle FRRouting (base sur Alpine, contient busbox)
# FRR fournit zebra (gestion de routes) et les deamons des protocoles de routage
FROM frrouting/frr:latest

# Active les deamons BGP, OSPF et IS-IS dans /etc/frr/daemons
# (par defaut seul zebra est actif : on remplace "=no" par "=yes")
RUN sed -i -e 's/bgpd=no/bgpd=yes/' \
            -e 's/ospfd=no/ospfd=yes/' \
            -e 's/isisd=no/isisd=yes/' \
            /etc/frr/daemons

# Creer le fichier de config de vtysh (vide) pour supprimer l'avertissement au lancement
RUN touch /etc/frr/vtysh.conf

# Au run du conteneur: demarre FRR et deamons, puis ouvre un shell
# pour pouvoir acceder au routeur depuis la console GNS3
CMD ["/bin/sh", "-c", "/usr/lib/frr/frrinit.sh start && /bin/sh"]
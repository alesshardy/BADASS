# P2 — Découverte d'un VXLAN

Réutilise les **deux mêmes images** que la P1 (host Alpine + routeur FRR).
Aucune IP n'est configurée par défaut : tout se fait dans les consoles GNS3.

## Topologie

```
                Switch_apintus
                /           \
   routeur_apintus-1      routeur_apintus-2
    eth1 10.1.1.1/24       eth1 10.1.1.2/24      <- underlay (face au switch)
    eth0 |                 eth0 |                <- face au host (dans br0)
  host_apintus-1          host_apintus-2
    eth0 30.1.1.1/24       eth0 30.1.1.2/24      <- overlay (VXLAN)
```

- **VNI** : 10 — **interface** : `vxlan10` — **bridge** : `br0` — **port UDP** : 4789
- **Underlay** `10.1.1.0/24` : transport entre les VTEP (les routeurs)
- **Overlay** `30.1.1.0/24` : les hosts, qui se croient sur le même LAN

## Équipements

| Fichier | Équipement |
|---|---|
| `_apintus-1_s` / `_apintus-1_g` | routeur 1 (VTEP) : statique (`s`) / group multicast (`g`) |
| `_apintus-2_s` / `_apintus-2_g` | routeur 2 (VTEP) : statique (`s`) / group multicast (`g`) |
| `_apintus-1_host` / `_apintus-2_host` | hosts terminaux (PC 1 / PC 2) |

`Switch_apintus` est un simple switch Ethernet GNS3 : aucune config.

## Ordre de mise en place

1. Monter la topologie dans GNS3 avec les deux images de la P1.
2. **Mode statique** : coller `_apintus-1_s` puis `_apintus-2_s` dans les
   consoles des routeurs.
3. Coller `_apintus-1_host` et `_apintus-2_host` dans les hosts.
4. Test : depuis `host_apintus-1`, `ping 30.1.1.2`.
5. **Mode multicast** : repartir propre (ou recréer `vxlan10`) et coller les
   versions `_apintus-1_g` / `_apintus-2_g` à la place → découverte dynamique des VTEP.

## Vérifications pour la défense

- `ip -d link show vxlan10` → VNI 10, `remote` (statique) ou `group 239.1.1.1` (multicast)
- `brctl showmacs br0` → table des MAC apprises sur `br0`
- Wireshark sur le lien routeur ↔ switch → trame **VXLAN**, *VXLAN Network Identifier (VNI): 10*, ICMP encapsulé

## Export

Menu GNS3 : *File → Export portable project*, **Zip compression** + **Include base images**.
Enregistrer `P2.gns3project` dans ce dossier `P2/`.

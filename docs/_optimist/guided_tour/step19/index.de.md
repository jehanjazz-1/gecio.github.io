---
title: "19: Unsere Instanz lernt IPv6"
lang: de
permalink: /optimist/guided_tour/step19/
nav_order: 1190
parent: Guided Tour
---

Schritt 19: Unsere Instanz lernt IPv6
=====================================

Vorwort
-------

Nachdem im letzten Schritt die Instanz mit einer öffentlichen
IPv4 Adresse versehen wurde und diese auch per SSH erreichbar ist, wird es nun
Zeit die Instanz selber anzupassen.

Dafür nutzen wir in diesem Schritt CloudConfig und passen auch die Security Group an.

CloudConfig
-----------

Ist eine Ressource und wird daher auch unter `resources` geführt. (Typ =
`OS::HEAT::CloudConfig`)

Es gibt sehr viele Möglichkeiten, was alles in einer Instanz mit
CloudConfig bearbeiten werden kann.

Im diesem Schritt beschäftigen wir uns damit, alles notwendige für IPv6
vorzubereiten.

Der Start macht hierbei das erstellen der entsprechenden Dateien mit dem
notwendigen Inhalt, den wir bereits aus Schritt 11: Zugriff aus
dem Internet vorbereiten: Wir ergänzen IPv6 kennen und nutzen CloudConfig
in der `cloud_config` den Befehl `write_files`:

```yaml
heat_template_version: 2014-10-16

parameters:
    key_name:
        type: string
    public_network_id:
        type: string
        default: provider

resources:
    Instanz:
        type: OS::Nova::Server
        properties:
            key_name: { get_param: key_name }
            image: Ubuntu 16.04 Xenial Xerus - Latest
            flavor: m1.small
            networks:
                - port: { get_resource: Port }
 
    Instanz-Config:
        type: OS::Heat::CloudConfig
        properties:
            cloud_config:
                write_files:
                    - path: /etc/dhcp/dhclient6.conf
                      content: "timeout 30;"
                    - path: /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
                      content: "network: {config: disabled}"
                    - path: /etc/network/interfaces.d/lo.cfg
                      content: |
                        auto lo
                        iface lo inet loopback
                    - path: /etc/network/interfaces.d/ens3.cfg
                      content: |
                        iface ens3 inet6 auto
                            up sleep 5
                            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.ens3.leases -v ens3 || true

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk

    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }
            security_groups: { get_resource: Sec_SSH }

    Router:
        type: OS::Neutron::Router
        properties:
            external_gateway_info: { "network": { get_param: public_network_id } }
            name: BeispielRouter

    Subnet:
        type: OS::Neutron::Subnet
        properties:
            name: BeispielSubnet
            dns_nameservers:
                - #MussNochEingetragenWerden
                - #MussNochEingetragenWerden
            network: { get_resource: Netzwerk }
            ip_version: 4
            cidr: 10.0.0.0/24
            allocation_pools:
            - { start: 10.0.0.10, end: 10.0.0.250 }

    Router_Subnet_Bridge:
        type: OS::Neutron::RouterInterface
        depends_on: Subnet
        properties:
            router: { get_resource: Router }
            subnet: { get_resource: Subnet }

    Floating_IP:
        type: OS::Neutron::FloatingIP
        properties:
            floating_network: { get_param: public_network_id }
            port_id: { get_resource: Port }

    Sec_SSH:
        type: OS::Neutron::SecurityGroup
        properties:
            description: Diese Security Group erlaubt den eingehenden SSH-Traffic über Port22 und ICMP
            name: Ermöglicht SSH (Port22) und ICMP
            rules:
                - { direction: ingress, remote_ip_prefix: 0.0.0.0/0, port_range_min: 22, port_range_max: 22, protocol:tcp }
                - { direction: ingress, remote_ip_prefix: 0.0.0.0/0, protocol: icmp }
```

Wir haben die Dateien erstellt und den entsprechenden Inhalt eingefügt.

Wie in [Schritt 11: Zugriff aus dem Internet vorbereiten: Wir ergänzen IPv6](/optimist/guided_tour/step11/) beschrieben, ist es noch notwendig das Interface mit dem Befehl `runcmd` neu zustarten.

```yaml
heat_template_version: 2014-10-16

parameters:
    key_name:
        type: string
    public_network_id:
        type: string
        default: provider

resources:
    Instanz:
        type: OS::Nova::Server
        properties:
            key_name: { get_param: key_name }
            image: Ubuntu 16.04 Xenial Xerus - Latest
            flavor: m1.small
            networks:
                - port: { get_resource: Port }

    Instanz-Config:
        type: OS::Heat::CloudConfig
        properties:
            cloud_config:
                write_files:
                    - path: /etc/dhcp/dhclient6.conf
                      content: "timeout 30;"
                    - path: /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
                      content: "network: {config: disabled}"
                    - path: /etc/network/interfaces.d/lo.cfg
                      content: |
                        auto lo
                        iface lo inet loopback
                    - path: /etc/network/interfaces.d/ens3.cfg
                      content: |
                        iface ens3 inet6 auto
                            up sleep 5
                            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.ens3.leases -v ens3 || true
                runcmd:
                    - [ ifdown, ens3]
                    - [ ifup, ens3]

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk

    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }
            security_groups: { get_resource: Sec_SSH }

    Router:
        type: OS::Neutron::Router
        properties:
            external_gateway_info: { "network": { get_param: public_network_id } }
            name: BeispielRouter

    Subnet:
        type: OS::Neutron::Subnet
        properties:
            name: BeispielSubnet
            dns_nameservers:
                - 8.8.8.8
                - 8.8.4.4
            network: { get_resource: Netzwerk }
            ip_version: 4
            cidr: 10.0.0.0/24
            allocation_pools:
            - { start: 10.0.0.10, end: 10.0.0.250 }

    Router_Subnet_Bridge:
        type: OS::Neutron::RouterInterface
        depends_on: Subnet
        properties:
            router: { get_resource: Router }
            subnet: { get_resource: Subnet }

    Floating_IP:
        type: OS::Neutron::FloatingIP
        properties:
            floating_network: { get_param: public_network_id }
            port_id: { get_resource: Port }

    Sec_SSH:
        type: OS::Neutron::SecurityGroup
        properties:
            description: Diese Security Group erlaubt den eingehenden SSH-Traffic über Port22 und ICMP
            name: Ermöglicht SSH (Port22) und ICMP
            rules:
                - { direction: ingress, remote_ip_prefix: 0.0.0.0/0, port_range_min: 22, port_range_max: 22, protocol:tcp }
                - { direction: ingress, remote_ip_prefix: 0.0.0.0/0, protocol: icmp }
```

Im letzten Schritt passen wir die Security Group an, damit auch ein Zugriff über IPv6 möglich ist.

```yaml
heat_template_version: 2014-10-16

parameters:
    key_name:
        type: string
    public_network_id:
        type: string
        default: provider

resources:
    Instanz:
        type: OS::Nova::Server
        properties:
            key_name: { get_param: key_name }
            image: Ubuntu 16.04 Xenial Xerus - Latest
            flavor: m1.small
            networks:
                - port: { get_resource: Port }

    Instanz-Config:
        type: OS::Heat::CloudConfig
        properties:
            cloud_config:
                write_files:
                    - path: /etc/dhcp/dhclient6.conf
                      content: "timeout 30;"
                    - path: /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
                      content: "network: {config: disabled}"
                    - path: /etc/network/interfaces.d/lo.cfg
                      content: |
                        auto lo
                        iface lo inet loopback
                    - path: /etc/network/interfaces.d/ens3.cfg
                      content: |
                        iface ens3 inet6 auto
                            up sleep 5
                            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.ens3.leases -v ens3 || true
                runcmd:
                    - [ ifdown, ens3]
                    - [ ifup, ens3]

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk

    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }
            security_groups: { get_resource: Sec_SSH }

    Router:
        type: OS::Neutron::Router
        properties:
            external_gateway_info: { "network": { get_param: public_network_id } }
            name: BeispielRouter

    Subnet:
        type: OS::Neutron::Subnet
        properties:
            name: BeispielSubnet
            dns_nameservers:
                - 8.8.8.8
                - 8.8.4.4
            network: { get_resource: Netzwerk }
            ip_version: 4
            cidr: 10.0.0.0/24
            allocation_pools:
            - { start: 10.0.0.10, end: 10.0.0.250 }

    Router_Subnet_Bridge:
        type: OS::Neutron::RouterInterface
        depends_on: Subnet
        properties:
            router: { get_resource: Router }
            subnet: { get_resource: Subnet }

    Floating_IP:
        type: OS::Neutron::FloatingIP
        properties:
            floating_network: { get_param: public_network_id }
            port_id: { get_resource: Port }

    Sec_SSH:
        type: OS::Neutron::SecurityGroup
        properties:
            description: Diese Security Group erlaubt den eingehenden SSH-Traffic über Port22 und ICMP
            name: Ermöglicht SSH (Port22) und ICMP
            rules:
                - { direction: ingress, remote_ip_prefix: 0.0.0.0/0, port_range_min: 22, port_range_max: 22, protocol:tcp }
                - { direction: ingress, remote_ip_prefix: 0.0.0.0/0, protocol: icmp }
                - { direction: ingress, remote_ip_prefix: "::/0", port_range_min: 22, port_range_max: 22, protocol: tcp, ethertype: IPv6 }
                - { direction: ingress, remote_ip_prefix: "::/0", protocol: ipv6-icmp, ethertype: IPv6 }
```

Abschluss
---------

Wir haben nun die Möglichkeit Instanzen per Cloud-Init anzupassen und IPv6 nutzbar gemacht.

Im nächsten und letzten Schritt werden wir mehrere Instanzen per Heat starten.

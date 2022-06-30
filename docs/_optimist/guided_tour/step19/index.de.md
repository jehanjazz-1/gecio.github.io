---
title: "19: Unsere Instanz lernt IPv6"
lang: de
permalink: /optimist/guided_tour/step19/
nav_order: 1190
parent: Guided Tour
---

# Schritt 19: Unsere Instanz lernt IPv6

## Einführung

Nachdem im letzten Schritt die Instanz mit einer öffentlichen
IPv4 Adresse versehen wurde und diese auch mit SSH erreichbar ist, passen wir in diesem Schritt die Instanz an.

Dafür nutzen wir CloudConfig und passen auch die Security Group an.

## CloudConfig

`CloudConfig` ist eine Ressource und wird daher unter `resources` geführt (Typ =
`OS::HEAT::CloudConfig`).

In einer Instanz kann mit
`CloudConfig` alles mögliche bearbeitet werden.
Im diesem Schritt nutzen wir die Ressource, um alles notwendige für IPv6 vorzubereiten.

Wir beginnen mit dem Erstellen der entsprechenden Dateien. Dazu nutzen wir den Inhalt aus [Schritt 11](/optimist/guided_tour/step11/) und  in `cloud_config` den Befehl `write_files`:

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

Wir haben nun die Dateien erstellt und den entsprechenden Inhalt eingefügt.

Wie in [Schritt 11](/optimist/guided_tour/step11/) beschrieben, muss das Interface mit dem Befehl `runcmd` neu gestartet werden.

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

## Zusammenfassung

Sie wissen nun, wie man Instanzen mit Cloud-Init anpassen und IPv6 nutzen kann.

Im nächsten und letzten Schritt werden wir mehrere Instanzen mit Heat starten.

---
title: "17: The network in Heat"
lang: en
permalink: /optimist/guided_tour/step17/
nav_order: 1170
parent: Guided Tour
---

# Step 17: The network in Heat

## Start

Now that you have a simple template with a parameter, you can add the network.

## The template

Continue using the template you previously created.

First, add a new parameter, the ID of the external network, and name it
*public\_network\_id,*. Also define a default *provider*:

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
```

## Network

Next, add the network.

Like the VM, the network is a `resource`, it will be added to that block.

The type for network resources is *`OS::Neutron::Net`*

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

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk
```

## The port

Now that you have defined a network you can add the port, which is a resource with type *`OS::Neutron::Port`*.

To ensure that this port is used by your VM, add the *networks* property to it. Define a *port* property that then will use the *get\_resource*
function to link it to the *Port*.

Furthermore, you want to link the port to the network by adding a *network* property that also uses the *get\_resource* function to link it to the
*Netzwerk*.

At this point, your template looks like this:

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
                - port: {get_resource: Port }

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk
 
    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }
```

## The router

Your network needs a *Router* resource, with the type *`OS::Neutron::Router`.*

With this type it is important to define the external network it will use:

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
                - port: {get_resource: Port }

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk

    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }
 
    Router:
        type: OS::Neutron::Router
        properties:
            external_gateway_info: { "network": { get_param: public_network_id }
            name: BeispielRouter
```

## The subnet

Next, define a subnet for your network. This is the *Subnet* resource with type *`OS::Neutron::Subnet.`*

It is in the subnet that you define IP information like nameserver(s), the IP version, the IP range, and other IP-related settings:

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
                - port: {get_resource: Port }

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk

    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }

    Router:
        type: OS::Neutron::Router
        properties:
            external_gateway_info: { "network": { get_param: public_network_id }
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
```

## Subnet bridge

Finally, define a subnet bridge with type *`OS::Neutron::RouterInterface`*. This associates the subnet with the router to ensure that VMs in that subnet will use the router.

Additionally, you can define the *depends\_on* property, which ensures that the subnet bridge will only be created if *Subnet* is available:

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
                - port: {get_resource: Port }

    Netzwerk:
        type: OS::Neutron::Net
        properties:
            name: BeispielNetzwerk

    Port:
        type: OS::Neutron::Port
        properties:
            network: { get_resource: Netzwerk }

    Router:
        type: OS::Neutron::Router
        properties:
            external_gateway_info: { "network": { get_param: public_network_id }
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
```

## Conclusion

You have now created the full network. When the stack is created, it will create a VM and all the required components to give it connectivity. The next step is to assign a public IP address to the instance.

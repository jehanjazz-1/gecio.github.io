---
title: Default Quotas
lang: en
permalink: /optimist/specs/default_quota/
parent: Specifications
nav_order: 9200
last_modified_date: 2021-04-19
---

OpenStack Default Quotas
========================

In Optimist we have defined default quotas for the OpenStack Compute service, the OpenStack Block Storage service, and the OpenStack Networking service. We also have separate quotas for the Octavia Loadbalancer service and its associated components. These default values are listed below.

Compute Settings
----------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Cores                    |        256          |
| Fixed IPs                |        Unlimited    |
| Floating IPs             |        15           |
| Injected File Size       |        10240        |
| Injected Files           |        100          |
| Instances                |        100          |
| Key Pairs                |        100          |
| Properties               |        128          |
| Ram                      |        524288       |
| Server Groups            |        10           |
| Server Group Members     |        10           |

Block Storage settings
----------------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Backups                  |        100          |
| Backup Gigabytes         |        10000        |
| Gigabytes                |        10000        |
| Per-volume-gigabytes     |        Unlimited    |
| Snapshots                |        100          |
| Volumes                  |        100          |

Network settings
----------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Floating IPs             |        15           |
| Secgroup Rules           |        1000         |
| Secgroups                |        100          |
| Networks                 |        100          |
| Subnets                  |        200          |
| Ports                    |        500          |
| Routers                  |        50           |
| RBAC Policies            |        100          |
| Subnetpools              |        Unlimited    |

Octavia Loadbalancers
----------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Load Balancers           | 100                 |
| Listeners                | 100                 |
| Pools                    | 100                 |
| Health Monitors          | 100                 |
| Members                  | 100                 |

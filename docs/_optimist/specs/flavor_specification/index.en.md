---
title: Flavor Specifications
lang: en
permalink: /optimist/specs/flavor_specification/
parent: Specifications
nav_order: 9100
last_modified_date: 2022-03-29
---

# Flavor Specifications

In the OpenStack context the term "flavor" refers to a hardware profile that can be used for a virtual machine. In Optimist we have set up
various standard hardware profiles (flavors). These have different limits, which are listed below for all available flavors.

## Migrating between Flavor Types

To change the flavors of existing instances, the OpenStack "Resize Instance" Option can be used either via the Dashboard or the CLI. This will result in a reboot of the Instance but the content of the instance will be preserved.

## Deprecated Flavor Types

The following Flavor Types are currently considered deprecated and the removal of these flavor families is planned for the near future. We will regularly check if these flavors are still in use, if not, we will set them to private in order to to avoid new instances being created with them.

- m1-Family (Deprecated)
- e1-Family (e = equal) (Deprecated)
- r1-Family (r = ram) (Deprecated)
- Memory Flavors (Deprecated)
- Windows Flavors (Deprecated)

## Flavor Types

### Standard Flavors

| Name       | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| s1.micro   |     1 |  2 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| s1.small   |     2 |  4 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| s1.medium  |     4 |  8 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| s1.large   |     8 | 16 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| s1.xlarge  |    16 | 32 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| s1.2xlarge |    30 | 64 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### Dedicated CPU Flavors

| Name       | Cores |   RAM  |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | -----: | ----: | -----------------------: | -------------------------: | ----------------: |
| d1.micro   |     1 |  8 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| d1.small   |     2 | 16 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| d1.medium  |     4 | 32 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| d1.large   |     8 | 64 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| d1.xlarge  |    16 | 128 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| d1.2xlarge |    30 | 256 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### m1-Family (Deprecated)

| Name       | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| m1.micro   |     1 |  1 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| m1.small   |     2 |  4 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| m1.medium  |     4 |  8 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| m1.large   |     8 | 16 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| m1.xlarge  |    16 | 32 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| m1.xxlarge |    30 | 64 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### e1-Family (e = equal) (Deprecated)

| Name       | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| e1.small   |     2 |  2 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| e1.medium  |     4 |  4 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| e1.large   |     8 |  8 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| e1.xlarge  |    16 | 16 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| e1.xxlarge |    30 | 32 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### r1-Family (r = ram) (Deprecated)

| Name      | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :-------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| r1.small  |     2 |  6 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| r1.medium |     4 | 12 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| r1.large  |     8 | 32 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| r1.xlarge |    16 | 48 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### Memory Flavors (Deprecated)

| Name       | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| mem.micro  |     4 | 16 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| mem.small  |     8 | 32 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| mem.medium |     8 | 64 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |

### Windows Flavors (Deprecated)

| Name       | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| win.micro  |     1 |  2 GB | 80 GB |              1000 / 1000 |         200 MB/s /200 MB/s |          1 Gbit/s |
| win.small  |     2 |  8 GB | 80 GB |              1000 / 1000 |         200 MB/s /200 MB/s |          2 Gbit/s |
| win.medium |     4 | 16 GB | 80 GB |              1000 / 1000 |         200 MB/s /200 MB/s |          3 Gbit/s |
| win.large  |     8 | 32 GB | 80 GB |              1000 / 1000 |         200 MB/s /200 MB/s |          4 Gbit/s |
| win.xlarge |    16 | 64 GB | 80 GB |              1000 / 1000 |         200 MB/s /200 MB/s |          4 Gbit/s |

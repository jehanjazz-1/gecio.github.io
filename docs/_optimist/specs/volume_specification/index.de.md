---
title: Volumenspezifikationen
lang: de
permalink: /optimist/specs/volume_specification/
parent: Spezifikationen
nav_order: 9400
last_modified_date: 2022-02-02
---

# Volumenspezifikationen

In OpenStack sind "Volumes" persistente Speicher, die Sie an Ihre laufenden OpenStack Compute-Instanzen anhängen können. In Optimist haben wir drei Serviceklassen für Volumes eingerichtet. Diese haben unterschiedliche Limits, die unten aufgeführt sind.

## Volume-Typen

Wir haben drei Hauptvolumentypen:

* high-iops
* default
* low-iops

## Volumen-QoS-Liste

Nachfolgend eine Übersicht der drei Volume-Typen:

| Name          | Associations  | Read Bytes Sec | Read IOPS Sec  | Write Bytes Sec | Write IOPS Sec |
| :------------ | ------------: | -------------: | -------------: | --------------: | -------------: |
| qos-high-iops | high-iops     | 524288000      | 10000          | 524288000       | 10000          |
| qos-default   | default       | 209715200      | 2500           | 209715200       | 2500           |
| qos-low-iops  | low-iops      | 52428800       | 300            | 52428800        | 300            |

## Auswählen eines Volume-Typs

Sie können beim Erstellen eines Volumes mit dem folgenden Befehl einen der drei Volume-Typen auswählen (Wenn nicht anders angegeben, wird immer der Typ „Standard“ verwendet):
`$ openstack volume create <volume-name> --size 10 --type high-iops`

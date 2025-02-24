---
title: GKS Changelog v2.20
lang: de
permalink: /gks/about/changelog-v2.20/
nav_order: 700
parent: Über GKS
---

## Neue URL

Das iMKE-Dashboard wird umbenannt in GKS-Dashboard, da der neue Name besser die
Zugehörigkeit zur German Edge Cloud widerspiegelt als der vorherige. Auch der
Domänenname ändert sich damit. Das Dashboard ist ab dem 1. Juli 2022 unter der
URL [https://gks.gec.io](http://gks.gec.io) erreichbar.

## Unterstützte Kubernetes Versionen

Im aktuellen Release werden die folgenden Kubernetes-Versionen unterstützt:

* 1.21.8
* 1.22.5
* 1.23.6

## End of Life Ankündigungen

Wir werden die Unterstützung der Kubernetes Version v1.21 am 28.06.2022 beenden.

Führen Sie bei allen bestehenden Clustern mit Kubernetes Version 1.21 ein Update auf mindestens Version 1.22 bis zu diesem Datum durch.

## Neue Funktionen

Das aktuelle Release der Plattform ist ein technisches Release ohne neue Features.

## Bugfixes

* Für Kundencluster, die im Backend etcd 3.5 nutzen (Kubernetes 1.22 Cluster), wurden im Backend etcd Korruptionschecks aktiviert um etcd Dateninkonsistenzen zu entdecken. Diese Checks werden beim etcd Startup sowie alle 4 Stunden durchgeführt. ([#13766](https://groups.google.com/a/kubernetes.io/g/dev/c/B7gJs88XtQc/m/rSgNOzV2BwAJ))

## Änderungen in Kubernetes

### Upgrade-Hinweise für Kubernetes 1.23

Wenn Sie ein Upgrade auf Kubernetes 1.23 planen, lesen Sie bitte im offiziellen Kubernetes v1.23 Changelog den Abschnitt [What's New](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md#whats-new-major-themes) und machen Sie sich mit den bevorstehenden Änderungen vertraut. Es werden diesmal sehr viele beta-Kubernetes APIs entfernt, was potenziell Änderungen im Software-Ausroll-Prozess zur Folge hat.

Eine Übersicht über die Änderungen finden Sie im Changelog in Abschnitt [Changes by Kind](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md#changes-by-kind-2).

* [Wichtige Hinweise zum Upgrade](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md#urgent-upgrade-notes)
* [Deprecations](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md#deprecation)
* [API-Änderungen](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md#api-change-4)
* [Features](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.22.md#feature-7)

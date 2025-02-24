---
title: Role-Based Access Control (RBAC)
lang: de
permalink: /gks/accessmanagement/usingrbac/
nav_order: 7200
parent: Access Management
---
<!-- LTeX:  language=de-DE -->
# Role-Based Access Control (RBAC)

Die RBAC-Funktion erlaubt es einem GKS-Projekt-Admin, einen feingranularen Zugriff basierend auf vordefinierten `ClusterRoles` und `Roles` zu gewähren. Über das GKS Dashboard kann ein Admin dafür clusterweit gültige `ClusterRoleBindings` und nur in einem bestimmten Namespace gültige `RoleBindings` anlegen.
![RBAC option](../../accessmanagement/images/AccMgmt02.png)

Benutzer mit diesem Zugriffslevel haben keinen Zugriff auf das GKS Dashboard, können sich jedoch über einen Link (siehe unten) ihre eigene `kubeconfig`-Datei herunterladen und damit Zugriff auf das Cluster bekommen.

Mehr Informationen über Kubernetes RBAC finden Sie [hier](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

## Benutzer-basierter Zugriff

Um einem Benutzer einen RBAC-basierten Zugriff auf einem Cluster einzurichten, klicken Sie im RBAC-Widget auf `Add Binding`.

![RBAC Add Binding](../images/RBAC01.png)

## Cluster-weiter Zugriff

Um einen Cluster-weiten Zugriff zu gewähren, wählen Sie im folgenden Fenster `Cluster` aus, tragen die E-Mail-Adresse des Benutzers ein und wählen die entsprechende Rolle aus.

![Add a cluserrolebinding](../images/RBAC02.png)

Dabei sollten Sie jedoch beachten, dass der Benutzer prinzipiell für GKS autorisiert ist. Dieser Zugriff ist notwendig, um die
`kubeconfig`-Datei herunterladen zu können. Die auswählbaren Rollen sind als `ClusterRoles` angelegt und können mit `kubectl` betrachtet werden.

```bash
kubectl get clusterrole $NAME_OF_CLUSTERROLE -o yaml
```

## Namespace-weiter Zugriff

Wenn der Zugriff auf einen Namespace beschränkt werden soll, müssen Sie im `Add Binding`-Dialog zu `Namespace` wechseln und die E-Mail-Adresse angeben.

Im nächsten Schritt müssen Sie die Rolle auswählen, die dem Benutzer zugewiesen werden soll.

![Add a rolebinding #1](../images/RBAC03.png)

Zuletzt müssen Sie noch den Namespace auswählen, in dem diese Berechtigung gelten soll.

![Add a rolebinding #2](../images/RBAC04.png)

Die für diese Rollen gewährten Zugriffsberechtigungen können Sie ebenfalls mit `kubectl` abfragen. Da `Roles` im Gegensatz zu `ClusterRoles` auf einen bestimmten Namespace bezogen sind, müssen Sie in diesem Fall auch den Namespace mit angeben.

```bash
kubectl get role $NAME_OF_ROLE -n $NAMESPACE -o yaml
```

Nachdem Sie den Zugriff entsprechend gewährt haben, sollten die gewährten Rechte im Dashboard sichtbar sein.

![RBAC option](../images/RBAC05.png)

## Den Benutzern die kubeconfig-Datei zur Verfügung stellen

Sobald einem Benutzer RBAC-Rechte zugewiesen wurden, können Sie diesem seine persönliche `kubeconfig`-Datei über einen speziellen Link zur Verfügung stellen.

Dazu müssen Sie den `Share kubeconfig` Link im GKS Dashboard öffnen.

![Share kubeconfig button](../images/RBAC06.png)

Im nächsten Schritt müssen Sie den angezeigten Link kopieren und an den Nutzer schicken.

![Share kubeconfig dialog](../images/RBAC07.png)

Der Link zeigt zu einer Login-Seite. Dort muss sich der Benutzer authentifizieren und kann danach direkt seine `kubeconfig`-Datei herunterladen:

![Login page](../images/RBAC08.png)

Sobald ein Benutzer seine `kubeconfig`-Datei heruntergeladen hat, werden alle eventuellen weiteren Änderungen an den RBAC-Rechten sofort aktiv. Insbesondere ein Entziehen der Rechte ist sofort umgesetzt, ein `Revoke Token` ist damit nicht notwendig.

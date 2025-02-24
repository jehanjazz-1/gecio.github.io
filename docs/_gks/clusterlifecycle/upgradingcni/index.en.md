---
title: CNI Updates
lang: en
permalink: /gks/clusterlifecycle/upgradingcni/
nav_order: 4260
parent: Cluster Lifecycle
---

# CNI Updates

On the detail page of the cluster you can see if an update of the CNI is available.
If you see a green arrow on the CNI plugin box, you can update. Click the plugin box to start.
![Step 1](../images/CNIUpd01.png)

It will show you the available versions. To start the upgrade, click the `Change CNI Version` button.

![Step 2](../images/CNIUpd02.png)

The update process starts in the background. While updating, the worker nodes will experience some drop packages while each node is updated.
The process updates one worker at a time, so deployments with more the 1 replica should not experience an outage.

The update normally completes without issues and the cluster is working afterwards.
In cases where there are network problems at the end of the update (all pods in the canal daemonset are recreated), possible solutions would be:

1. Restart the pods one more time after the update. You can do this with the kubectl client: `kubectl rollout restart daemonset --namespace kube-system canal`
2. A rolling recreation of all worker nodes. You can do this in the UI.

CNI update only supports updates to the next minor version. If you see more than one version in the dropdown, you need to update one by one.

![Step 3](../images/CNIUpd03.png)

# 📈 Scale Worker Node

To ensure you have sufficient resources to run the workshop activities, you need to scale your OpenShift cluster by adding additional worker nodes. This activity will guide you through scaling worker nodes in your cluster.

## Objectives

In this activity, you will:

* Understand the resource requirements for the workshop
* Scale worker nodes in your OpenShift cluster
* Verify that the additional nodes are available and ready

## Prerequisites

* You have admin access to your OpenShift cluster

## Scale Worker Nodes

### Step 1: Scale Worker Nodes via OpenShift Console

1. Login to your OpenShift cluster console:

   <a href="https://console-openshift-console.apps.<CLUSTER_DOMAIN>" target="_blank">OpenShift Console</a>

2. Navigate to **Compute** → **MachineSet** in the left navigation menu.

3. You will see a list of MachineSets created in your cluster. The MachineSet name will look something like: `ocp-jcsl6-worker-us-east-2a`

4. Select the first MachineSet in the list (typically the worker MachineSet).

5. Click on the **three dots (⋮)** menu button on the right side of the MachineSet row.

6. Select **Edit machine count** from the dropdown menu.

7. In the dialog that appears, change the machine count from **0** to **1**.

8. Click **Save** to apply the changes.

   The OpenShift cluster will now start provisioning a new worker node.

?> **Note** The estimated time for node provisioning is **5 to 10 minutes**. You can monitor the progress in the MachineSet details page.

### Step 2: Verify New Nodes are Ready

1. Wait for the new node to be provisioned. This typically takes **5 to 10 minutes**.

2. You can monitor the progress in the OpenShift Console:
   - Navigate to **Compute** → **Machines** to see the machine being created
   - Navigate to **Compute** → **Nodes** to see when the node joins the cluster

3. Alternatively, you can check using the CLI:

```bash
oc get nodes
```

4. Wait until the new node shows as `Ready`:

```bash
oc get nodes -w
```

You should see your new worker node in the list with status `Ready`.

5. Verify node resources:

```bash
oc describe node <node-name>
```

6. Check that the nodes have sufficient resources:

```bash
oc top nodes
```

## Next Steps

Now that you have scaled your cluster with additional worker nodes, you're ready to configure hardware profiles. Click the link below to proceed:

* [🔧 Configure Hardware Profiles](0-initial-setup/2-hardware-profiles.md)

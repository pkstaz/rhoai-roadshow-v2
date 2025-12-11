# 📈 Scale Worker Node

To ensure you have sufficient resources to run the workshop activities, you need to scale your OpenShift cluster by adding additional worker nodes. This activity will guide you through scaling worker nodes in your cluster.

## Objectives

In this activity, you will:

* Understand the resource requirements for the workshop
* Scale worker nodes in your OpenShift cluster
* Verify that the additional nodes are available and ready

## Prerequisites

* You have admin access to your OpenShift cluster
* You have access to your cloud provider (AWS, Azure, GCP) to provision instances
* Your cluster has the necessary permissions to create new nodes

## Scale Worker Nodes

### Step 1: Check Current Cluster Resources

Before scaling, check your current cluster resources:

1. Login to your OpenShift cluster:

```bash
oc login --server=https://api.<CLUSTER_DOMAIN>:6443 -u admin -p <password>
```

2. Check current nodes:

```bash
oc get nodes
```

3. Check current worker nodes:

```bash
oc get nodes -l node-role.kubernetes.io/worker
```

4. Check available resources:

```bash
oc top nodes
```

### Step 2: Determine Scaling Requirements

For this workshop, you should have sufficient resources to run:
* Multiple workbenches
* Model serving instances
* Vector databases
* Other supporting workloads

Recommended minimum:
* **Worker Nodes**: At least 2-3 worker nodes
* **CPU**: Minimum 8 cores per node
* **Memory**: Minimum 32 GiB per node
* **Storage**: Adequate storage for models and data

### Step 3: Scale Worker Nodes via Machine Set (AWS Example)

?> **Note** The exact method for scaling nodes depends on your cloud provider. This example uses AWS Machine Sets.

1. List existing MachineSets:

```bash
oc get machineset -n openshift-machine-api
```

2. Identify the MachineSet you want to scale. Typically, you'll scale the worker MachineSet.

3. Scale the MachineSet by increasing replicas. For example, to add one more worker node:

```bash
oc scale machineset <machineset-name> --replicas=<desired-count> -n openshift-machine-api
```

Or edit the MachineSet directly:

```bash
oc edit machineset <machineset-name> -n openshift-machine-api
```

Change the `spec.replicas` field to the desired number of nodes.

4. Monitor the node creation:

```bash
oc get machines -n openshift-machine-api
oc get nodes
```

### Step 4: Verify New Nodes are Ready

1. Wait for the new nodes to join the cluster and become ready:

```bash
oc get nodes -w
```

2. Check that the nodes show as `Ready`:

```bash
oc get nodes
```

You should see your new worker nodes in the list with status `Ready`.

3. Verify node resources:

```bash
oc describe node <node-name>
```

4. Check that the nodes have sufficient resources:

```bash
oc top nodes
```

### Step 5: Verify Cluster Capacity

1. Check overall cluster capacity:

```bash
oc get nodes -o custom-columns=NAME:.metadata.name,CPU:.status.capacity.cpu,MEMORY:.status.capacity.memory
```

2. Verify you have enough resources for the workshop workloads.

## Verification Checklist

- [ ] Additional worker nodes are in `Ready` state
- [ ] Nodes have sufficient CPU and memory resources
- [ ] Cluster has adequate capacity for workshop workloads
- [ ] All nodes are schedulable and healthy

## Troubleshooting

If nodes are not joining the cluster:

1. Check Machine status:
   ```bash
   oc get machines -n openshift-machine-api
   oc describe machine <machine-name> -n openshift-machine-api
   ```

2. Check node conditions:
   ```bash
   oc get nodes
   oc describe node <node-name>
   ```

3. Review MachineSet events:
   ```bash
   oc get events -n openshift-machine-api --sort-by='.lastTimestamp'
   ```

4. Check cloud provider quotas and limits to ensure you can provision additional instances.

## Next Steps

Now that you have scaled your cluster with additional worker nodes, you're ready to configure hardware profiles. Click the link below to proceed:

* [🔧 Configure Hardware Profiles](0-initial-setup/2-hardware-profiles.md)

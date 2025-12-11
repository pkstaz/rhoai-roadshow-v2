# 🎮 Add GPU Node to Cluster

To enable GPU-accelerated workloads in your OpenShift cluster, you need to add GPU worker nodes. This activity will guide you through adding a GPU node to your cluster, which is essential for running LLM inference and other GPU-intensive workloads.

## Objectives

In this activity, you will:

* Understand the requirements for GPU nodes in OpenShift
* Add a GPU worker node to your cluster
* Verify that the GPU node is available and properly configured
* Install and configure the GPU Operator

## Prerequisites

* You have admin access to your OpenShift cluster
* You have access to your cloud provider (AWS, Azure, GCP) to provision GPU instances
* Your cluster has the necessary permissions to create new nodes

## Add GPU Node to Cluster

### Step 1: Determine GPU Requirements

Before adding a GPU node, determine your requirements:

* **GPU Type**: For this workshop, we recommend NVIDIA L4 GPUs (cost-effective, good for inference)
* **Instance Type**: AWS `g6.8xlarge` (24GB L4 NVIDIA, 32 vCPUs, 128 GiB memory)
* **Storage**: Ensure adequate storage for model files and data

### Step 2: Add GPU Node via Machine Set (AWS Example)

?> **Note** The exact method for adding nodes depends on your cloud provider. This example uses AWS Machine Sets.

1. Login to your OpenShift cluster:

```bash
oc login --server=https://api.<CLUSTER_DOMAIN>:6443 -u admin -p <password>
```

2. Create a MachineSet for GPU nodes. Create a file `gpu-machineset.yaml`:

```yaml
apiVersion: machine.openshift.io/v1beta1
kind: MachineSet
metadata:
  name: gpu-worker-<zone>
  namespace: openshift-machine-api
spec:
  replicas: 1
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: <cluster-id>
      machine.openshift.io/cluster-api-machine-role: worker
      machine.openshift.io/cluster-api-machine-type: worker
  template:
    metadata:
      labels:
        machine.openshift.io/cluster-api-cluster: <cluster-id>
        machine.openshift.io/cluster-api-machine-role: worker
        machine.openshift.io/cluster-api-machine-type: worker
        node-role.kubernetes.io/gpu: ""
    spec:
      providerSpec:
        value:
          ami:
            id: <ami-id>
          apiVersion: machine.openshift.io/v1beta1
          credentialsSecret:
            name: aws-cloud-credentials
          instanceType: g6.8xlarge
          kind: AWSMachineProviderConfig
          placement:
            availabilityZone: <zone>
            region: <region>
          securityGroups:
          - filters:
            - name: tag:Name
              values:
              - <cluster-id>-worker-sg
          subnet:
            filters:
            - name: tag:Name
              values:
              - <cluster-id>-private-<zone>
          tags:
          - name: kubernetes.io/cluster/<cluster-id>
            value: owned
          userDataSecret:
            name: worker-user-data
```

3. Replace the placeholders:
   * `<cluster-id>`: Your cluster ID
   * `<zone>`: Availability zone (e.g., `us-east-2a`)
   * `<region>`: AWS region
   * `<ami-id>`: AMI ID for your OpenShift version

4. Apply the MachineSet:

```bash
oc apply -f gpu-machineset.yaml
```

5. Monitor the node creation:

```bash
oc get machines -n openshift-machine-api
oc get nodes
```

### Step 3: Verify GPU Node is Ready

1. Wait for the node to join the cluster and become ready:

```bash
oc get nodes -l node-role.kubernetes.io/gpu
```

2. Check that the node shows as `Ready`:

```bash
oc get nodes
```

You should see your new GPU node in the list with status `Ready`.

### Step 4: Install GPU Operator

The GPU Operator manages NVIDIA GPU resources in OpenShift. Install it if not already present:

1. Check if the GPU Operator is installed:

```bash
oc get subscription -n openshift-operators | grep gpu
```

2. If not installed, create a Subscription:

```yaml
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: gpu-operator
  namespace: openshift-operators
spec:
  channel: stable
  name: gpu-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
```

3. Apply the subscription:

```bash
oc apply -f gpu-operator-subscription.yaml
```

4. Wait for the operator to install and verify:

```bash
oc get pods -n openshift-gpu-operator
```

### Step 5: Label the GPU Node

Label your GPU node so workloads can be scheduled on it:

```bash
oc label node <gpu-node-name> node-role.kubernetes.io/gpu=""
oc label node <gpu-node-name> accelerator=nvidia
```

### Step 6: Verify GPU Availability

1. Check that GPUs are detected:

```bash
oc get node <gpu-node-name> -o jsonpath='{.status.allocatable.nvidia\.com/gpu}'
```

2. You should see the number of GPUs available (e.g., `1`)

3. Test GPU access by running a simple GPU workload or checking node resources:

```bash
oc describe node <gpu-node-name> | grep -i gpu
```

## Verification Checklist

- [ ] GPU node is in `Ready` state
- [ ] GPU Operator is installed and running
- [ ] Node is labeled with `node-role.kubernetes.io/gpu`
- [ ] GPUs are detected and allocatable
- [ ] Hardware profiles can use the GPU node

## Troubleshooting

If the GPU node is not showing GPUs:

1. Check GPU Operator pods are running:
   ```bash
   oc get pods -n openshift-gpu-operator
   ```

2. Check node conditions:
   ```bash
   oc describe node <gpu-node-name>
   ```

3. Review GPU Operator logs:
   ```bash
   oc logs -n openshift-gpu-operator -l app=nvidia-operator-validator
   ```

## Next Steps

Now that you have GPU nodes configured, you're ready to configure hardware profiles. Click the link below to proceed:

* [🔧 Configure Hardware Profiles](0-platform-setup/2-hardware-profiles.md)


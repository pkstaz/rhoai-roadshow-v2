# 🔧 Configure Hardware Profiles

Hardware profiles in Red Hat OpenShift AI allow you to define and manage compute resources for your data science workbenches and model serving workloads. By configuring hardware profiles, you can ensure that your workloads have access to the appropriate GPU resources, memory, and CPU allocations.

## Objectives

In this activity, you will:

* Understand what hardware profiles are and why they're important
* Configure hardware profiles for different workload types
* Verify that hardware profiles are available for use

## What are Hardware Profiles?

Hardware profiles define the compute resources available to workbenches and model serving instances in OpenShift AI. They specify:

* GPU type and quantity (e.g., NVIDIA L4, A100)
* CPU cores
* Memory allocation
* Whether resources are shared or dedicated

## Configure Hardware Profiles

### Step 1: Access OpenShift AI Administration

1. Login to your OpenShift cluster console:
   
   <a href="https://console-openshift-console.apps.<CLUSTER_DOMAIN>" target="_blank">OpenShift Console</a>

2. Navigate to **OpenShift AI** → **Settings** → **Environment Setup** → **Hardware profiles**

### Step 2: Review Existing Hardware Profiles

1. You should see a list of existing hardware profiles. Common profiles include:
   * **Small** - CPU-only workloads
   * **Medium** - Shared GPU access
   * **Large** - Dedicated GPU access

2. Review the configuration of each profile to understand the resource allocations.

### Step 3: Create a New Hardware Profile

For this workshop, you will create a new hardware profile with the following configuration:

1. Click **Create hardware profile** or **Add hardware profile**

2. Enter the following details:

   **Name**: `ai-roadshow-profile`

3. Configure the resources as follows:

   | Resource name | Resource identifier | Resource type | Default | Minimum allowed | Maximum allowed |
   |---------------|---------------------|---------------|---------|------------------|-----------------|
   | CPU | `cpu` | CPU | 1 Cores | 1 Cores | 2 Cores |
   | Memory | `memory` | Memory | 12 GiB | 1 GiB | 24 GiB |
   | GPU | `nvidia.com/gpu` | Accelerator | 1 | 1 | 1 |

4. Click **Create** or **Save** to create the hardware profile

?> **Note** The GPU resource identifier `nvidia.com/gpu` is the standard Kubernetes resource name for NVIDIA GPUs. Ensure your cluster has GPU nodes configured and the GPU Operator installed for this resource to be available.

### Step 4: Verify Hardware Profile Availability

1. Navigate to **Red Hat OpenShift AI** → **Data Science Projects** → Select your project

2. Try creating a new workbench and verify that hardware profiles are available in the dropdown

3. You should see the configured hardware profiles listed as options

## Verification

To verify that hardware profiles are properly configured:

1. Check that hardware profiles appear in the workbench creation form
2. Verify that GPU-enabled profiles show the correct GPU type and count
3. Ensure that resource quotas are appropriate for your workloads

## Next Steps

Once you have configured hardware profiles, you're ready to add GPU nodes to your cluster. Click the link below to proceed:

* [🎮 Add GPU Node to Cluster](0-platform-setup/2-add-gpu-node.md)


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

2. Navigate to **Red Hat OpenShift AI** → **Settings** → **Cluster settings** → **Hardware profiles**

### Step 2: Review Existing Hardware Profiles

1. You should see a list of existing hardware profiles. Common profiles include:
   * **Small** - CPU-only workloads
   * **Medium** - Shared GPU access
   * **Large** - Dedicated GPU access

2. Review the configuration of each profile to understand the resource allocations.

### Step 3: Create or Modify Hardware Profiles

?> **Note** The exact steps for creating hardware profiles may vary depending on your OpenShift AI version. Consult your administrator or the OpenShift AI documentation for specific instructions.

For this workshop, ensure you have at least one hardware profile configured with:

* **GPU Type**: NVIDIA L4 (or equivalent)
* **GPU Count**: 1 (shared) or more
* **Memory**: Minimum 32Gi
* **CPU**: Minimum 8 cores

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


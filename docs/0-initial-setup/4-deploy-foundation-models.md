# 🤖 Deploy Foundation Models

Before you can start working with LLMs in the workshop, you need to deploy foundation models that will be used throughout the exercises. This activity will guide you through deploying foundation models that are required for the LLM, RAG, and Agents modules.

## Objectives

In this activity, you will:

* Create a Serving Runtime for model deployment
* Deploy a foundation model using the created Serving Runtime
* Configure model resources and settings

## Prerequisites

* You have completed [Create Project](0-initial-setup/3-create-project.md)
* You are logged in as admin in OpenShift AI
* You have access to model repositories (Hugging Face, Red Hat AI validated models)
* Sufficient storage and compute resources for model deployment

## Step 1: Create Serving Runtime

1. Ensure you are logged in as **admin** in OpenShift AI.

2. Navigate to **Settings** → **Model resources and operations** → **Serving Runtimes**

3. Click **Add Serving Runtimes**

4. Configure the Serving Runtime:

   **Select the API protocol this runtime supports** *  
   Select: **REST**

   **Select the model types this runtime supports** *  
   Select: **Generative AI Model (Example LLM)**

5. Click on the text **"start from scratch"**

6. Copy and paste the following YAML:

```yaml
apiVersion: serving.kserve.io/v1alpha1
kind: ServingRuntime
metadata:
  name: vllm-cuda-runtime-gptoss
  annotations:
    openshift.io/display-name: vLLM NVIDIA GPU ServingRuntime for GPTOSS
    opendatahub.io/recommended-accelerators: '["nvidia.com/gpu"]'
  labels:
    opendatahub.io/dashboard: 'true'
spec:
  annotations:
    prometheus.io/port: '8080'
    prometheus.io/path: '/metrics'
  multiModel: false
  supportedModelFormats:
    - autoSelect: true
      name: vLLM
  containers:
    - name: kserve-container
      image: quay.io/cestayg/vllm:0.10.1-gptoss
      command:
        - python
        - -m
        - vllm.entrypoints.openai.api_server
      args:
        - "--port=8080"
        - "--model=/mnt/models"
        - "--served-model-name={{.Name}}"
      env:
        - name: HF_HOME
          value: /tmp/hf_home
      ports:
        - name: http
          containerPort: 8080
          protocol: TCP
```

7. Click **Create**

8. In the list of Serving Runtimes, you can drag the icon on the left (with squares) to move it to the first position in the row.

## Step 2: Verify No Models Are Using GPU

1. Navigate to **AI Hub** → **Deployments** and select **All projects**

2. If you see any model in **Started** or **Running** status, click on the **three dots (⋮)** on the right side and click **Stop**

## Step 3: Deploy Model

1. Select the project **ai-roadshow**

2. Click **Deploy model**

3. Follow the guided step-by-step process:

### Step 3.1: Model Location

**Where is the model currently located?**

* Select (from combobox): **URI**

* **URI**: `oci://quay.io/cestayg/modelcar-gpt-oss-20b:1.5`

* **Enable**: Check **Create a connection to this location** (this creates a connection object)

* **Name**: `gpt-oss-20b`

* **Model type**: Select (from combobox): **Generative AI Model (Example LLM)**

* Click **Next**

### Step 3.2: Model Deployment Configuration

* **Model deployment name**: `gpt-oss-20b`

* **Hardware profile**: `ai-roadshow-profile`

* Click on the text **"customize resources request and limits"**

* Change **CPU limit** to `2`

* Change **Memory limit** to `24`

* **Serving runtime** *: Select **vLLM NVIDIA GPU ServingRuntime for GPTOSS**

* **Number of replicas to deploy** *: `1`

* Click **Next**

### Step 3.3: Advanced Settings

* Leave all advanced settings as default

* Do not select any options

* Click **Deploy model**

?> **Note** This deployment can take **10 to 15 minutes** to complete.

## Next Steps

Now that you have deployed foundation models, you're ready to create a workbench. Click the link below to proceed:

* [🖥️ Create Workbench](0-initial-setup/5-create-workbench.md)

# 🤖 Deploy Base Models

Before you can start working with LLMs in the workshop, you need to deploy base models that will be used throughout the exercises. This activity will guide you through deploying foundational models that are required for the LLM, RAG, and Agents modules.

## Objectives

In this activity, you will:

* Understand which models are needed for the workshop
* Deploy models using Red Hat AI Inference Server (RHAIIS) or vLLM
* Verify that models are accessible and ready for use
* Configure model endpoints for use in subsequent modules

## Prerequisites

* You have completed [Scale Worker Node](0-initial-setup/1-scale-worker-node.md)
* Worker nodes are available and ready with sufficient resources
* You have access to model repositories (Hugging Face, Red Hat AI validated models)
* Sufficient storage and compute resources for model deployment

## Models Required for Workshop

For this workshop, you will deploy the following base models:

1. **LLM Models** (for text generation, summarization, etc.):
   * Llama 3.2 3B (instruction-tuned)
   * DeepSeek R1 8B (reasoning model, quantized)
   * Llama 4 Scout 17B (Red Hat validated, MoE model)

2. **Embedding Model** (for RAG):
   * all-MiniLM-L6-v2 (sentence transformers)

## Deploy Models

### Step 1: Access Model Serving Interface

1. Login to OpenShift AI:
   
   <a href="https://rhods-dashboard-redhat-ods-applications.apps.<CLUSTER_DOMAIN>" target="_blank">OpenShift AI Dashboard</a>

2. Navigate to **Model Serving** → **Inference Services**

### Step 2: Deploy Llama 3.2 3B Model

1. Click **Deploy model** or **Create InferenceService**

2. Configure the model deployment:

   **Basic Information:**
   * **Name**: `llama-3b`
   * **Model Framework**: `PyTorch` or `vLLM`
   * **Model Format**: `HuggingFace`

   **Model Source:**
   * **Model URI**: `meta-llama/Llama-3.2-3B-Instruct`
   * Or use Red Hat validated model: `RedHatAI/Llama-3.2-3B-Instruct`

   **Resources:**
   * **Hardware Profile**: Select your GPU-enabled hardware profile
   * **GPU Count**: `1`
   * **Memory**: `16Gi` (minimum)

   **Advanced Settings:**
   * **Max Tokens**: `15000`
   * **Temperature**: `0.7`

3. Click **Deploy** and wait for the model to be deployed

4. Monitor the deployment status until it shows as `Ready`

### Step 3: Deploy DeepSeek R1 8B Model

1. Create a new InferenceService for DeepSeek:

   **Basic Information:**
   * **Name**: `deepseek-8b`
   * **Model Framework**: `vLLM`
   * **Model Format**: `HuggingFace`

   **Model Source:**
   * **Model URI**: `unsloth/DeepSeek-R1-0528-Qwen3-8B-bnb-4bit`
   * Note: This is a 4-bit quantized model for efficiency

   **Resources:**
   * **Hardware Profile**: GPU-enabled profile
   * **GPU Count**: `1`
   * **Memory**: `18Gi` (quantized model requires less memory)

   **Advanced Settings:**
   * **Max Tokens**: `10000`
   * **Quantization**: `4-bit`

2. Deploy and wait for readiness

### Step 4: Deploy Embedding Model

1. Create InferenceService for embeddings:

   **Basic Information:**
   * **Name**: `all-minilm-l6-v2`
   * **Model Framework**: `Sentence Transformers`
   * **Model Type**: `Embedding`

   **Model Source:**
   * **Model URI**: `sentence-transformers/all-MiniLM-L6-v2`

   **Resources:**
   * **Hardware Profile**: Can use CPU or GPU profile
   * **Memory**: `4Gi` (embedding models are smaller)

   **Advanced Settings:**
   * **Embedding Dimension**: `384`

2. Deploy the embedding model

### Step 5: Configure Model Endpoints

Once models are deployed, note their endpoints:

1. For each deployed model, find the inference endpoint URL:

```bash
oc get inferenceservice -n <namespace>
oc get route -n <namespace>
```

2. The endpoint will typically be in the format:
   ```
   https://<model-name>.<namespace>.apps.<CLUSTER_DOMAIN>/v1/models/<model-name>
   ```

3. Test the endpoint with a simple request:

```bash
curl -X POST https://<endpoint>/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Hello, how are you?",
    "max_tokens": 50
  }'
```

### Step 6: Verify Model Accessibility

1. Check model serving status in OpenShift AI dashboard

2. Verify models are listed and show as `Ready`

3. Test each model endpoint to ensure they respond correctly

4. For LLamaStack integration (if used), verify models are configured in the LLamaStack ConfigMap:

```bash
oc get configmap llama-stack-config -n llama-stack -o yaml
```

## Model Configuration Summary

After deployment, you should have:

| Model | Type | Endpoint | Status |
|-------|------|----------|--------|
| llama-3b | LLM | `https://...` | Ready |
| deepseek-8b | LLM | `https://...` | Ready |
| all-minilm-l6-v2 | Embedding | `https://...` | Ready |

?> **Note** The Llama 4 Scout 17B model may be deployed as a Model-as-a-Service (MaaS) externally, depending on your setup. This is typically configured separately.

## Verification Checklist

- [ ] All required models are deployed
- [ ] Models show as `Ready` in the dashboard
- [ ] Model endpoints are accessible
- [ ] Test requests to models return successful responses
- [ ] Models are configured in LLamaStack (if applicable)
- [ ] GPU resources are properly allocated

## Troubleshooting

If models fail to deploy:

1. Check pod status:
   ```bash
   oc get pods -n <namespace> | grep <model-name>
   ```

2. Review pod logs:
   ```bash
   oc logs <pod-name> -n <namespace>
   ```

3. Verify GPU availability:
   ```bash
   oc describe node <gpu-node> | grep -i gpu
   ```

4. Check resource quotas:
   ```bash
   oc describe quota -n <namespace>
   ```

## Next Steps

Now that you have deployed base models, you're ready to create a workspace. Click the link below to proceed:

* [🏗️ Create Workspace](0-initial-setup/4-create-workspace.md)


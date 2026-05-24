# Self-Hosted LLM Complete Guide

**Version:** 1.0 
**Created:** 2026-05-05 
**Objective:** Run and use local Large Language Models 
**Platform:** Linux (Fedora/RHEL), Kubernetes/OpenShift

---

## Introduction

**Self-hosted LLM**: Run large language models on your own hardware (GPU/CPU) instead of cloud services.

### Why Self-hosted LLM?

 **Privacy** - Sensitive code/documents never leave your machine 
 **Cost-Effective** - No API costs (ChatGPT: $20/mo, Claude Pro: $20/mo) 
 **Offline Operation** - Works without internet 
 **Customizable** - Fine-tune with your own data 
 **Full Control** - No rate limiting, no usage restrictions 
 **Local Integration** - VSCode, IDE plugins with local model

**Enterprise Benefits:**
- Sensitive code (infrastructure, cloud configurations) stays private
- Linux native support (RHEL, Fedora, Ubuntu, Debian)
- Container deployment options (Podman, Docker, Kubernetes)
- Compliance requirements (GDPR, SOC2, HIPAA)

---

## 1. Hardware Requirements

### Minimum Requirements (CPU Only)

```
CPU: 8+ core modern processor (Intel/AMD)
RAM: 16GB+ (small models: 7B parameters)
Disk: 50GB+ free space
OS: Linux (Fedora 40+, RHEL 9+), macOS, Windows

Performance: ~5-10 tokens/sec (slow but usable)
```

### Recommended Configuration (GPU)

```
GPU: NVIDIA RTX 3060 12GB / RTX 4060 Ti 16GB / RTX 4090 24GB
CPU: 8+ cores
RAM: 32GB+
Disk: 100GB+ NVMe SSD
OS: Linux (CUDA support)

Performance: 30-100+ tokens/sec (ChatGPT-like speed)
```

### Professional Setup (Multi-GPU)

```
GPU: 2x NVIDIA RTX 4090 (48GB VRAM) or A100/H100
CPU: 16+ cores (AMD Ryzen 9 / Intel i9)
RAM: 64GB+
Disk: 500GB+ NVMe RAID

Use case: Large models (70B+), batch processing, finetuning
```

### Model Size vs VRAM Requirements

| Model Size | Parameters | VRAM (FP16) | VRAM (4-bit quantized) | Example Models |
|------------|------------|-------------|------------------------|----------------|
| **Tiny** | 1-3B | 2-6 GB | 1-3 GB | Phi-3 Mini, TinyLlama |
| **Small** | 7-8B | 14-16 GB | 4-6 GB | Llama 3.1 8B, Mistral 7B |
| **Medium** | 13-14B | 26-28 GB | 8-10 GB | Llama 3.1 13B |
| **Large** | 30-34B | 60-68 GB | 16-20 GB | Qwen 2.5 32B |
| **Extra Large** | 70-72B | 140+ GB | 35-40 GB | Llama 3.1 70B |

**Quantization:**
- **FP16** (16-bit): Full precision, 2GB/1B parameter
- **Q8** (8-bit): ~50% size, minimal quality loss
- **Q4** (4-bit): ~25% size, acceptable quality (recommended for consumer GPUs)
- **Q3/Q2**: Extreme compression, significant quality loss

---

## 2. Popular Open-Source Models

### 2.1 Meta Llama 3.1 (Meta AI)

**Description:** Meta's official open-source model, excellent general-purpose use.

```
Versions:
 Llama 3.1 8B (14GB VRAM FP16, 4GB Q4)
 Llama 3.1 70B (140GB VRAM FP16, 35GB Q4)
 Llama 3.1 405B (810GB VRAM - multi-GPU)

Strengths:
 Excellent coding (Python, JavaScript, Go)
 Long context (128k tokens)
 Instruction-following
 Multilingual support

License: Llama 3.1 Community License (commercial use OK)
```

**Usage Example:**

```bash
# Ollama install + Llama 3.1 download
ollama pull llama3.1:8b

# Start chat
ollama run llama3.1:8b
>>> Write a Python script to configure Kubernetes cluster via SSH.
```

### 2.2 Mistral 7B / Mixtral 8x7B (Mistral AI)

**Description:** French startup's excellent quality model, fast and efficient.

```
Versions:
 Mistral 7B v0.3 (14GB VRAM FP16, 4GB Q4)
 Mixtral 8x7B (90GB VRAM FP16, 24GB Q4) - MoE (Mixture of Experts)
 Mistral NeMo 12B (24GB VRAM FP16, 7GB Q4)

Strengths:
 Very fast inference (optimized architecture)
 Outstanding code generation (especially Python)
 Good mathematical ability
 32k context window

License: Apache 2.0 (fully open)
```

**Usage Example:**

```bash
ollama pull mistral:7b
ollama run mistral:7b
>>> Debug this Kubernetes YAML: [paste manifest]
```

### 2.3 Qwen 2.5 (Alibaba Cloud)

**Description:** Chinese tech giant's model, excellent multilingual and coding capabilities.

```
Versions:
 Qwen 2.5 7B (14GB VRAM FP16, 4GB Q4)
 Qwen 2.5 14B (28GB VRAM FP16, 8GB Q4)
 Qwen 2.5 32B (64GB VRAM FP16, 16GB Q4)
 Qwen 2.5 72B (144GB VRAM FP16, 36GB Q4)

Strengths:
 Excellent multilingual (English, Chinese, Hungarian)
 Long context (128k tokens)
 Strong coding (Python, Go, Rust)
 Math & reasoning

License: Apache 2.0
```

**Usage Example:**

```bash
ollama pull qwen2.5:7b
ollama run qwen2.5:7b
>>> Explain the Kubernetes Operator pattern.
```

### 2.4 DeepSeek Coder V2 (DeepSeek AI)

**Description:** Specialized coding model, optimized specifically for programming.

```
Versions:
 DeepSeek Coder 6.7B (14GB VRAM FP16, 4GB Q4)
 DeepSeek Coder 33B (66GB VRAM FP16, 18GB Q4)
 DeepSeek Coder V2 236B (multi-GPU)

Strengths:
 Best code generation (GPT-4 level coding)
 16k context (full file context)
 Code completion, refactoring
 Multi-language support (80+ programming languages)

License: DeepSeek License (commercial OK)
```

**Usage Example:**

```bash
ollama pull deepseek-coder:6.7b
ollama run deepseek-coder:6.7b
>>> Convert this Bash script to Ansible playbook: [paste script]
```

### 2.5 Phi-3 (Microsoft)

**Description:** Small but surprisingly strong model, ideal for CPU operation.

```
Versions:
 Phi-3 Mini (3.8B) - 8GB VRAM FP16, 2GB Q4
 Phi-3 Medium (14B) - 28GB VRAM FP16, 8GB Q4

Strengths:
 Very small (runs on CPU!)
 Fast inference (up to 50 tokens/sec on CPU)
 Good reasoning
 Low latency

Weaknesses:
 Shorter context (4k-8k)
 Less detailed responses

License: MIT License
```

**Usage Example:**

```bash
ollama pull phi3:mini
ollama run phi3:mini
>>> Summarize this error log: [paste log]
```

### 2.6 CodeLlama (Meta AI)

**Description:** Llama-based variant specifically finetuned for coding.

```
Versions:
 CodeLlama 7B (14GB VRAM FP16, 4GB Q4)
 CodeLlama 13B (26GB VRAM FP16, 8GB Q4)
 CodeLlama 34B (68GB VRAM FP16, 18GB Q4)

Variants:
- CodeLlama Base: Code completion
- CodeLlama Instruct: Chat-based coding
- CodeLlama Python: Python specific

Strengths:
 Infilling (insert code in middle)
 Code completion (IDE integration)
 Long context (16k tokens - full files)

License: Llama 2 License
```

---

## 3. Runtime Platforms

### 3.1 Ollama (Easiest)

**Description:** Docker-like UX for LLMs, one-command install and run.

**Installation (Fedora/RHEL):**

```bash
# Download and install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Start systemd service
sudo systemctl enable ollama
sudo systemctl start ollama

# Verify
ollama --version
```

**Usage:**

```bash
# Download model
ollama pull llama3.1:8b

# Chat mode
ollama run llama3.1:8b

# API server (HTTP REST API)
ollama serve # Port: 11434

# cURL usage
curl http://localhost:11434/api/generate -d '{
 "model": "llama3.1:8b",
 "prompt": "Why is Kubernetes important?",
 "stream": false
}'
```

**Advantages:**
- Simple installation (1 command)
- Automatic model management (download, update)
- REST API (integrate anywhere)
- GPU auto-detect (CUDA, ROCm)

**Disadvantages:**
- Fewer fine-tuning options
- Limited UI (CLI + API)

### 3.2 LM Studio (Graphical UI)

**Description:** User-friendly GUI application for model management and chat.

**Installation:**

```bash
# Download: https://lmstudio.ai/
# Linux AppImage or DEB package
chmod +x LM_Studio-0.2.31.AppImage
./LM_Studio-0.2.31.AppImage
```

**Features:**
- Modern chat UI (ChatGPT-like)
- Integrated model browser (HuggingFace, Ollama)
- GPU/CPU speed monitoring
- Temperature, top-p, max tokens sliders
- Conversation history save

**Advantages:**
- Beginner-friendly (point-and-click)
- Nice UI
- Model performance benchmark

**Disadvantages:**
- Not scriptable (GUI only)
- Harder server mode

### 3.3 Text Generation WebUI (Oobabooga)

**Description:** Open-source webUI with advanced features (finetuning, extensions).

**Installation:**

```bash
# Git clone
git clone https://github.com/oobabooga/text-generation-webui
cd text-generation-webui

# Install (automatic conda env)
./start_linux.sh

# Browser: http://localhost:7860
```

**Features:**
- Web-based UI (remote access)
- Extensions (LoRA, multimodal, API)
- Model comparison
- Character chat (roleplay)
- Advanced parameters (sampler, CFG)

**Advantages:**
- Most features
- Community-developed extensions
- OpenAI API compatible server

**Disadvantages:**
- Complex installation
- Steep learning curve

### 3.4 vLLM (Production-Grade)

**Description:** High-performance inference server with PagedAttention optimization.

**Installation:**

```bash
# Python virtual env
python3 -m venv vllm-env
source vllm-env/bin/activate

# Install vLLM (CUDA support)
pip install vllm

# Start server
vllm serve meta-llama/Llama-3.1-8B-Instruct \
 --host 0.0.0.0 \
 --port 8000 \
 --tensor-parallel-size 1
```

**Features:**
- Fastest inference (20-30x faster than Transformers)
- Batch processing
- Continuous batching
- OpenAI-compatible API

**Advantages:**
- Production-ready
- Kubernetes/OpenShift deployment
- Scalability (multi-GPU, distributed)

**Disadvantages:**
- No UI (API only)
- Advanced GPU configuration required

### 3.5 LocalAI (OpenAI-Compatible API)

**Description:** OpenAI API drop-in replacement with local models.

**Installation (Docker/Podman):**

```bash
# Podman run
podman run -p 8080:8080 \
 -v $HOME/.local/share/localai:/build/models \
 quay.io/go-skynet/local-ai:latest

# OpenAI SDK usage (Python)
import openai
openai.api_base = "http://localhost:8080/v1"
openai.api_key = "not-needed"

response = openai.ChatCompletion.create(
 model="llama3.1",
 messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

**Advantages:**
- OpenAI API compatibility (easy migration)
- Multi-model support
- Audio transcription (Whisper), Image generation (Stable Diffusion)

---

## 4. Practical Use Cases

### 4.1 VSCode Integration (Continue.dev)

**Installation:**

```bash
# VSCode Extension: Continue
code --install-extension continue.continue

# Settings (Ctrl+Shift+P → Continue: Open Config)
# ~/.continue/config.json
{
 "models": [
 {
 "title": "Llama 3.1 8B",
 "provider": "ollama",
 "model": "llama3.1:8b",
 "apiBase": "http://localhost:11434"
 }
 ]
}
```

**Usage in VSCode:**

```
1. Ctrl+L → Open chat
2. Select code + Ctrl+I → Inline edit
3. "@file" mention → File context
4. "/edit" command → Code refactoring
```

**Exercise 4.1: Generate Ansible Playbook**

```
Continue chat:
>>> Generate an Ansible playbook to install and configure PostgreSQL 15 on RHEL 9,
 with custom pg_hba.conf for remote access.

[Llama 3.1 generates 50-100 lines of Ansible YAML]

>>> Add a handler to restart PostgreSQL if config changes.

[Continue updates code with handler]
```

### 4.2 Code Review and Bug Detection

**Usage:**

```bash
# From Ollama CLI
ollama run deepseek-coder:6.7b

>>> Review this Python code for potential bugs and security issues:
[paste Python code]

[Model feedback: potential SQL injection, unhandled exceptions, etc.]
```

**Exercise 4.2: Kubernetes Manifest Review**

```yaml
# deployment.yaml (bad configuration)
apiVersion: apps/v1
kind: Deployment
metadata:
 name: myapp
spec:
 replicas: 1
 template:
 spec:
 containers:
 - name: app
 image: myapp:latest
 resources:
 requests:
 memory: "64Mi"
 cpu: "250m"
```

**LLM prompt:**

```
>>> Review this Kubernetes deployment for production best practices.

[Model response:]
Issues found:
1. Missing imagePullPolicy - use "Always" or specific tag
2. "latest" tag is anti-pattern - use semantic versioning
3. Missing liveness/readiness probes
4. No resource limits defined (only requests)
5. Missing security context (runAsNonRoot)
6. Single replica - no high availability

Recommendations:
- Add imagePullPolicy: IfNotPresent
- Use specific image tag: myapp:1.2.3
- Add livenessProbe and readinessProbe
- Define resources.limits
- Add securityContext with runAsNonRoot: true
- Increase replicas to 3 for HA
```

### 4.3 Documentation Generation

**Exercise 4.3: Document Go Function**

```go
// Original code (no documentation)
func ReconcilePipeline(ctx context.Context, pr *tektonv1.PipelineRun) error {
 if pr.Status.CompletionTime != nil {
 return nil
 }
 if pr.Spec.Timeout != nil {
 deadline := pr.CreationTimestamp.Add(pr.Spec.Timeout.Duration)
 if time.Now().After(deadline) {
 return fmt.Errorf("pipeline timed out")
 }
 }
 return r.updateStatus(ctx, pr)
}
```

**LLM prompt:**

```
>>> Add comprehensive GoDoc comments to this function, explaining parameters,
 return values, and edge cases.

[Model generated documentation:]

// ReconcilePipeline performs reconciliation for a Tekton PipelineRun resource.
//
// This function checks if the PipelineRun has completed and handles timeout scenarios.
// If the PipelineRun is already completed (CompletionTime is set), reconciliation is skipped.
// For running PipelineRuns with a configured timeout, it validates whether the deadline
// has been exceeded and returns an error if the pipeline has timed out.
//
// Parameters:
// - ctx: Context for cancellation and deadline propagation
// - pr: Pointer to the PipelineRun resource to reconcile
//
// Returns:
// - nil if reconciliation succeeds or PipelineRun is already completed
// - error if the PipelineRun has exceeded its timeout or status update fails
//
// Edge cases:
// - If pr.Spec.Timeout is nil, no timeout validation is performed
// - CompletionTime check prevents redundant reconciliation of finished runs
func ReconcilePipeline(ctx context.Context, pr *tektonv1.PipelineRun) error {
 // ... original code ...
}
```

---

## 5. Kubernetes/OpenShift Deployment

### 5.1 Ollama Deployment (Kubernetes)

**Deployment YAML:**

```yaml
# ollama-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: ollama
 namespace: ai-services
spec:
 replicas: 1
 selector:
 matchLabels:
 app: ollama
 template:
 metadata:
 labels:
 app: ollama
 spec:
 containers:
 - name: ollama
 image: ollama/ollama:latest
 ports:
 - containerPort: 11434
 name: http
 resources:
 requests:
 memory: "8Gi"
 cpu: "2"
 limits:
 memory: "16Gi"
 cpu: "4"
 nvidia.com/gpu: "1" # GPU support
 volumeMounts:
 - name: models
 mountPath: /root/.ollama
 volumes:
 - name: models
 persistentVolumeClaim:
 claimName: ollama-models-pvc
---
apiVersion: v1
kind: Service
metadata:
 name: ollama
 namespace: ai-services
spec:
 selector:
 app: ollama
 ports:
 - port: 11434
 targetPort: 11434
 type: ClusterIP
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: ollama-models-pvc
 namespace: ai-services
spec:
 accessModes:
 - ReadWriteOnce
 resources:
 requests:
 storage: 100Gi
 storageClassName: fast-ssd
```

**Deploy:**

```bash
# Create namespace
kubectl create namespace ai-services

# Deploy
kubectl apply -f ollama-deployment.yaml

# Check pods
kubectl get pods -n ai-services

# Download model (exec into pod)
kubectl exec -it -n ai-services deployment/ollama -- ollama pull llama3.1:8b

# Port-forward for local access
kubectl port-forward -n ai-services svc/ollama 11434:11434

# Test
curl http://localhost:11434/api/generate -d '{"model":"llama3.1:8b","prompt":"Hello!"}'
```

---

## 6. Cost-Benefit Analysis

### Cloud LLM vs Self-Hosted

**ChatGPT Plus ($20/mo):**
```
Monthly cost: $20 × 12 = $240/year
Limitations:
- Rate limiting (50 messages/3 hours GPT-4)
- No offline mode
- Privacy concerns (code goes to OpenAI)
```

**Self-Hosted (RTX 4060 Ti 16GB):**
```
Hardware: $500 (one-time)
Power: ~200W × 8h/day × $0.15/kWh × 365 days = ~$87/year
Total (Year 1): $587
Total (5 years): $500 + ($87 × 5) = $935 ($187/year average)

ROI: ~2.5 years (break-even point)

Benefits:
 Unlimited usage
 Offline mode
 Complete privacy
 Customizable (finetuning)
```

---

## 7. Recommended Model Selection by Use Case

### Coding (VSCode Copilot Replacement)

```
1. DeepSeek Coder 6.7B (Q4: 4GB VRAM)
 - Best code generation
 - Fast inference
 - IDE integration (Continue.dev)

2. CodeLlama 7B Instruct (Q4: 4GB VRAM)
 - Good coding + chat
 - Long context (16k)

3. Qwen 2.5 Coder 7B (Q4: 4GB VRAM)
 - Multilingual code (Python, Go, Rust)
 - Strong reasoning
```

### General Chat + Documentation

```
1. Llama 3.1 8B Instruct (Q4: 4GB VRAM)
 - Balanced (code + chat + reasoning)
 - Long context (128k)
 - Good multilingual

2. Qwen 2.5 14B Instruct (Q4: 8GB VRAM)
 - Better reasoning
 - Excellent multilingual
 - Math

3. Mistral 7B Instruct (Q4: 4GB VRAM)
 - Fast
 - Good reasoning
 - Compact
```

### CPU-Only (No GPU)

```
1. Phi-3 Mini (3.8B) - Q4: 2GB RAM
 - Runs on CPU (15-20 tokens/sec)
 - Good for small tasks
 - Low latency

2. TinyLlama 1.1B - Q4: 1GB RAM
 - Ultra fast CPU inference
 - Basic chatbot
 - Embedded devices
```

---

## 8. Quick Start Guide

**If you're completely new:**

```bash
# 1. Install Ollama (5 minutes)
curl -fsSL https://ollama.com/install.sh | sh

# 2. Download Llama 3.1 8B model (10 minutes, 4.7GB)
ollama pull llama3.1:8b

# 3. Start chat
ollama run llama3.1:8b

# 4. Try your first prompt
>>> Explain Kubernetes Pods in simple terms.

# 5. VSCode integration (Continue.dev)
code --install-extension continue.continue
```

**Recommended first model:**
- **Have GPU (8GB+ VRAM):** Llama 3.1 8B or Qwen 2.5 7B
- **CPU only:** Phi-3 Mini (3.8B)
- **For coding:** DeepSeek Coder 6.7B

**Budget:**
- **Beginner:** $0 (use Ollama + free models)
- **Intermediate:** $500-1000 (RTX 4060 Ti 16GB)
- **Professional:** $2000-4000 (RTX 4090 or multi-GPU setup)

---

**Created:** 2026-05-05 
**Author:** AI Assistant for portfolio demonstration 
**Purpose:** Master self-hosted LLM usage 

**Happy Local AI Running!**

---

## License

This guide is released under the **MIT License**.

---

## Contributing

Contributions welcome! Found an error or want to add use cases:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/new-usecase`)
3. Commit changes (`git commit -m 'Add RAG implementation guide'`)
4. Push (`git push origin feature/new-usecase`)
5. Open Pull Request

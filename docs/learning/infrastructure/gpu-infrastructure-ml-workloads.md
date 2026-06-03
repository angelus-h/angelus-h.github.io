# GPU Infrastructure for ML Workloads - Learning Plan

**Version:** 1.0  
**Last Updated:** 2026-06-03  
**Target Audience:** Infrastructure engineers transitioning to AI/ML infrastructure  
**Prerequisites:** Kubernetes basics, Linux system administration, Python fundamentals  
**Estimated Time:** 8-12 weeks intensive study + hands-on practice

---

## Overview

This learning plan teaches GPU-based infrastructure building and operations for machine learning workloads. The goal is to become capable of:
- Operating GPU clusters on Kubernetes
- Building ML model serving infrastructure (inference)
- Configuring distributed training environments
- GPU resource management and cost optimization

**Why Relevant:**
- AI/ML infrastructure engineer roles (e.g., Kraken Senior AI Compute Infrastructure Engineer)
- LLM serving infrastructure (backend for ChatGPT-like systems)
- Emerging field: few experienced engineers, high demand

---

## 10 Modules: From Basics to Production-Ready Infrastructure

### Module 1: GPU Fundamentals and CUDA Introduction

**Goal:** Understand how GPUs work, how they differ from CPUs, and what CUDA is.

**Learning Materials:**

1. **GPU Architecture Basics**
   - CUDA cores vs. CPU cores (parallelism)
   - Memory hierarchy: VRAM, L1/L2 cache, registers
   - Throughput vs. latency optimization
   - **Source:** NVIDIA CUDA Programming Guide (official documentation)
   - **Link:** https://docs.nvidia.com/cuda/cuda-c-programming-guide/

2. **NVIDIA GPU Hardware Overview**
   - GPU generations: Pascal → Volta → Ampere → Hopper → Blackwell
   - Datacenter GPUs: A100, H100, L40S
   - Consumer GPUs for ML use: RTX 3090, RTX 4090
   - Tensor Cores (FP16, BF16, INT8 support)
   - **Source:** NVIDIA Data Center GPU documentation
   - **Link:** https://www.nvidia.com/en-us/data-center/

3. **CUDA Basics (from Python)**
   - CUDA toolkit installation
   - GPU detection: `nvidia-smi` command
   - PyTorch/TensorFlow GPU support
   - **Practice:** Simple CUDA kernel execution from Python (CuPy library)

**Hands-on Assignment (2-3 days):**
- Install CUDA toolkit (WSL2 or native Linux)
- Run `nvidia-smi` command, interpret output
- Simple PyTorch script on GPU (matrix multiplication benchmark)
- Blog post: "GPU vs CPU: Why GPUs are faster for matrix multiplication"

**Checkpoint:**
- Can explain why GPUs are suitable for parallel workloads
- Understand VRAM vs. system RAM difference
- Can identify GPU bottlenecks (from `nvidia-smi` output)

---

### Module 2: Kubernetes GPU Support and Device Plugins

**Goal:** Using GPUs in Kubernetes cluster, resource scheduling.

**Learning Materials:**

1. **NVIDIA GPU Operator**
   - What is NVIDIA GPU Operator (Kubernetes operator pattern)
   - Components: NVIDIA driver, CUDA runtime, device plugin, monitoring
   - GPU node labeling and tainting
   - **Source:** NVIDIA GPU Operator documentation
   - **Link:** https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/

2. **Kubernetes Device Plugin Framework**
   - Device plugin API (kubelet integration)
   - GPU resource request/limit syntax (`nvidia.com/gpu: 1`)
   - GPU sharing vs. exclusive allocation
   - **Source:** Kubernetes Device Plugin documentation
   - **Link:** https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/

3. **GPU Scheduling and Node Affinity**
   - Node selector by GPU type (A100, H100)
   - Taints/tolerations for GPU nodes
   - Gang scheduling (multi-GPU distributed training)
   - **Source:** Volcano Scheduler documentation (advanced GPU scheduling)
   - **Link:** https://volcano.sh/

**Hands-on Assignment (5-7 days):**
- Kind/minikube cluster + NVIDIA GPU Operator deployment (if GPU available)
- Alternative: Cloud GPU instance (GCP n1-standard-4 + 1x T4 GPU, $0.50/hour)
- Deploy simple PyTorch pod with GPU resource request
- Monitor GPU utilization: `kubectl exec` + `nvidia-smi`
- Test GPU resource limits: what happens when 2 pods request 1 GPU?

**Checkpoint:**
- Deployed GPU Operator on Kubernetes
- Can request GPU in pod manifest
- Understand GPU sharing limitations (why exclusive allocation needed)

---

### Module 3: ML Model Serving - vLLM

**Goal:** Understanding LLM inference serving with vLLM (fastest open-source serving engine).

**Learning Materials:**

1. **What is ML Model Serving?**
   - Training vs. inference difference
   - Inference latency, throughput, concurrency
   - Batching strategies (static vs. dynamic batching)
   - **Source:** Google SRE Book - Chapter on Serving Infrastructure
   - **Link:** https://sre.google/workbook/

2. **vLLM Basics**
   - PagedAttention (vLLM key innovation - KV cache management)
   - Continuous batching (dynamic batching for LLMs)
   - vLLM vs. HuggingFace Transformers (speed comparison)
   - **Source:** vLLM official documentation
   - **Link:** https://docs.vllm.ai/

3. **vLLM Deployment on Kubernetes**
   - Docker image build with vLLM
   - Model loading (HuggingFace Hub integration)
   - GPU memory estimation (model size vs. VRAM)
   - **Source:** vLLM Kubernetes deployment guide
   - **Link:** https://docs.vllm.ai/en/latest/serving/deploying_with_kubernetes.html

**Hands-on Assignment (7-10 days):**
- Cloud GPU instance (1x A10G or T4)
- Deploy vLLM + Llama-3-8B model (or Mistral-7B)
- Test API endpoint: `/v1/completions` (OpenAI-compatible API)
- Benchmark: latency, throughput (requests/sec)
- Experiment: batch size impact on throughput
- Blog post: "vLLM benchmarking - Llama-3-8B on A10G GPU"

**Checkpoint:**
- Ran vLLM server on Kubernetes
- Understand PagedAttention concept (KV cache optimization)
- Can explain latency vs. throughput trade-off

---

### Module 4: NVIDIA Triton Inference Server

**Goal:** Multi-model, multi-framework serving (vLLM alternative/complement).

**Learning Materials:**

1. **Triton Inference Server Overview**
   - Multi-backend support (TensorRT, ONNX, PyTorch, TensorFlow)
   - Model repository structure (config.pbtxt)
   - Dynamic batching and sequence batching
   - **Source:** NVIDIA Triton documentation
   - **Link:** https://docs.nvidia.com/deeplearning/triton-inference-server/

2. **TensorRT Basics**
   - Model optimization (quantization, pruning, kernel fusion)
   - FP16/INT8 precision (speed vs. accuracy trade-off)
   - TensorRT engine building workflow
   - **Source:** NVIDIA TensorRT Developer Guide
   - **Link:** https://docs.nvidia.com/deeplearning/tensorrt/

3. **Triton Kubernetes Deployment**
   - Helm chart usage
   - Model versioning (A/B testing support)
   - Metrics endpoint (Prometheus integration)
   - **Source:** Triton Kubernetes deployment examples
   - **Link:** https://github.com/triton-inference-server/server/tree/main/deploy/k8s

**Hands-on Assignment (7-10 days):**
- Deploy Triton Inference Server on Kubernetes
- Convert PyTorch model → ONNX → TensorRT
- Serve model on Triton (ResNet or BERT)
- Compare performance: PyTorch backend vs. TensorRT backend
- Test dynamic batching (1 vs. 4 vs. 8 batch size)
- Blog post: "Triton Inference Server - Model optimization with TensorRT"

**Checkpoint:**
- Deployed Triton server
- Converted model to TensorRT
- Understand dynamic batching mechanics

---

### Module 5: Distributed Training Fundamentals

**Goal:** Understanding multi-GPU and multi-node training (data parallelism, model parallelism).

**Learning Materials:**

1. **Distributed Training Strategies**
   - Data parallelism (DDP - DistributedDataParallel)
   - Model parallelism (Tensor parallelism, Pipeline parallelism)
   - Hybrid parallelism (FSDP - Fully Sharded Data Parallel)
   - **Source:** PyTorch Distributed Training Tutorial
   - **Link:** https://pytorch.org/tutorials/beginner/dist_overview.html

2. **NCCL (NVIDIA Collective Communications Library)**
   - All-reduce, all-gather, broadcast operations
   - InfiniBand vs. Ethernet (GPU node networking)
   - NCCL performance tuning
   - **Source:** NVIDIA NCCL documentation
   - **Link:** https://docs.nvidia.com/deeplearning/nccl/

3. **PyTorch FSDP (Fully Sharded Data Parallel)**
   - ZeRO optimization (DeepSpeed origin)
   - Sharding strategies (full, hybrid, no shard)
   - Memory efficiency (vs. DDP)
   - **Source:** PyTorch FSDP Tutorial
   - **Link:** https://pytorch.org/tutorials/intermediate/FSDP_tutorial.html

**Hands-on Assignment (10-14 days):**
- Multi-GPU setup (2-4 GPUs - can use cloud: 1x p3.8xlarge AWS = 4x V100)
- Implement simple DDP training (CIFAR-10 dataset)
- Compare training time: 1 GPU vs. 2 GPUs vs. 4 GPUs (scaling efficiency)
- FSDP experiment: Train Llama-3-8B (smaller model due to budget constraints)
- Monitor GPU utilization: `nvidia-smi dmon` (all GPUs)
- Blog post: "Distributed training scaling - DDP vs. FSDP comparison"

**Checkpoint:**
- Ran multi-GPU training
- Understand data parallelism concept
- Aware of NCCL's role (inter-GPU communication)

---

### Module 6: GPU Observability and Monitoring

**Goal:** GPU metrics collection, dashboards, alerting.

**Learning Materials:**

1. **NVIDIA DCGM (Data Center GPU Manager)**
   - GPU metrics: utilization, temperature, power, memory
   - DCGM exporter (Prometheus integration)
   - GPU health checks (ECC errors, thermal throttling)
   - **Source:** NVIDIA DCGM documentation
   - **Link:** https://docs.nvidia.com/datacenter/dcgm/

2. **Prometheus + Grafana GPU Dashboards**
   - DCGM exporter deployment on Kubernetes
   - GPU metrics scraping (Prometheus ServiceMonitor)
   - Pre-built Grafana dashboards
   - **Source:** DCGM Exporter GitHub repo
   - **Link:** https://github.com/NVIDIA/dcgm-exporter

3. **Alerting Strategies**
   - GPU utilization thresholds (underutilization = cost waste)
   - Temperature alerts (thermal throttling prevention)
   - Memory saturation (OOM prediction)
   - **Source:** Prometheus Alerting Best Practices
   - **Link:** https://prometheus.io/docs/practices/alerting/

**Hands-on Assignment (5-7 days):**
- Deploy DCGM exporter on Kubernetes cluster
- Configure Prometheus scraping (GPU metrics endpoint)
- Import Grafana dashboard (NVIDIA official dashboard)
- Create custom alert: GPU utilization <20% for 1h (cost optimization signal)
- Test alert: trigger low utilization scenario
- Blog post: "GPU observability - DCGM + Prometheus + Grafana setup"

**Checkpoint:**
- See GPU metrics on Grafana dashboard
- Can write custom alerts on GPU metrics
- Understand GPU health monitoring importance (ECC errors, thermal issues)

---

### Module 7: GPU Cost Optimization

**Goal:** Minimizing cloud GPU costs, on-prem vs. cloud trade-offs.

**Learning Materials:**

1. **Cloud GPU Pricing Models**
   - On-demand vs. spot instances (AWS, GCP, Azure)
   - Reserved instances (1-3 year commitments)
   - Preemptible instances (GCP) / Spot instances (AWS) - 60-80% savings
   - **Source:** Cloud provider pricing pages
   - **Link (GCP):** https://cloud.google.com/compute/gpus-pricing
   - **Link (AWS):** https://aws.amazon.com/ec2/instance-types/p4/

2. **GPU Utilization Optimization**
   - Time-slicing (NVIDIA MIG - Multi-Instance GPU)
   - Model quantization (FP16, INT8) - cost savings via smaller GPU
   - Batch size tuning (throughput maximization)
   - **Source:** NVIDIA MIG User Guide
   - **Link:** https://docs.nvidia.com/datacenter/tesla/mig-user-guide/

3. **On-Prem GPU ROI Analysis**
   - Hardware cost (GPU servers, networking, cooling)
   - Break-even point vs. cloud (typically 12-18 months)
   - Operational costs (power, cooling, maintenance)
   - **Source type:** Industry case studies (self-reported data, 3rd party reviews)
   - **Uncertainty:** ROI depends on workload pattern (24/7 vs. bursty)

**Hands-on Assignment (3-5 days):**
- Cost analysis spreadsheet:
  - Cloud GPU cost (1x A100 on-demand vs. spot, 1 year)
  - On-prem equivalent (DGX A100 server + TCO)
  - Break-even calculation
- Test spot instance: AWS p3.2xlarge spot (check interruption rate)
- Experiment: FP16 vs. FP32 inference (speed + cost impact)
- Blog post: "GPU cost optimization - Cloud vs. on-prem analysis"

**Checkpoint:**
- Can estimate cloud GPU costs (various pricing models)
- Understand on-prem vs. cloud trade-offs
- Aware of GPU utilization optimization techniques

---

### Module 8: Advanced Kubernetes GPU Scheduling

**Goal:** Production-grade GPU scheduling (gang scheduling, bin-packing, autoscaling).

**Learning Materials:**

1. **Gang Scheduling (All-or-Nothing Scheduling)**
   - Distributed training jobs (multi-GPU/multi-node)
   - Volcano scheduler (Kubernetes job queueing)
   - PodGroup CRD (gang scheduling primitive)
   - **Source:** Volcano Scheduler documentation
   - **Link:** https://volcano.sh/en/docs/

2. **GPU Bin-Packing and Fragmentation**
   - Node fragmentation (1x 8-GPU node vs. 8x 1-GPU nodes)
   - Bin-packing strategies (first-fit, best-fit)
   - GPU topology awareness (NVLink, PCIe lanes)
   - **Source type:** Research papers (Google Borg, Microsoft Singularity)
   - **Uncertainty:** Production scheduler algorithms often proprietary

3. **Cluster Autoscaler GPU Support**
   - GKE/EKS/AKS GPU node pools
   - Scale-up/scale-down policies (min/max nodes)
   - Spot instance integration (autoscaling + spot = complex)
   - **Source:** Kubernetes Cluster Autoscaler documentation
   - **Link:** https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler

**Hands-on Assignment (7-10 days):**
- Deploy Volcano scheduler on Kubernetes cluster
- Test gang scheduling: 4-GPU distributed training job
- Simulate node fragmentation: deploy jobs sub-optimally
- Configure Cluster Autoscaler (GKE GPU node pool)
- Test autoscaling: submit large batch jobs, watch nodes scale up
- Blog post: "Advanced GPU scheduling - Volcano + Cluster Autoscaler"

**Checkpoint:**
- Deployed Volcano scheduler
- Understand gang scheduling use case
- Can configure GPU cluster autoscaling

---

### Module 9: Ray Serve and Ray Cluster

**Goal:** Understanding Ray framework (distributed computing + ML serving).

**Learning Materials:**

1. **Ray Core Basics**
   - Ray task and actor model
   - Distributed computing primitives (@ray.remote)
   - Ray cluster architecture (head node, worker nodes)
   - **Source:** Ray documentation
   - **Link:** https://docs.ray.io/en/latest/ray-core/walkthrough.html

2. **Ray Serve (ML Model Serving)**
   - Multi-model serving (1 cluster, N models)
   - Autoscaling replicas (traffic-based)
   - Ray Serve vs. vLLM (Ray Serve = general framework, vLLM = LLM-specific)
   - **Source:** Ray Serve documentation
   - **Link:** https://docs.ray.io/en/latest/serve/index.html

3. **KubeRay Operator**
   - Ray cluster Kubernetes deployment
   - RayCluster CRD
   - GPU resource allocation (heterogeneous GPU types)
   - **Source:** KubeRay documentation
   - **Link:** https://docs.ray.io/en/latest/cluster/kubernetes/index.html

**Hands-on Assignment (7-10 days):**
- Deploy Ray cluster on Kubernetes (KubeRay operator)
- Simple Ray task: distributed data processing (GPU-accelerated)
- Deploy Ray Serve + simple model (BERT fine-tuned model)
- Test autoscaling: load test Ray Serve endpoint
- Compare: Ray Serve vs. Triton Inference Server (use case fit)
- Blog post: "Ray Serve - Distributed ML serving on Kubernetes"

**Checkpoint:**
- Deployed Ray cluster on Kubernetes
- Ran Ray Serve for model serving
- Understand Ray framework use case (vs. pure Kubernetes jobs)

---

### Module 10: Production Case Study and Capstone Project

**Goal:** Building real production-like GPU infrastructure end-to-end.

**Capstone Project Description:**

**Task:** Build LLM serving infrastructure on Kubernetes (Llama-3-8B model).

**Requirements:**
1. **Infrastructure:**
   - Kubernetes cluster (GKE or EKS, 2x GPU nodes - A10G or L4)
   - NVIDIA GPU Operator installation
   - Prometheus + Grafana monitoring

2. **ML Serving:**
   - vLLM deployment (Llama-3-8B model)
   - OpenAI-compatible API endpoint
   - HorizontalPodAutoscaler (HPA) - based on CPU/GPU metrics

3. **Observability:**
   - GPU metrics (DCGM exporter)
   - Latency/throughput metrics (Prometheus)
   - Grafana dashboard (custom + pre-built)

4. **Cost Optimization:**
   - Spot instance usage (node pool)
   - Cluster Autoscaler (scale-to-zero capable)
   - Cost report (estimated $/month, $/request)

5. **Documentation:**
   - Architecture diagram (Kubernetes resources, data flow)
   - Runbook (deployment, troubleshooting, scaling)
   - Blog post series (3-5 posts, publish on Medium/Dev.to)

**Timeline:** 14-21 days

**Success Criteria:**
- Working vLLM API endpoint (<2s latency, 95th percentile)
- GPU utilization >60% (efficient resource use)
- Autoscaling works (scale-up under load, scale-down after)
- Documentation publication-ready (blog posts, GitHub repo)

**Checkpoint:**
- Full end-to-end GPU infrastructure deployed
- Blog posts published (LinkedIn share, portfolio)
- GitHub repository public (resume/CV reference)

---

## 13 Practices: Hands-On Skill Development

### Practice 1: Daily `nvidia-smi` Habit
- **What:** Run `nvidia-smi` command daily, interpret output
- **Why:** Develop GPU monitoring intuition
- **Time:** 5 minutes/day
- **Tool:** Terminal + NVIDIA GPU (cloud or local)

### Practice 2: Weekly GPU Benchmark Runs
- **What:** Weekly benchmarks on different GPUs (T4, A10G, A100)
- **Why:** Understand hardware performance characteristics
- **Time:** 1-2 hours/week
- **Tool:** Cloud GPU instances (GCP, AWS free tier credits)

### Practice 3: Model Serving Latency Optimization
- **What:** vLLM or Triton latency tuning (batch size, precision)
- **Why:** Production serving performance tuning skill
- **Time:** 3-4 hours/week
- **Tool:** vLLM + load testing tool (Locust, k6)

### Practice 4: Kubernetes GPU Troubleshooting Scenarios
- **What:** Simulated failure scenarios (GPU OOM, driver crash, pod eviction)
- **Why:** Incident response preparation
- **Time:** 2-3 hours/week
- **Tool:** Kubernetes cluster + Chaos Engineering tools (Chaos Mesh)

### Practice 5: GPU Cost Tracking Spreadsheet
- **What:** Weekly GPU cost tracking (cloud spend, utilization)
- **Why:** Cost awareness, optimization habit
- **Time:** 30 minutes/week
- **Tool:** Google Sheets + cloud billing API

### Practice 6: Read GPU Performance Papers
- **What:** Weekly 1 research paper on GPU optimization
- **Why:** Learn cutting-edge techniques
- **Time:** 2-3 hours/week
- **Sources:** arXiv.org (cs.DC, cs.LG categories), NVIDIA Technical Blog

### Practice 7: Contribute to Open-Source GPU Projects
- **What:** Issue filing, documentation PR, small bug fix (vLLM, Triton, KubeRay)
- **Why:** Community engagement, code review exposure
- **Time:** 2-4 hours/week
- **Platform:** GitHub (vLLM, NVIDIA Triton repos)

### Practice 8: LinkedIn GPU Content Sharing
- **What:** Weekly post: learning insight, benchmark result, tool comparison
- **Why:** Personal brand building, network expansion
- **Time:** 30 minutes/week
- **Platform:** LinkedIn (tech hashtags: #GPUComputing, #MLOps, #Kubernetes)

### Practice 9: Mock Interview Practice
- **What:** Practice GPU infra interview questions (technical + behavioral)
- **Why:** Interview readiness
- **Time:** 1 hour/week
- **Format:** Self-recorded video or peer mock interview

### Practice 10: Architecture Review Sessions
- **What:** Review existing GPU infra architectures (open-source, blog posts)
- **Why:** Design pattern recognition, trade-off analysis skill
- **Time:** 2 hours/week
- **Sources:** Company engineering blogs (OpenAI, Anthropic, Cohere)

### Practice 11: Failure Log Analysis
- **What:** Collect GPU failure logs (OOM, CUDA errors), analyze root cause
- **Why:** Debugging skill, common failure patterns knowledge
- **Time:** 1-2 hours/week
- **Tool:** Kubernetes logs, DCGM logs, application logs

### Practice 12: GPU Tech News Curation
- **What:** Weekly GPU tech news summary (new hardware, framework releases)
- **Why:** Industry trend awareness
- **Time:** 1 hour/week
- **Sources:** NVIDIA Blog, The Register (HPC section), Hacker News

### Practice 13: Certification Path Tracking
- **What:** Track progress: NVIDIA DLI courses, CKA (Certified Kubernetes Administrator)
- **Why:** Formal credentials (resume value)
- **Time:** Variable (certification-dependent)
- **Platform:** NVIDIA DLI, CNCF (Kubernetes certifications)

---

## Source Hierarchy and Reliability

### Primary Sources (Most Reliable)
1. **NVIDIA official documentation** (CUDA, DCGM, GPU Operator, TensorRT)
2. **Kubernetes official documentation** (Device Plugins, Scheduling)
3. **Cloud provider documentation** (GCP, AWS, Azure GPU offerings)
4. **PyTorch/TensorFlow official tutorials** (Distributed training)

### Professional Analyses (High Reliability)
1. **NVIDIA Technical Blog** - GPU performance optimization articles
2. **Google Research publications** - TPU/GPU scheduling papers
3. **Microsoft Research** - Azure ML infrastructure papers
4. **Meta Engineering Blog** - PyTorch FSDP, distributed training

### Community Sources (Medium Reliability)
1. **GitHub repositories** (vLLM, Triton, KubeRay - code + issues + discussions)
2. **Reddit r/MachineLearning** - Practitioner insights (consider bias)
3. **Hacker News** - Industry discussions (multiple perspectives)
4. **Medium/Dev.to blog posts** - Hands-on tutorials (verify claims)

### Sources to Verify (Low Reliability)
1. **Marketing materials** (vendor bias - NVIDIA vs. AMD vs. Intel)
2. **Anecdotal Reddit comments** - Useful starting point, but verify!
3. **Outdated tutorials** (GPU tech changes fast - check publish date)

---

## Possible Biases and Uncertainties

### Technological Biases
- **NVIDIA dominance:** Curriculum is NVIDIA-centric (CUDA ecosystem)
  - **Alternatives:** AMD ROCm, Intel oneAPI (less mature, but exist)
  - **Bias:** NVIDIA marketing materials may influence objectivity

### Cost Uncertainties
- **Cloud GPU pricing volatility:** Spot instance prices fluctuate (supply/demand)
  - **Mitigation:** Always check current pricing, use cost calculators
- **On-prem ROI assumptions:** TCO calculation depends on workload pattern
  - **Uncertainty:** 12-18 month break-even is speculation (no universal rule)

### Learning Curve Uncertainties
- **8-12 week estimate:** Assumption of daily 3-4 hours intensive study
  - **Variance:** Depends on individual background (ML knowledge, Kubernetes experience)
  - **Reality check:** Production-ready skillset may take 6-12 months (part-time)

### Practical Constraints
- **GPU access cost:** Cloud GPU practice costs $200-500 (budget-dependent)
  - **Alternative:** Google Colab Pro ($10/month, limited GPU time)
  - **Trade-off:** Cheaper, but less production-like environment

### Source Freshness
- **Rapid GPU tech evolution:** 6-month-old tutorial may be outdated
  - **Mitigation:** Always check documentation publish date
  - **Best practice:** Official docs > blog posts (docs maintained, blogs static)

---

## Next Steps (Using This Learning Plan)

1. **Assess commitment:**
   - Do you have 8-12 weeks for intensive learning?
   - Do you have $200-500 budget for cloud GPU practice?
   - **If NO:** Consider part-time schedule (3-6 months, daily 1-2 hours)

2. **Start with Module 1:**
   - Don't skip ahead (fundamentals are critical)
   - Hands-on assignments are mandatory (passive reading ≠ skill)

3. **Track progress:**
   - Markdown checklist (checkbox after each module)
   - Blog posts = public accountability

4. **Adjust as needed:**
   - If a module is too fast/slow → customize
   - If a tech stack isn't relevant (e.g., Ray Serve) → skip or replace

5. **Portfolio building in parallel:**
   - GitHub repo for every capstone project
   - LinkedIn posts at every milestone
   - **Goal:** 3-6 months later, credible "GPU Infrastructure Engineer" profile

---

**Last Updated:** 2026-06-03  
**Version:** 1.0  
**Feedback/Questions:** Open an issue or comment on this document!

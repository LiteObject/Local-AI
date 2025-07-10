# AI Model Formats & Types Guide

## Model Formats

Model formats determine how models are stored, optimized, and loaded for inference.

### Full Precision Formats

Original model weights without compression (FP32/BF16/FP16):

| Format | Description | Use Case |
|--------|-------------|----------|
| `.safetensors` | Secure tensor format | Hugging Face models (recommended) |
| `.pt` / `.pth` | PyTorch native format | PyTorch development |
| `.bin` | Legacy binary format | Older Hugging Face models |
| `.ckpt` | Checkpoint format | TensorFlow, older PyTorch |

### Quantized Formats

Compressed models with reduced precision for efficiency:

| Format | Precision | Best For | Quality |
|--------|-----------|----------|---------|
| **GGUF** | 2-8 bit | CPU/Mixed inference | Excellent |
| **GPTQ** | 3-4 bit | GPU inference | Very Good |
| **AWQ** | 4 bit | GPU inference | Excellent |
| **GGML** | 2-8 bit | Legacy (use GGUF) | Good |

#### GGUF Quantization Levels
- **Q2_K** - 2.6 bpw, smallest size, lowest quality
- **Q3_K_M** - 3.3 bpw, good balance
- **Q4_K_M** - 4.4 bpw, recommended default
- **Q5_K_M** - 5.1 bpw, high quality
- **Q6_K** - 6.6 bpw, very high quality
- **Q8_0** - 8.5 bpw, almost lossless

### Specialized Formats

Optimized for specific hardware or deployment:

| Format | Target Platform | Notes |
|--------|----------------|-------|
| `.onnx` | Cross-platform | Universal compatibility |
| `.tflite` | Mobile/Edge | TensorFlow Lite |
| `.trt` | NVIDIA GPUs | TensorRT optimization |
| `.mlmodel` | Apple devices | CoreML format |
| `.openvino` | Intel hardware | OpenVINO toolkit |

## Model Architectures

### Core Architectures

| Architecture | Purpose | Examples |
|--------------|---------|----------|
| **Decoder-only** | Text generation | GPT-4, Llama, Mistral |
| **Encoder-only** | Text understanding | BERT, RoBERTa |
| **Encoder-Decoder** | Translation, summarization | T5, FLAN-T5 |

### Model Categories by Use Case

#### General Purpose
- **Llama 3.1** - Meta's flagship model series
- **Mistral** - Efficient European model
- **Qwen 2.5** - Alibaba's multilingual model
- **Gemma 2** - Google's open model

#### Code Specialized
- **CodeLlama** - Code generation and completion
- **StarCoder2** - Multi-language programming
- **DeepSeek Coder** - Strong coding performance
- **CodeGemma** - Google's code model

#### Chat Optimized
- **Llama 3.1 Instruct** - Instruction-following
- **Mistral Instruct** - Conversational AI
- **Vicuna** - ChatGPT-style responses
- **OpenHermes** - High-quality chat model

#### Multimodal
- **LLaVA** - Vision + language understanding
- **Bakllava** - Mistral-based vision model
- **Moondream** - Lightweight vision model

## Choosing the Right Format

### For Local CPU Inference
- Use **GGUF Q4_K_M** for best balance
- Use **GGUF Q5_K_M** for higher quality
- Use **GGUF Q2_K** for minimal RAM usage

### For GPU Inference
- Use **GPTQ** or **AWQ** for NVIDIA GPUs
- Use **safetensors** for full precision
- Use **GGUF** for mixed CPU/GPU inference

### For Production Deployment
- Use **ONNX** for cross-platform compatibility
- Use **TensorRT** for NVIDIA GPU optimization
- Use **OpenVINO** for Intel hardware
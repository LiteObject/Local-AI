# AI Model Format & Types

## Model Formats
Model formats, which determine how a model is stored, optimized, and loaded. Here are the most common ones:

### 1. Full Precision Models
These are the original, unoptimized versions of models, typically in FP32 or BF16 precision.
- **`.safetensors`** – A secure alternative to `.bin`, used mainly in Hugging Face Transformers.
- **`.pt` / `.pth`** – PyTorch model format.
- **`.ckpt`** – Checkpoint format, used in TensorFlow and some older PyTorch models.

### 2. Quantized & Optimized Formats
These formats reduce memory usage while maintaining reasonable accuracy:
- **`.gguf`** – The latest format for `llama.cpp`, supporting various quantization levels (2-bit, 3-bit, 4-bit, etc.).
- **`.ggml`** – Older version of `gguf`, used in `llama.cpp` but mostly replaced by `gguf`.
- **`.gptq`** – GPTQ quantization for faster inference on GPUs.
- **`.awq`** – Activation-aware quantization for improved efficiency on GPUs.
- **`.safetensors (quantized)`** – Used with quantized Hugging Face models.

### 3. On-Device & Specialized Formats
These formats optimize for specific hardware or frameworks:
- **`.tflite`** – TensorFlow Lite format for mobile and edge devices.
- **`.onnx`** – Open Neural Network Exchange, used for cross-platform compatibility.
- **`.trt`** – TensorRT format optimized for NVIDIA GPUs.
- **`.mlmodel`** – CoreML format for Apple devices.

## Model Types
Model **types** refer to the architectures and capabilities of different LLMs, such as:  

### 1. Model Architectures (Types of LLMs)
These define how a model is built and functions:
- **Decoder-only (Autoregressive models)** – Used for text generation (e.g., GPT-4, LLaMA, Mistral).  
- **Encoder-only (Bidirectional models)** – Used for understanding and classification (e.g., BERT, RoBERTa).  
- **Encoder-Decoder (Seq2Seq models)** – Used for translation, summarization, etc. (e.g., T5, FLAN-T5).  

### 2. Model Variants (Fine-Tuned for Tasks)
- **General-purpose LLMs** – GPT-4, LLaMA, Mistral (for broad tasks).  
- **Code models** – StarCoder, CodeLLaMA (for programming).  
- **Chat models** – GPT-4-turbo, Vicuna, OpenAssistant (for conversational AI).  
- **Multimodal models** – Gemini, GPT-4V, LLaVA (for text + image understanding).  

So while **formats** (like `.gguf`, `.onnx`) define how a model is stored and optimized, **model types** refer to architectures and purposes. 
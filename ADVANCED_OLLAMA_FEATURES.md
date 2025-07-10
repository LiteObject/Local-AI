# Advanced Ollama Features

## Custom Models with Modelfiles

```dockerfile
FROM llama3.1:8b
SYSTEM "You are a Python coding expert."
TEMPLATE """{{ .System }}
User: {{ .Prompt }}
Assistant: """
PARAMETER temperature 0.1
PARAMETER stop "User:"
```

```bash
ollama create python-expert -f ./Modelfile
ollama run python-expert
```

## 2. Multi-Model Management

### Running Multiple Models
```bash
# List all available models
ollama list

# Run different models simultaneously
ollama serve &  # Start server
ollama run llama3.1:8b &
ollama run codellama:7b &
```

### API-Based Model Switching
```python
import requests

# Switch between models via API
def query_model(model, prompt):
    response = requests.post('http://localhost:11434/api/generate', 
        json={'model': model, 'prompt': prompt})
    return response.json()

# Use different models for different tasks
code_result = query_model('codellama:7b', 'Write a Python function')
text_result = query_model('llama3.1:8b', 'Explain this concept')
```

## 3. GPU Optimization

### Automatic GPU Detection
Ollama automatically detects and uses available GPUs (NVIDIA CUDA, AMD ROCm, Apple Metal).

### Manual GPU Configuration
```bash
# Set GPU layers (how much of model runs on GPU)
OLLAMA_NUM_GPU_LAYERS=35 ollama run llama3.1:8b

# Limit GPU memory usage
OLLAMA_GPU_MEMORY_FRACTION=0.8 ollama serve

# Use specific GPU in multi-GPU setup
CUDA_VISIBLE_DEVICES=0 ollama run llama3.1:8b
```

### Performance Monitoring
```bash
# Check GPU usage
nvidia-smi

# Monitor Ollama performance
ollama ps  # Show running models and resource usage
```

## 4. Model Quantization & Variants

### Available Quantization Levels
```bash
# Different quantization levels for same model
ollama pull llama3.1:8b-q2_K     # 2-bit (smallest)
ollama pull llama3.1:8b-q4_K_M   # 4-bit (recommended)
ollama pull llama3.1:8b-q6_K     # 6-bit (high quality)
ollama pull llama3.1:8b-q8_0     # 8-bit (near full precision)
```

### Custom Quantization
```dockerfile
# Modelfile with specific quantization
FROM llama3.1:8b-q4_K_M
SYSTEM "You are a helpful assistant."
PARAMETER num_ctx 4096  # Context length
PARAMETER num_gpu 35    # GPU layers
```

## 5. API Integration & Development

### REST API
```python
# Complete API example
import requests
import json

def chat_with_ollama(model, messages):
    response = requests.post('http://localhost:11434/api/chat',
        json={
            'model': model,
            'messages': messages,
            'stream': False
        })
    return response.json()['message']['content']

# Usage
messages = [{'role': 'user', 'content': 'Hello!'}]
response = chat_with_ollama('llama3.1:8b', messages)
```

### LangChain Integration
```python
from langchain_community.llms import Ollama
from langchain.chains import RetrievalQA

# Initialize Ollama with LangChain
llm = Ollama(model="llama3.1:8b")

# Create RAG pipeline
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vector_store.as_retriever()
)
```

### Docker Integration
```dockerfile
# Dockerfile for Ollama service
FROM ollama/ollama:latest
RUN ollama pull llama3.1:8b
EXPOSE 11434
CMD ["ollama", "serve"]
```

## 6. Advanced Configuration

### Parameter Tuning
```bash
# Runtime parameters
ollama run llama3.1:8b --temperature 0.7 --top-p 0.9 --repeat-penalty 1.1
```

### Environment Variables
```bash
# Server configuration
export OLLAMA_HOST=0.0.0.0:11434      # Bind to all interfaces
export OLLAMA_ORIGINS="*"              # CORS origins
export OLLAMA_MODELS="/custom/path"    # Custom model directory
export OLLAMA_NUM_PARALLEL=4           # Parallel requests
export OLLAMA_MAX_LOADED_MODELS=3      # Max concurrent models
```

### System Prompts & Templates
```dockerfile
# Advanced Modelfile
FROM llama3.1:8b

TEMPLATE """
{{ if .System }}<|start_header_id|>system<|end_header_id|>

{{ .System }}<|eot_id|>{{ end }}{{ if .Prompt }}<|start_header_id|>user<|end_header_id|>

{{ .Prompt }}<|eot_id|>{{ end }}<|start_header_id|>assistant<|end_header_id|>

"""

SYSTEM "You are a helpful AI assistant."
PARAMETER stop "<|eot_id|>"
PARAMETER temperature 0.7
```

## 7. Model Management & Versioning

### Model Tagging
```bash
# Create tagged versions
ollama create my-model:v1 -f ./Modelfile.v1
ollama create my-model:v2 -f ./Modelfile.v2
ollama create my-model:latest -f ./Modelfile.latest

# List model versions
ollama list | grep my-model

# Remove specific versions
ollama rm my-model:v1
```

### Model Import/Export
```bash
# Export model for backup
ollama show my-model --modelfile > my-model.Modelfile

# Import model on another system
ollama create my-model -f ./my-model.Modelfile

# Copy models between systems
ollama cp source-model target-model
```

## 8. Production Deployment

### Load Balancing
```yaml
# docker-compose.yml for scaled deployment
version: '3.8'
services:
  ollama-1:
    image: ollama/ollama
    ports: ["11434:11434"]
    volumes: ["./models:/root/.ollama"]
  
  ollama-2:
    image: ollama/ollama
    ports: ["11435:11434"]
    volumes: ["./models:/root/.ollama"]
  
  nginx:
    image: nginx
    ports: ["80:80"]
    volumes: ["./nginx.conf:/etc/nginx/nginx.conf"]
```

### Monitoring & Logging
```bash
# Enable debug logging
OLLAMA_DEBUG=1 ollama serve

# Monitor with systemd
sudo systemctl status ollama
sudo journalctl -u ollama -f

# Health check endpoint
curl http://localhost:11434/api/tags
```

### Security Configuration
```bash
# Restrict access
export OLLAMA_HOST=127.0.0.1:11434  # Local only
export OLLAMA_ORIGINS="https://myapp.com"  # Specific origins

# Use reverse proxy with authentication
# nginx, Traefik, or similar
```

## 9. Performance Optimization

### Memory Management
```bash
# Control memory usage
export OLLAMA_MAX_VRAM=8GB           # Limit VRAM usage
export OLLAMA_SWAP_SIZE=4GB          # Enable swap for large models
export OLLAMA_NUM_THREAD=8           # CPU threads for inference
```

### Batch Processing
```python
# Efficient batch processing
import asyncio
import aiohttp

async def process_batch(prompts, model="llama3.1:8b"):
    async with aiohttp.ClientSession() as session:
        tasks = []
        for prompt in prompts:
            task = session.post('http://localhost:11434/api/generate',
                json={'model': model, 'prompt': prompt})
            tasks.append(task)
        
        responses = await asyncio.gather(*tasks)
        return [await r.json() for r in responses]
```

### Caching Strategies
```bash
# Enable model caching
export OLLAMA_KEEP_ALIVE=24h         # Keep models loaded
export OLLAMA_MAX_LOADED_MODELS=5    # Cache multiple models

# Preload frequently used models
ollama run llama3.1:8b "" --keep-alive 24h
```

## Best Practices

### Development Workflow
1. **Start Simple** - Begin with basic models and configurations
2. **Iterate Quickly** - Use Modelfiles for rapid experimentation
3. **Monitor Performance** - Track resource usage and response times
4. **Version Control** - Tag and manage model versions systematically
5. **Test Thoroughly** - Validate model behavior before production

### Production Checklist
- [ ] Configure appropriate resource limits
- [ ] Set up monitoring and logging
- [ ] Implement health checks
- [ ] Configure security restrictions
- [ ] Plan for model updates and rollbacks
- [ ] Document API usage and parameters

Ollama's advanced features enable sophisticated AI deployments while maintaining simplicity and local control. These capabilities make it suitable for everything from development experimentation to production-scale applications.
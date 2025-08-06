# Advanced Ollama Tricks (For When You Want to Get Fancy)

*Okay, so you've got the basics down and want to see what else Ollama can do. Here's the fun stuff.*

## Creating custom models with Modelfiles

Think of Modelfiles like recipes - you take an existing model and customize how it behaves:

```dockerfile
FROM llama3.3:8b
SYSTEM "You are a Python coding expert who explains things clearly."
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

Now you've got a model that's specifically tuned to help with Python and gives more focused, less random responses.

## Running multiple models at once

This is actually pretty useful - you can have different models for different tasks:

```bash
# See what models you have
ollama list

# Start the Ollama server in the background
ollama serve &  

# Now you can run multiple models
ollama run llama3.3:8b &
ollama run qwen2.5-coder:7b &
ollama run deepseek-r1:7b &
```

### Switching between models via API
```python
import requests

def ask_model(model, question):
    response = requests.post('http://localhost:11434/api/generate', 
        json={'model': model, 'prompt': question})
    return response.json()

# Use different models for different things
code_answer = ask_model('qwen2.5-coder:7b', 'Write a Python function to sort a list')
general_answer = ask_model('llama3.3:8b', 'Explain quantum computing simply')
reasoning_answer = ask_model('deepseek-r1:7b', 'Solve this complex logic puzzle: ...')
```

Pretty neat - you can have a coding specialist and a general knowledge model running side by side.

## GPU optimization (making things faster)

Ollama is pretty smart about using your GPU automatically, but you can tweak things:

### Automatic GPU detection
Ollama automatically finds and uses your GPU (NVIDIA, AMD, or Apple's chips). Usually it just works.

### Manual GPU tweaking
```bash
# Control how much of the model runs on GPU (higher = faster but uses more VRAM)
OLLAMA_NUM_GPU_LAYERS=35 ollama run llama3.1:8b

# Limit how much GPU memory to use (helpful if you're also gaming)
OLLAMA_GPU_MEMORY_FRACTION=0.8 ollama serve

# Use a specific GPU if you have multiple
CUDA_VISIBLE_DEVICES=0 ollama run llama3.1:8b
```

### Check what's happening
```bash
# See GPU usage (NVIDIA cards)
nvidia-smi

# See what Ollama is doing
ollama ps  # Shows running models and resource usage
```

**Reality check:** The defaults usually work fine. Only mess with this if you're having performance issues.

## Model quantization levels (quality vs size trade-offs)

Different compression levels of the same model - think video quality settings:

```bash
# Different quality levels for the same model
ollama pull llama3.1:8b-q2_K     # Smallest file, lowest quality
ollama pull llama3.1:8b-q4_K_M   # Good balance (recommended)
ollama pull llama3.1:8b-q6_K     # Larger file, high quality
ollama pull llama3.1:8b-q8_0     # Huge file, near-perfect quality
```

### Custom quantization in Modelfiles
```dockerfile
# Modelfile with specific settings
FROM llama3.1:8b-q4_K_M
SYSTEM "You are a helpful assistant."
PARAMETER num_ctx 4096  # How much context to remember
PARAMETER num_gpu 35    # How many layers to put on GPU
```

**Practical advice:** q4_K_M is the sweet spot for most people. Only go higher if you have tons of RAM and want max quality.

## API integration for developers

This is where Ollama really shines - you can integrate it into your own apps:

### Basic REST API usage
```python
# Simple chat completion
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

# Example usage
messages = [{'role': 'user', 'content': 'Hello there!'}]
response = chat_with_ollama('llama3.1:8b', messages)
print(response)
```

### LangChain integration (for RAG and complex workflows)
```python
from langchain_community.llms import Ollama
from langchain.chains import RetrievalQA

# Connect Ollama to LangChain
llm = Ollama(model="llama3.1:8b")

# Create a retrieval-augmented generation (RAG) pipeline
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=your_vector_store.as_retriever()
)
```

### Running in Docker
```dockerfile
# Dockerfile for Ollama in a container
FROM ollama/ollama:latest
RUN ollama pull llama3.1:8b
EXPOSE 11434
CMD ["ollama", "serve"]
```

This stuff gets pretty technical, but it's powerful once you get the hang of it.

## Fine-tuning behavior with parameters

You can adjust how models respond by tweaking runtime parameters:

```bash
# Make responses more creative/random
ollama run llama3.1:8b --temperature 0.9 --top-p 0.9 

# Make responses more focused/deterministic
ollama run llama3.1:8b --temperature 0.1 --repeat-penalty 1.2
```

### Environment variables for server configuration
```bash
# Make Ollama accessible from other computers on your network
export OLLAMA_HOST=0.0.0.0:11434      

# Allow requests from web apps
export OLLAMA_ORIGINS="*"              

# Store models somewhere else
export OLLAMA_MODELS="/custom/path"    

# Handle more simultaneous requests
export OLLAMA_NUM_PARALLEL=4           

# Keep more models loaded in memory
export OLLAMA_MAX_LOADED_MODELS=3      
```

### Advanced Modelfile example
```dockerfile
# More sophisticated model customization
FROM llama3.1:8b

TEMPLATE """
{{ if .System }}<|start_header_id|>system<|end_header_id|>

{{ .System }}<|eot_id|>{{ end }}{{ if .Prompt }}<|start_header_id|>user<|end_header_id|>

{{ .Prompt }}<|eot_id|>{{ end }}<|start_header_id|>assistant<|end_header_id|>

"""

SYSTEM "You are a helpful AI assistant with a sense of humor."
PARAMETER stop "<|eot_id|>"
PARAMETER temperature 0.7
```

**Honestly:** The defaults work fine for most people. Only mess with this stuff if you have specific needs.

## Managing your model collection

### Versioning and tagging
```bash
# Create different versions of customized models
ollama create my-assistant:v1 -f ./Modelfile.v1
ollama create my-assistant:v2 -f ./Modelfile.v2
ollama create my-assistant:latest -f ./Modelfile.latest

# See all your models and versions
ollama list | grep my-assistant

# Clean up old versions
ollama rm my-assistant:v1
```

### Backup and sharing
```bash
# Export a model's configuration for backup
ollama show my-assistant --modelfile > my-assistant.Modelfile

# Import on another computer
ollama create my-assistant -f ./my-assistant.Modelfile

# Copy models between different names
ollama cp source-model target-model
```

This is handy when you've spent time tweaking a model and want to save that configuration.

## Production deployment (if you're building something serious)

### Load balancing with Docker Compose
```yaml
# docker-compose.yml for running multiple Ollama instances
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

### Monitoring and logging
```bash
# Debug mode for troubleshooting
OLLAMA_DEBUG=1 ollama serve

# System service monitoring (Linux)
sudo systemctl status ollama
sudo journalctl -u ollama -f

# Health check
curl http://localhost:11434/api/tags
```

### Security for production
```bash
# Restrict access to localhost only
export OLLAMA_HOST=127.0.0.1:11434  

# Only allow specific websites to connect
export OLLAMA_ORIGINS="https://myapp.com"  

# Use a reverse proxy (nginx, Traefik, etc.) with authentication
```

## Performance optimization tricks

### Memory management
```bash
# Control memory usage
export OLLAMA_MAX_VRAM=8GB           # Don't use all your graphics memory
export OLLAMA_SWAP_SIZE=4GB          # Use system RAM for large models
export OLLAMA_NUM_THREAD=8           # CPU threads for processing
```

### Batch processing for efficiency
```python
# Process multiple requests efficiently
import asyncio
import aiohttp

async def process_multiple_prompts(prompts, model="llama3.1:8b"):
    async with aiohttp.ClientSession() as session:
        tasks = []
        for prompt in prompts:
            task = session.post('http://localhost:11434/api/generate',
                json={'model': model, 'prompt': prompt})
            tasks.append(task)
        
        responses = await asyncio.gather(*tasks)
        return [await r.json() for r in responses]
```

### Smart caching
```bash
# Keep models loaded longer (saves startup time)
export OLLAMA_KEEP_ALIVE=24h         

# Cache multiple models in memory
export OLLAMA_MAX_LOADED_MODELS=5    

# Preload models you use frequently
ollama run llama3.1:8b "" --keep-alive 24h
```

## Some practical advice from experience

### Development workflow that actually works
1. **Start simple** - Get basic functionality working first
2. **Iterate quickly** - Use Modelfiles to test different behaviors rapidly
3. **Monitor everything** - Keep an eye on resource usage, especially at first
4. **Version your models** - Tag different configurations so you can roll back
5. **Test thoroughly** - AI models can be unpredictable, so test edge cases

### Production deployment checklist
- [ ] Set appropriate resource limits
- [ ] Configure monitoring and alerting
- [ ] Set up health checks
- [ ] Implement proper security (don't expose to the internet without authentication)
- [ ] Plan for model updates and rollbacks
- [ ] Document your API usage and parameters

**Bottom line:** Ollama is surprisingly powerful once you dig into it. You can build some pretty sophisticated AI applications while keeping everything running on your own hardware. The learning curve isn't too steep, and the flexibility is worth it.
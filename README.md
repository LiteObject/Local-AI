# Run AI Models on Your Computer

T## Step 2: Figure out what your computer can handle

This is important - you can't run huge models on a potato computer. But don't worry, there are good options for everyone:

| What you've got | What you can run | How good is it? |
|-----------------|------------------|-----------------|
| Basic laptop (8GB RAM) | 3B-7B models | Pretty decent for most stuff |
| Gaming rig (good GPU) | 13B models | Really good, honestly |
| Beast machine (16GB+ GPU) | 30B+ models | Scary good |

**Not sure what you have?** 
- Windows: Right-click "This PC" → Properties
- Mac: Apple Menu → About This Mac
- Linux: You probably already know, but `lscpu` and `free -h` if you don'ting $20/month for ChatGPT? Yeah, me too. Here's how to run these AI models on your own machine - completely free and private.

## Why I switched to running AI locally

Look, I was skeptical at first. But after using local AI for months, I'm never going back to paid services for most tasks. Here's why:

- **It's actually free** - No subscription fees eating into your budget
- **Your conversations stay private** - Nothing gets sent to some company's servers
- **Works when your internet doesn't** - Perfect for flights or sketchy WiFi
- **You can tinker with it** - Want to modify how the AI behaves? Go for it.

## Step 1: Pick your tool (this matters)

### If you hate command lines
**[LM Studio](https://lmstudio.ai/)** - This one's got a nice interface
1. Download it and install (pretty straightforward)
2. Browse models in the app - they've got tons
3. Hit download, wait a bit, then start chatting

### If you're okay with typing commands
**[Ollama](https://ollama.com/)** - My personal favorite
```bash
# Mac/Linux folks:
curl -fsSL https://ollama.com/install.sh | sh

# Windows people: Just download from the website

# Then try this:
ollama run llama3.2:3b
```

Honestly, I'd recommend starting with LM Studio if you're new to this stuff. You can always try Ollama later.

## Step 2: Check Your Hardware

| Your Computer Has | You Can Run | Quality |
|-------------------|-------------|---------|
| 8GB RAM, no gaming GPU | 7B models | Good for most tasks |
| Gaming GPU (8GB+) | 13B models | Very good |
| High-end GPU (16GB+) | 30B+ models | Excellent |

**Don't know your specs?** 
- Windows: Right-click "This PC" → Properties
- Mac: Apple Menu → About This Mac

## Step 3: Download your first model

I've tried a bunch of these. Here are the ones that actually work well:

- **llama3.2:3b** - Start here. It's fast, works on anything, and surprisingly good
- **phi3.5:3.8b** - Microsoft made this one. Also pretty solid
- **qwen2.5:7b** - Great if you need multiple languages
- **qwen2.5-coder:7b** - Best one I've found for coding help
- **smollm2:1.7b** - New lightweight option that's surprisingly capable

Honestly, just start with `llama3.2:3b`. You can always download more later (and trust me, you will).

**📋 [Detailed model breakdown →](MODEL_GUIDE.md)**
**🆕 [What I'm using right now →](CURRENT_MODEL_RECOMMENDATIONS.md)**

## Step 4: Actually start using it

### If you went with LM Studio
1. Download a model from the search tab (I'd suggest llama3.2:3b)
2. Switch to the chat tab
3. Pick your model from the dropdown and start typing

### If you went with Ollama
```bash
ollama run llama3.2:3b
>>> Hey there! What can I help you with?
```

That's it. You're now running AI on your own machine. Pretty cool, right?

## When things go wrong (they sometimes do)

**Model running like molasses?** Try something smaller like `phi3:mini`
**Computer says "out of memory"?** Your machine might need more RAM, or try a smaller model
**Installation failing?** Restart your computer and check if your antivirus is being overly paranoid

## More stuff to read

- [What Are AI Models?](./WHAT_ARE_AI_MODELS.md) - If you're curious how this magic works
- [Tool Comparison](./TOOL_COMPARISON.md) - Deep dive into your options
- [Model Guide](./MODEL_GUIDE.md) - Which models are actually good
- [Current Favorites](./CURRENT_MODEL_RECOMMENDATIONS.md) - What I'm using lately
- [File Formats Explained](./MODEL_FORMATS_AND_TYPES.md) - The technical stuff
- [Advanced Ollama Tricks](./ADVANCED_OLLAMA_FEATURES.md) - For when you want to get fancy

## Stuck?

- 90% of problems are solved by trying a smaller model first
- Check if your antivirus is blocking stuff
- When in doubt, restart and try again
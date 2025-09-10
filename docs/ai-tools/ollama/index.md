# Ollama

> **Local AI models for privacy-sensitive development work**

Ollama runs large language models locally on your machine, providing complete privacy and control over your AI interactions. Perfect for working with sensitive code, proprietary projects, or when you need offline AI capabilities.

## 🎯 Why Ollama?

- **Complete Privacy** - Models run entirely on your local machine
- **Offline Capability** - No internet connection required after model download
- **Cost Control** - No API costs, unlimited usage after initial setup
- **Customization** - Fine-tune models for your specific use cases
- **Speed** - No network latency, instant responses
- **Security** - Sensitive code never leaves your environment

## 🔧 Installation & Setup

### System Requirements

**Minimum Requirements:**
- **Memory**: 8GB RAM (16GB+ recommended)
- **Storage**: 10GB+ free space per model
- **CPU**: Modern multi-core processor
- **GPU** (optional): NVIDIA GPU with CUDA support for faster inference

**Recommended Setup:**
- **Memory**: 32GB+ RAM for larger models
- **GPU**: RTX 3080+ or equivalent for optimal performance
- **Storage**: SSD for faster model loading

### Install Ollama

**macOS:**
```bash
# Download from website or use Homebrew
brew install ollama

# Or download installer from https://ollama.ai/download
```

**Linux:**
```bash
# Install script
curl -fsSL https://ollama.ai/install.sh | sh

# Or manual download
wget https://github.com/jmorganca/ollama/releases/latest/download/ollama-linux-amd64
chmod +x ollama-linux-amd64
sudo mv ollama-linux-amd64 /usr/local/bin/ollama
```

**Windows:**
```bash
# Download installer from https://ollama.ai/download
# Or use Windows Subsystem for Linux (WSL)
```

### Start Ollama Service

```bash
# Start Ollama service
ollama serve

# Or run in background (macOS/Linux)
ollama serve &

# On macOS, Ollama can run as a service
brew services start ollama
```

## 🔍 Quick Verification

Test your Ollama installation:

```bash
# Check if Ollama is running
curl http://localhost:11434/

# Download and run your first model
ollama pull llama2

# Test the model
ollama run llama2
>>> Hello, how can you help with coding?

# Test via API
curl -X POST http://localhost:11434/api/generate -d '{
  "model": "llama2",
  "prompt": "Write a Python function to calculate factorial",
  "stream": false
}'

# List installed models  
ollama list

# Check system info
ollama show llama2
```

## 🚀 Development Workflow Integration

### Command Line Usage

**Basic Chat Interface:**
```bash
# Interactive chat with a model
ollama run codellama

>>> Can you review this Python function for potential bugs?
>>> def process_user_data(data):
>>>     return data.strip().lower()

[Model analyzes code and provides feedback]

>>> How can I make this more robust?

[Model suggests error handling and validation]
```

**Code Generation:**
```bash
ollama run codellama "Create a FastAPI endpoint for user authentication with JWT tokens"

# Or pipe code to model
cat auth.py | ollama run codellama "Explain this authentication code and suggest improvements"
```

**Code Review:**
```bash
# Review git diff
git diff | ollama run codellama "Review these changes for potential issues"

# Review specific files
ollama run codellama "Review this Python file for security vulnerabilities" < app.py
```

### API Integration

**Python Integration:**
```python
import requests
import json

class OllamaClient:
    def __init__(self, base_url="http://localhost:11434"):
        self.base_url = base_url
    
    def generate(self, model, prompt, stream=False):
        url = f"{self.base_url}/api/generate"
        data = {
            "model": model,
            "prompt": prompt,
            "stream": stream
        }
        
        response = requests.post(url, json=data)
        return response.json()
    
    def chat(self, model, messages):
        url = f"{self.base_url}/api/chat"
        data = {
            "model": model,
            "messages": messages
        }
        
        response = requests.post(url, json=data)
        return response.json()

# Usage examples
client = OllamaClient()

# Code review
review = client.generate(
    model="codellama",
    prompt="Review this function for bugs:\n\ndef calculate_total(items):\n    total = 0\n    for item in items:\n        total += item.price * item.quantity\n    return total"
)

print(review['response'])

# Interactive chat
messages = [
    {"role": "user", "content": "Help me debug a memory leak in Python"},
    {"role": "assistant", "content": "I'd be happy to help debug your memory leak. Can you share the code?"},
    {"role": "user", "content": "Here's the problematic function: [code here]"}
]

response = client.chat("codellama", messages)
```

**JavaScript Integration:**
```javascript
class OllamaClient {
    constructor(baseUrl = 'http://localhost:11434') {
        this.baseUrl = baseUrl;
    }
    
    async generate(model, prompt) {
        const response = await fetch(`${this.baseUrl}/api/generate`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({
                model,
                prompt,
                stream: false
            })
        });
        
        return response.json();
    }
    
    async codeReview(code, language = 'javascript') {
        const prompt = `Review this ${language} code for potential issues, bugs, and improvements:\n\n${code}`;
        return this.generate('codellama', prompt);
    }
}

// Usage
const ollama = new OllamaClient();

// Code review
const code = `
function processUserData(userData) {
    const cleaned = userData.map(user => {
        return {
            id: user.id,
            name: user.name.trim(),
            email: user.email.toLowerCase()
        };
    });
    return cleaned;
}
`;

ollama.codeReview(code, 'javascript').then(result => {
    console.log('Review:', result.response);
});
```

## 🧠 Recommended Models

### Code-Focused Models

**CodeLlama (Meta):**
```bash
# Download different sizes
ollama pull codellama:7b      # 4.8GB - Fast, good for simple tasks
ollama pull codellama:13b     # 7.6GB - Better quality
ollama pull codellama:34b     # 20GB - Best quality, requires more RAM

# Specialized versions
ollama pull codellama:7b-instruct    # Better at following instructions
ollama pull codellama:7b-python      # Python-specialized
```

**Use Cases:**
- Code generation and completion
- Bug detection and fixing
- Code explanation and documentation
- Refactoring suggestions

**DeepSeek Coder:**
```bash
ollama pull deepseek-coder:6.7b
ollama pull deepseek-coder:33b
```

**Use Cases:**
- Advanced code understanding
- Complex algorithm implementation
- Code optimization
- Architecture suggestions

### General Purpose Models

**Llama 2:**
```bash
ollama pull llama2:7b         # General purpose, fast
ollama pull llama2:13b        # Better reasoning
ollama pull llama2:70b        # Best quality (requires 64GB+ RAM)
```

**Use Cases:**
- Documentation writing
- Technical explanations
- Project planning
- Problem-solving discussions

**Mistral:**
```bash
ollama pull mistral:7b        # Fast and efficient
ollama pull mixtral:8x7b      # Mixture of experts, very capable
```

**Use Cases:**
- Code review and analysis
- Technical writing
- Debugging assistance
- API design discussions

### Specialized Models

**Qwen Coder:**
```bash
ollama pull qwen:7b-coder     # Alibaba's coding model
```

**StarCoder:**
```bash
ollama pull starcoder:7b      # Trained on permissive code
```

## 📋 Common Development Workflows

### 1. Code Review Workflow

```bash
# Create alias for quick code review
echo 'alias code-review="ollama run codellama"' >> ~/.bashrc
source ~/.bashrc

# Review current changes
git diff HEAD~1 | code-review "Review these changes for potential issues"

# Review specific file
code-review "Review this Python file for security vulnerabilities and performance issues" < auth.py

# Interactive review session
code-review
>>> I'm working on a user authentication system. Can you help me review the security aspects?
>>> [Paste your code here]
```

### 2. Documentation Generation

```bash
# Generate README
ollama run llama2 "Generate a comprehensive README.md for this Python project" < setup.py

# Generate API documentation
ollama run codellama "Generate API documentation for these Python functions" < api.py

# Generate code comments
ollama run codellama "Add comprehensive docstrings to this Python file" < utils.py
```

### 3. Debugging Workflow

```bash
# Analyze error logs
tail -f /var/log/app.log | ollama run codellama "Analyze these error logs and suggest fixes"

# Debug specific error
ollama run codellama "This Python code is giving me a KeyError. Help me debug it:

def process_user(user_data):
    return {
        'name': user_data['full_name'],
        'email': user_data['email_address']
    }
"

# Performance debugging
ollama run codellama "This function is slow. How can I optimize it?" < slow_function.py
```

### 4. Learning and Exploration

```bash
# Learn new concepts
ollama run llama2 "Explain microservices architecture with pros, cons, and implementation examples"

# Compare technologies
ollama run llama2 "Compare FastAPI vs Flask for building REST APIs. Include performance, features, and use cases"

# Code explanation
ollama run codellama "Explain this complex algorithm step by step" < algorithm.py
```

## ⚙️ Advanced Configuration

### Model Management

```bash
# List all available models
ollama list

# Remove unused models
ollama rm old-model:7b

# Update model to latest version
ollama pull codellama:13b

# Show model information
ollama show codellama:7b

# Copy and customize model
ollama create my-custom-codellama -f Modelfile
```

### Custom Model Configuration

Create a `Modelfile`:
```dockerfile
FROM codellama:7b

# Set custom parameters
PARAMETER temperature 0.1      # More deterministic responses
PARAMETER top_p 0.9
PARAMETER top_k 40

# Set system prompt
SYSTEM """
You are an expert software engineer with deep knowledge of Python, JavaScript, and system architecture. 
Always provide secure, efficient, and well-documented code.
Focus on best practices and explain your reasoning.
"""
```

Build custom model:
```bash
ollama create my-python-expert -f Modelfile
ollama run my-python-expert "Help me design a scalable web API"
```

### Performance Optimization

**GPU Configuration:**
```bash
# Check GPU usage
nvidia-smi

# Run with specific GPU
CUDA_VISIBLE_DEVICES=0 ollama serve

# Monitor GPU usage while running model
watch nvidia-smi
```

**Memory Management:**
```bash
# Set memory limits (Linux)
systemd-run --scope -p MemoryLimit=16G ollama serve

# Monitor memory usage
htop
# Look for ollama processes and memory consumption
```

**Model Loading Optimization:**
```bash
# Keep model in memory for faster subsequent calls
ollama run codellama --keep-alive 60m

# Preload models at startup
ollama pull codellama:7b
ollama pull llama2:7b
ollama run codellama:7b "warmup" >/dev/null &
ollama run llama2:7b "warmup" >/dev/null &
```

## 🔐 Security & Privacy

### Why Local Models Matter

**Data Privacy:**
- Code never leaves your machine
- No API logs or data retention by third parties
- Complete control over sensitive business logic
- Compliance with strict data governance requirements

**Security Benefits:**
- No network dependency for AI assistance
- No risk of prompt injection attacks on remote services
- Local audit trail of all AI interactions
- Air-gapped environments supported

### Best Practices

**Secure Setup:**
```bash
# Run Ollama on localhost only (default)
# Avoid exposing to external networks

# If needed for team access, use SSH tunneling
ssh -L 11434:localhost:11434 user@server

# Monitor access
tail -f ~/.ollama/logs/server.log
```

**Model Verification:**
```bash
# Verify model integrity
ollama show codellama:7b
# Check SHA256 hash matches expected value

# Only download models from trusted sources
ollama pull officialmodel:tag
# Avoid third-party or unverified models
```

## 🐛 Troubleshooting

### Common Issues

**Model Won't Load:**
```bash
# Check available memory
free -h

# Check disk space
df -h

# Try smaller model
ollama pull codellama:7b  # Instead of 34b
```

**Slow Performance:**
```bash
# Check if using GPU
nvidia-smi  # Should show ollama process using GPU

# Monitor CPU usage
htop

# Try reducing model size or parameters
ollama run codellama:7b --num-ctx 2048  # Reduce context window
```

**Connection Issues:**
```bash
# Check if service is running
curl http://localhost:11434/api/tags

# Restart service
pkill ollama
ollama serve

# Check logs
tail -f ~/.ollama/logs/server.log
```

### Performance Tuning

**Optimize for Your Hardware:**
```bash
# For low-memory systems
ollama run codellama:7b --num-gpu 0  # Force CPU-only

# For high-memory systems
ollama run codellama:34b --num-gpu 1  # Use GPU acceleration

# Adjust context window based on use case
ollama run codellama --num-ctx 4096   # Larger context for big files
ollama run codellama --num-ctx 1024   # Smaller for quick queries
```

## 🔗 Integration Guides

- **[Privacy Workflows](guides/privacy.md)** - Secure development practices
- **[Model Recommendations](models.md)** - Choosing the right model for your needs
- **[API Integration](guides/api-integration.md)** - Building applications with Ollama
- **[Performance Optimization](guides/performance.md)** - Getting the most from your hardware

## 📚 Related Documentation

- **[Claude Code](../claude-code/index.md)** - For when you need cloud AI capabilities
- **[Claude Desktop](../claude-desktop/index.md)** - Visual and conversational AI workflows
- **[Python Development](../../python/index.md)** - Python-specific AI-assisted workflows

---

**Next:** Check [Model Recommendations](models.md) to choose the best models for your development work, then explore [Privacy Workflows](guides/privacy.md) for secure development practices.
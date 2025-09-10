# Ollama Model Recommendations

This guide helps you choose the right models for your development needs based on hardware, use case, and performance requirements.

## Model Selection Matrix

| Use Case | Recommended Models | Memory | Best For |
|----------|-------------------|---------|----------|
| **Quick Code Snippets** | `codellama:7b` | 8GB | Fast responses, simple tasks |
| **Code Reviews** | `codellama:13b`, `deepseek-coder:6.7b` | 16GB | Thorough analysis, bug detection |
| **Complex Development** | `codellama:34b`, `deepseek-coder:33b` | 32GB+ | Architecture, complex algorithms |
| **Documentation** | `llama2:13b`, `mistral:7b` | 16GB | Technical writing, explanations |
| **Learning & Research** | `mixtral:8x7b`, `llama2:70b` | 64GB+ | Deep explanations, comparisons |

## Code-Focused Models

### CodeLlama Family (Meta)

**CodeLlama 7B** - Best for quick tasks
```bash
ollama pull codellama:7b
# Size: 4.8GB | RAM: 8GB | Speed: ⭐⭐⭐⭐⭐
```

**Strengths:**
- Fast inference times
- Good at code completion
- Minimal resource usage
- Great for interactive development

**Best for:**
- Simple function generation
- Quick bug fixes
- Code explanations
- Learning programming concepts

**Example Use:**
```bash
ollama run codellama:7b "Write a Python function to validate email addresses"
```

---

**CodeLlama 13B** - Balanced performance
```bash
ollama pull codellama:13b
# Size: 7.6GB | RAM: 16GB | Speed: ⭐⭐⭐⭐
```

**Strengths:**
- Better code understanding
- More accurate suggestions
- Good debugging capabilities
- Handles medium complexity

**Best for:**
- Code reviews
- Refactoring suggestions
- API design
- Testing strategies

**Example Use:**
```bash
ollama run codellama:13b "Review this FastAPI endpoint for security issues"
```

---

**CodeLlama 34B** - Professional grade
```bash
ollama pull codellama:34b
# Size: 20GB | RAM: 32GB+ | Speed: ⭐⭐⭐
```

**Strengths:**
- Advanced code analysis
- Complex problem solving
- Architectural guidance
- Multi-language expertise

**Best for:**
- System design discussions
- Performance optimization
- Complex debugging
- Large codebase analysis

**Example Use:**
```bash
ollama run codellama:34b "Design a scalable microservices architecture for e-commerce"
```

### Specialized Versions

**CodeLlama Instruct** - Better at following instructions
```bash
ollama pull codellama:7b-instruct
ollama pull codellama:13b-instruct
```

**Use when:**
- Need precise output format
- Following complex instructions
- Step-by-step code generation
- Structured responses required

**CodeLlama Python** - Python-specialized
```bash
ollama pull codellama:7b-python
```

**Use when:**
- Python-heavy projects
- Django/Flask development
- Data science workflows
- Python best practices needed

## Advanced Code Models

### DeepSeek Coder

**DeepSeek Coder 6.7B**
```bash
ollama pull deepseek-coder:6.7b
# Size: 4.2GB | RAM: 8GB | Speed: ⭐⭐⭐⭐⭐
```

**Strengths:**
- Excellent code understanding
- Strong debugging capabilities
- Good at code optimization
- Efficient resource usage

**Best for:**
- Algorithm implementation
- Performance optimization
- Code quality improvement
- Complex logic debugging

---

**DeepSeek Coder 33B**
```bash
ollama pull deepseek-coder:33b
# Size: 19GB | RAM: 32GB+ | Speed: ⭐⭐⭐
```

**Strengths:**
- Superior code analysis
- Advanced problem solving
- Multi-language expertise
- Enterprise-level capabilities

**Best for:**
- Large-scale refactoring
- Architecture reviews
- Security analysis
- Complex system design

### StarCoder

**StarCoder 7B**
```bash
ollama pull starcoder:7b
# Size: 4.3GB | RAM: 8GB | Speed: ⭐⭐⭐⭐
```

**Strengths:**
- Trained on permissive licenses
- Good code completion
- Multi-language support
- Open-source friendly

**Best for:**
- Open source projects
- Code completion
- Learning new languages
- Collaborative development

## General Purpose Models

### Llama 2 Family

**Llama 2 7B** - Fast general AI
```bash
ollama pull llama2:7b
# Size: 4.1GB | RAM: 8GB | Speed: ⭐⭐⭐⭐⭐
```

**Best for:**
- Documentation writing
- Technical explanations
- Project planning
- Quick Q&A

---

**Llama 2 13B** - Better reasoning
```bash
ollama pull llama2:13b
# Size: 7.6GB | RAM: 16GB | Speed: ⭐⭐⭐⭐
```

**Best for:**
- Complex explanations
- Technical writing
- Problem analysis
- Requirement gathering

---

**Llama 2 70B** - Professional grade
```bash
ollama pull llama2:70b
# Size: 40GB | RAM: 64GB+ | Speed: ⭐⭐
```

**Best for:**
- Enterprise documentation
- Complex technical discussions
- Strategic planning
- Research and analysis

### Mistral Family

**Mistral 7B** - Efficient and capable
```bash
ollama pull mistral:7b
# Size: 4.4GB | RAM: 8GB | Speed: ⭐⭐⭐⭐⭐
```

**Strengths:**
- Excellent efficiency
- Strong reasoning
- Good code understanding
- Multilingual support

**Best for:**
- Technical documentation
- Code reviews
- API design discussions
- Debugging assistance

---

**Mixtral 8x7B** - Mixture of experts
```bash
ollama pull mixtral:8x7b
# Size: 26GB | RAM: 32GB+ | Speed: ⭐⭐⭐
```

**Strengths:**
- Advanced reasoning
- Multi-task capability
- Excellent quality
- Versatile applications

**Best for:**
- Complex problem solving
- Architecture discussions
- Research and analysis
- Strategic planning

## Hardware-Based Recommendations

### Low-End Systems (8GB RAM)
```bash
# Primary choices
ollama pull codellama:7b        # Code tasks
ollama pull mistral:7b          # General tasks
ollama pull llama2:7b           # Documentation

# Alternative options
ollama pull deepseek-coder:6.7b # Advanced code analysis
ollama pull starcoder:7b        # Code completion
```

### Mid-Range Systems (16GB RAM)
```bash
# Recommended setup
ollama pull codellama:13b       # Primary code model
ollama pull llama2:13b          # General purpose
ollama pull mistral:7b          # Fast tasks

# Specialized additions
ollama pull codellama:7b-python # Python work
ollama pull codellama:7b-instruct # Structured tasks
```

### High-End Systems (32GB+ RAM)
```bash
# Professional setup
ollama pull codellama:34b       # Advanced code work
ollama pull mixtral:8x7b        # Complex reasoning
ollama pull deepseek-coder:33b  # Code analysis

# Keep smaller models for speed
ollama pull codellama:7b        # Quick tasks
ollama pull mistral:7b          # Fast responses
```

### Enterprise Systems (64GB+ RAM)
```bash
# Full capability setup
ollama pull llama2:70b          # Best general model
ollama pull codellama:34b       # Best code model
ollama pull mixtral:8x7b        # Advanced reasoning
ollama pull deepseek-coder:33b  # Code analysis

# Specialized models
ollama pull codellama:13b-instruct
ollama pull codellama:7b-python
```

## Model Performance Comparison

### Inference Speed (Tokens/Second)

| Model | 7B Models | 13B Models | 34B+ Models |
|-------|-----------|------------|-------------|
| **CPU (16-core)** | 20-30 tok/s | 10-15 tok/s | 3-5 tok/s |
| **RTX 3080** | 60-80 tok/s | 30-40 tok/s | 10-15 tok/s |
| **RTX 4090** | 100-120 tok/s | 50-60 tok/s | 20-25 tok/s |

### Quality vs Speed Trade-offs

```bash
# For interactive development (speed priority)
ollama run codellama:7b

# For thorough code reviews (quality priority)
ollama run codellama:34b

# For balanced workflows (quality + reasonable speed)
ollama run codellama:13b
```

## Use Case-Specific Recommendations

### Web Development
```bash
# Frontend (JavaScript/TypeScript)
ollama pull codellama:13b       # React, Vue, Angular
ollama pull mistral:7b          # Documentation

# Backend (Node.js, Python, Go)
ollama pull codellama:13b       # API development
ollama pull deepseek-coder:6.7b # Performance optimization
```

### Data Science & ML
```bash
# Python-heavy workflows
ollama pull codellama:7b-python # Pandas, NumPy, Scikit-learn
ollama pull llama2:13b          # Explanations and documentation
ollama pull codellama:13b       # Complex algorithms
```

### DevOps & Infrastructure
```bash
# Infrastructure as Code
ollama pull codellama:13b       # Terraform, Ansible
ollama pull mistral:7b          # Documentation
ollama pull llama2:13b          # Architecture discussions
```

### Mobile Development
```bash
# iOS/Android
ollama pull codellama:13b       # Swift, Kotlin, Dart
ollama pull mistral:7b          # Quick questions
```

### Game Development
```bash
# Unity, Unreal, Godot
ollama pull codellama:13b       # C#, C++, GDScript
ollama pull llama2:13b          # Game design discussions
```

## Model Update Strategy

### Regular Updates
```bash
# Check for model updates monthly
ollama list
ollama pull codellama:7b    # Update main models
ollama pull mistral:7b

# Clean up old versions
ollama rm old-model-version
```

### Version Management
```bash
# Pin specific versions for consistency
ollama pull codellama:7b-20231201

# Create custom model variants
ollama create my-coding-assistant -f Modelfile
```

## Performance Monitoring

### Track Model Performance
```bash
# Monitor resource usage
htop                        # CPU and memory
nvidia-smi                  # GPU usage
ollama show model-name      # Model info
```

### Benchmark Different Models
```python
import time
import requests

def benchmark_model(model, prompt):
    start_time = time.time()
    response = requests.post('http://localhost:11434/api/generate', 
                           json={'model': model, 'prompt': prompt})
    end_time = time.time()
    
    return {
        'model': model,
        'response_time': end_time - start_time,
        'response_length': len(response.json().get('response', ''))
    }

# Test different models
models = ['codellama:7b', 'codellama:13b', 'mistral:7b']
prompt = "Write a Python function to sort a list"

for model in models:
    result = benchmark_model(model, prompt)
    print(f"{result['model']}: {result['response_time']:.2f}s")
```

## Troubleshooting Model Issues

### Model Won't Load
```bash
# Check available resources
free -h                     # Memory
df -h                       # Disk space
nvidia-smi                  # GPU memory

# Try smaller model
ollama pull codellama:7b    # Instead of 34b
```

### Poor Performance
```bash
# Check GPU utilization
nvidia-smi

# Adjust parameters
ollama run model --num-gpu 1     # Force GPU
ollama run model --num-gpu 0     # Force CPU
ollama run model --num-ctx 2048  # Reduce context
```

### Model Comparison
```bash
# A/B test different models
echo "def fibonacci(n):" | ollama run codellama:7b
echo "def fibonacci(n):" | ollama run deepseek-coder:6.7b
echo "def fibonacci(n):" | ollama run codellama:13b
```

## Next Steps

- **[Ollama Installation Guide](index.md)** - Set up Ollama on your system
- **[Privacy Workflows](guides/privacy.md)** - Secure development practices
- **[API Integration](guides/api-integration.md)** - Build applications with Ollama
- **[Performance Optimization](guides/performance.md)** - Maximize your hardware
# Performance Optimization for Ollama

Maximize the speed and efficiency of your local AI models with hardware optimization, configuration tuning, and workflow improvements.

## Hardware Optimization

### CPU Performance
```bash
# Check CPU capabilities
lscpu | grep -E "(Model name|Socket|Core|Thread)"

# Verify CPU features for AI workloads
lscpu | grep -E "(avx|avx2|avx512)"
# AVX2/AVX512 provide significant speedups

# Monitor CPU usage during inference
htop
# Look for high CPU utilization across all cores
```

### Memory Optimization
```bash
# Check available memory
free -h

# Verify system limits
ulimit -v  # Virtual memory limit
sysctl vm.max_map_count  # Memory mapping limit

# Monitor memory usage per model
ps aux | grep ollama
# Note RSS (resident memory) usage

# Optimize memory allocation
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### GPU Acceleration Setup

**NVIDIA GPU (CUDA)**
```bash
# Install NVIDIA drivers
sudo apt update
sudo apt install nvidia-driver-535  # Latest stable

# Install CUDA toolkit
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.0-1_all.deb
sudo dpkg -i cuda-keyring_1.0-1_all.deb
sudo apt update
sudo apt install cuda

# Verify CUDA installation
nvidia-smi
nvcc --version

# Test GPU acceleration
ollama run codellama:7b --verbose
# Should show GPU utilization in output
```

**AMD GPU (ROCm)**
```bash
# Install ROCm (Linux)
wget https://repo.radeon.com/amdgpu-install/5.6/ubuntu/jammy/amdgpu-install_5.6.50600-1_all.deb
sudo apt install ./amdgpu-install_5.6.50600-1_all.deb
sudo amdgpu-install --usecase=rocm

# Verify ROCm
rocm-smi
```

**Apple Silicon (Metal)**
```bash
# macOS with Apple Silicon has built-in optimization
# Verify Metal support
system_profiler SPDisplaysDataType | grep -A2 "Metal"

# Monitor GPU usage
sudo powermetrics --samplers gpu_power -n 1
```

## Model Performance Tuning

### Memory Management
```bash
# Keep models loaded in memory
ollama run codellama:7b --keep-alive 60m
# Model stays loaded for 60 minutes after last use

# Preload frequently used models
ollama pull codellama:7b
ollama pull mistral:7b
# Start background processes to keep them warm
(sleep 2; ollama run codellama:7b "warmup" >/dev/null) &
(sleep 5; ollama run mistral:7b "warmup" >/dev/null) &
```

### Context Window Optimization
```bash
# Reduce context for faster responses
ollama run codellama:7b --num-ctx 1024 "Quick code snippet"
# Default: 2048, Reduced: 1024 = ~2x faster

# Increase context for complex tasks
ollama run codellama:7b --num-ctx 4096 "Analyze large file"
# More context = better understanding, slower speed

# Find optimal context size for your use case
for ctx in 512 1024 2048 4096; do
    echo "Context: $ctx"
    time ollama run codellama:7b --num-ctx $ctx "def fibonacci(n):"
done
```

### Batch Processing
```bash
# Process multiple requests efficiently
# Instead of sequential calls:
ollama run codellama:7b "Review file1.py" < file1.py
ollama run codellama:7b "Review file2.py" < file2.py

# Use batch processing:
cat file1.py file2.py | ollama run codellama:7b "Review these Python files"
```

## System-Level Optimizations

### Process Priority
```bash
# Increase Ollama priority
sudo renice -5 $(pgrep ollama)

# Set CPU affinity for better performance
sudo taskset -cp 0-7 $(pgrep ollama)  # Use first 8 cores

# Persistent priority settings
echo 'ollama soft priority -5' | sudo tee -a /etc/security/limits.conf
```

### Storage Optimization
```bash
# Move models to fastest storage
# Check current model location
ollama show codellama:7b | grep "model path"

# Move to SSD if needed
sudo mkdir -p /fast-ssd/ollama
sudo chown $USER:$USER /fast-ssd/ollama
export OLLAMA_MODELS="/fast-ssd/ollama"

# Verify faster loading
time ollama run codellama:7b "test" >/dev/null
```

### Network Optimization (API calls)
```bash
# Optimize local network stack
sudo sysctl -w net.core.rmem_max=16777216
sudo sysctl -w net.core.wmem_max=16777216
sudo sysctl -w net.ipv4.tcp_rmem="4096 12582912 16777216"
sudo sysctl -w net.ipv4.tcp_wmem="4096 12582912 16777216"

# Use Unix sockets instead of TCP (when available)
export OLLAMA_HOST=unix:///tmp/ollama.sock
```

## Model-Specific Optimizations

### Choose Right Model Size
```python
# Performance benchmarking script
import time
import requests
import json

def benchmark_model(model, prompt, iterations=5):
    times = []
    for i in range(iterations):
        start = time.time()
        
        response = requests.post('http://localhost:11434/api/generate', 
                               json={
                                   'model': model,
                                   'prompt': prompt,
                                   'stream': False
                               })
        
        end = time.time()
        times.append(end - start)
    
    avg_time = sum(times) / len(times)
    tokens = len(response.json().get('response', '').split())
    tokens_per_second = tokens / avg_time if avg_time > 0 else 0
    
    return {
        'model': model,
        'avg_time': avg_time,
        'tokens_per_second': tokens_per_second,
        'response_quality': len(response.json().get('response', ''))
    }

# Test different model sizes
models = ['codellama:7b', 'codellama:13b', 'codellama:34b']
prompt = "Write a Python function to sort a list of dictionaries by a key"

print("Model Performance Comparison:")
print("-" * 60)

for model in models:
    try:
        result = benchmark_model(model, prompt)
        print(f"{result['model']:15} | "
              f"{result['avg_time']:6.2f}s | "
              f"{result['tokens_per_second']:6.1f} tok/s | "
              f"{result['response_quality']:4d} chars")
    except Exception as e:
        print(f"{model:15} | Error: {e}")
```

### Quantization Settings
```bash
# Use different quantization levels for speed vs quality
# Q4_0: Fastest, lower quality
# Q4_1: Balanced
# Q5_0: Better quality, slower
# Q5_1: Best quality for the size

# Most models default to optimal quantization
# Check model info
ollama show codellama:7b | grep -A10 "parameters"
```

## Performance Monitoring

### Real-Time Monitoring
```bash
# Monitor system resources during AI workload
# Terminal 1: Run model
ollama run codellama:13b

# Terminal 2: Monitor performance
watch -n 1 'echo "=== CPU ===" && \
            mpstat 1 1 | grep -A1 Average && \
            echo "=== Memory ===" && \
            free -h && \
            echo "=== GPU ===" && \
            nvidia-smi --query-gpu=utilization.gpu,memory.used,memory.total --format=csv,noheader'
```

### Automated Performance Testing
```python
#!/usr/bin/env python3
# performance_monitor.py

import psutil
import requests
import time
import json
import subprocess

class OllamaPerformanceMonitor:
    def __init__(self):
        self.base_url = "http://localhost:11434"
        
    def get_system_metrics(self):
        """Get current system performance metrics"""
        cpu_percent = psutil.cpu_percent(interval=1)
        memory = psutil.virtual_memory()
        
        # GPU metrics (if NVIDIA)
        try:
            gpu_output = subprocess.check_output([
                'nvidia-smi', '--query-gpu=utilization.gpu,memory.used',
                '--format=csv,noheader,nounits'
            ], text=True)
            gpu_util, gpu_mem = map(int, gpu_output.strip().split(', '))
        except:
            gpu_util, gpu_mem = 0, 0
            
        return {
            'cpu_percent': cpu_percent,
            'memory_percent': memory.percent,
            'memory_used_gb': memory.used / (1024**3),
            'gpu_utilization': gpu_util,
            'gpu_memory_mb': gpu_mem
        }
    
    def benchmark_inference(self, model, prompt):
        """Benchmark a single inference"""
        start_metrics = self.get_system_metrics()
        start_time = time.time()
        
        response = requests.post(f"{self.base_url}/api/generate", 
                               json={
                                   'model': model,
                                   'prompt': prompt,
                                   'stream': False
                               })
        
        end_time = time.time()
        end_metrics = self.get_system_metrics()
        
        inference_time = end_time - start_time
        response_text = response.json().get('response', '')
        tokens = len(response_text.split())
        
        return {
            'model': model,
            'inference_time': inference_time,
            'tokens_generated': tokens,
            'tokens_per_second': tokens / inference_time if inference_time > 0 else 0,
            'peak_cpu': max(start_metrics['cpu_percent'], end_metrics['cpu_percent']),
            'peak_gpu': max(start_metrics['gpu_utilization'], end_metrics['gpu_utilization']),
            'memory_used': end_metrics['memory_used_gb'],
            'response_length': len(response_text)
        }
    
    def run_comprehensive_benchmark(self):
        """Run benchmarks across different scenarios"""
        test_cases = [
            ("Simple function", "def hello_world():"),
            ("Complex algorithm", "Write a Python implementation of quicksort with comments"),
            ("Code review", "Review this function for bugs:\ndef divide(a, b):\n    return a / b"),
            ("Documentation", "Generate docstring for a REST API endpoint"),
        ]
        
        models = ['codellama:7b', 'codellama:13b']
        results = []
        
        for model in models:
            print(f"\nTesting {model}...")
            for test_name, prompt in test_cases:
                try:
                    result = self.benchmark_inference(model, prompt)
                    result['test_case'] = test_name
                    results.append(result)
                    print(f"  {test_name}: {result['tokens_per_second']:.1f} tok/s")
                except Exception as e:
                    print(f"  {test_name}: Error - {e}")
        
        return results
    
    def generate_report(self, results):
        """Generate performance report"""
        report = "# Ollama Performance Report\n\n"
        
        for model in set(r['model'] for r in results):
            model_results = [r for r in results if r['model'] == model]
            avg_speed = sum(r['tokens_per_second'] for r in model_results) / len(model_results)
            avg_cpu = sum(r['peak_cpu'] for r in model_results) / len(model_results)
            
            report += f"## {model}\n"
            report += f"- Average Speed: {avg_speed:.1f} tokens/second\n"
            report += f"- Average CPU Usage: {avg_cpu:.1f}%\n"
            report += f"- Memory Usage: {model_results[0]['memory_used']:.1f}GB\n\n"
            
            for result in model_results:
                report += f"### {result['test_case']}\n"
                report += f"- Speed: {result['tokens_per_second']:.1f} tok/s\n"
                report += f"- Response Length: {result['response_length']} chars\n"
                report += f"- Inference Time: {result['inference_time']:.2f}s\n\n"
        
        return report

# Usage
if __name__ == "__main__":
    monitor = OllamaPerformanceMonitor()
    results = monitor.run_comprehensive_benchmark()
    report = monitor.generate_report(results)
    
    with open('performance_report.md', 'w') as f:
        f.write(report)
    
    print("\nPerformance report saved to performance_report.md")
```

## Optimization Strategies by Use Case

### Interactive Development
```bash
# Optimize for low latency
export OLLAMA_NUM_PARALLEL=1      # Single request processing
export OLLAMA_MAX_LOADED_MODELS=2 # Keep 2 models loaded
export OLLAMA_FLASH_ATTENTION=1   # Enable flash attention

# Use fast models for interactive work
alias code-help="ollama run codellama:7b"
alias quick-review="ollama run mistral:7b"
```

### Batch Processing
```bash
# Optimize for throughput
export OLLAMA_NUM_PARALLEL=4      # Process multiple requests
export OLLAMA_MAX_LOADED_MODELS=1 # Focus resources on one model

# Use larger models for better quality
alias batch-process="ollama run codellama:13b"
```

### Background Processing
```bash
# Optimize for efficiency
export OLLAMA_NUM_PARALLEL=2
export OLLAMA_FLASH_ATTENTION=1

# Lower priority to not interfere with other work
nice -n 10 ollama run codellama:7b
```

## Workflow Optimizations

### Smart Model Selection
```python
# model_selector.py
import re

def select_optimal_model(prompt, context_length=0):
    """Select the best model based on task complexity"""
    
    # Simple tasks - use fastest model
    simple_patterns = [
        r"def \w+\(",           # Function definition
        r"fix.*bug",            # Simple bug fix
        r"explain.*briefly",    # Brief explanation
    ]
    
    # Complex tasks - use larger model
    complex_patterns = [
        r"design.*architecture", # System design
        r"review.*security",     # Security review
        r"optimize.*performance", # Performance optimization
    ]
    
    prompt_lower = prompt.lower()
    
    if any(re.search(pattern, prompt_lower) for pattern in complex_patterns):
        return "codellama:13b"
    elif context_length > 2000:
        return "codellama:13b"  # Large context needs bigger model
    elif any(re.search(pattern, prompt_lower) for pattern in simple_patterns):
        return "codellama:7b"
    else:
        return "codellama:7b"   # Default to fast model

# Usage
prompt = "def calculate_fibonacci(n):"
model = select_optimal_model(prompt)
print(f"Using {model} for: {prompt}")
```

### Caching and Reuse
```python
# smart_cache.py
import hashlib
import json
import os

class OllamaCache:
    def __init__(self, cache_dir="~/.ollama_cache"):
        self.cache_dir = os.path.expanduser(cache_dir)
        os.makedirs(self.cache_dir, exist_ok=True)
    
    def get_cache_key(self, model, prompt):
        """Generate cache key for prompt"""
        content = f"{model}:{prompt}"
        return hashlib.sha256(content.encode()).hexdigest()
    
    def get(self, model, prompt):
        """Get cached response if available"""
        cache_key = self.get_cache_key(model, prompt)
        cache_file = os.path.join(self.cache_dir, f"{cache_key}.json")
        
        if os.path.exists(cache_file):
            with open(cache_file, 'r') as f:
                return json.load(f)['response']
        return None
    
    def set(self, model, prompt, response):
        """Cache response"""
        cache_key = self.get_cache_key(model, prompt)
        cache_file = os.path.join(self.cache_dir, f"{cache_key}.json")
        
        with open(cache_file, 'w') as f:
            json.dump({
                'model': model,
                'prompt': prompt,
                'response': response
            }, f)

# Usage
cache = OllamaCache()

def cached_inference(model, prompt):
    # Check cache first
    cached_response = cache.get(model, prompt)
    if cached_response:
        print("Using cached response")
        return cached_response
    
    # Generate new response
    import requests
    response = requests.post('http://localhost:11434/api/generate', 
                           json={'model': model, 'prompt': prompt, 'stream': False})
    result = response.json()['response']
    
    # Cache for future use
    cache.set(model, prompt, result)
    return result
```

## Hardware Recommendations

### Budget Setup (8GB RAM)
```bash
# Optimal configuration for limited resources
# Use 7B models only
ollama pull codellama:7b
ollama pull mistral:7b

# Optimize for single model usage
export OLLAMA_MAX_LOADED_MODELS=1
export OLLAMA_NUM_PARALLEL=1

# Monitor memory closely
watch -n 1 free -h
```

### Performance Setup (16GB+ RAM)
```bash
# Balanced configuration
ollama pull codellama:13b      # Primary coding model
ollama pull mistral:7b         # Fast general model
ollama pull codellama:7b       # Fallback/quick tasks

export OLLAMA_MAX_LOADED_MODELS=2
export OLLAMA_NUM_PARALLEL=2
```

### Workstation Setup (32GB+ RAM + GPU)
```bash
# High-performance configuration
ollama pull codellama:34b      # Best quality coding
ollama pull codellama:13b      # Balanced option
ollama pull mixtral:8x7b       # Complex reasoning
ollama pull mistral:7b         # Fast responses

export OLLAMA_MAX_LOADED_MODELS=3
export OLLAMA_NUM_PARALLEL=4
export OLLAMA_FLASH_ATTENTION=1
```

## Troubleshooting Performance Issues

### Slow Inference
```bash
# Check model size vs available memory
ollama show codellama:34b | grep size
free -h

# Monitor during inference
htop          # CPU usage
iotop         # Disk I/O
nvidia-smi    # GPU usage

# Common fixes:
# 1. Use smaller model
ollama run codellama:7b instead of codellama:34b

# 2. Reduce context window
ollama run model --num-ctx 1024

# 3. Close unnecessary applications
# 4. Upgrade RAM if consistently swapping
```

### Memory Issues
```bash
# Monitor memory pressure
cat /proc/meminfo | grep -E "(MemTotal|MemFree|MemAvailable|SwapTotal|SwapFree)"

# Clear system cache
sudo sysctl vm.drop_caches=3

# Restart Ollama service
sudo systemctl restart ollama
```

### GPU Not Used
```bash
# Verify GPU detection
nvidia-smi

# Check CUDA installation
nvcc --version

# Reinstall Ollama with GPU support
curl -fsSL https://ollama.ai/install.sh | sh

# Force GPU usage
export CUDA_VISIBLE_DEVICES=0
ollama run codellama:7b --verbose
```

## Related Resources

- **[Model Selection](../models.md)** - Choose the right model for your hardware
- **[Privacy Setup](privacy.md)** - Secure configuration practices
- **[API Integration](api-integration.md)** - Optimize API calls
- **[Ollama Installation](../index.md)** - Complete setup guide
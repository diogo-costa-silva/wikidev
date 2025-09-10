# API Integration with Ollama

Build applications and workflows that leverage Ollama's local AI capabilities through its REST API, SDKs, and integrations.

## Ollama API Fundamentals

### Basic API Structure
```bash
# Check if Ollama is running
curl http://localhost:11434/api/tags

# Basic generation endpoint
curl -X POST http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "codellama:7b",
    "prompt": "Write a Python function to calculate factorial",
    "stream": false
  }'
```

### API Endpoints Overview
```bash
# List available models
GET /api/tags

# Generate text completion
POST /api/generate

# Chat completion (conversation)
POST /api/chat

# Create/import model
POST /api/create

# Pull model from registry
POST /api/pull

# Push model to registry
POST /api/push

# Delete model
DELETE /api/delete

# Copy model
POST /api/copy

# Show model information
POST /api/show
```

## Python Integration

### Basic Client Implementation
```python
import requests
import json
from typing import Dict, List, Optional, Iterator

class OllamaClient:
    def __init__(self, base_url: str = "http://localhost:11434"):
        self.base_url = base_url.rstrip('/')
        self.session = requests.Session()
    
    def generate(self, 
                 model: str, 
                 prompt: str, 
                 stream: bool = False,
                 **kwargs) -> Dict:
        """Generate text completion"""
        url = f"{self.base_url}/api/generate"
        data = {
            "model": model,
            "prompt": prompt,
            "stream": stream,
            **kwargs
        }
        
        response = self.session.post(url, json=data)
        response.raise_for_status()
        
        if stream:
            return self._handle_stream(response)
        else:
            return response.json()
    
    def chat(self, 
             model: str, 
             messages: List[Dict[str, str]], 
             stream: bool = False) -> Dict:
        """Chat completion with conversation context"""
        url = f"{self.base_url}/api/chat"
        data = {
            "model": model,
            "messages": messages,
            "stream": stream
        }
        
        response = self.session.post(url, json=data)
        response.raise_for_status()
        
        if stream:
            return self._handle_stream(response)
        else:
            return response.json()
    
    def _handle_stream(self, response) -> Iterator[Dict]:
        """Handle streaming responses"""
        for line in response.iter_lines():
            if line:
                yield json.loads(line.decode('utf-8'))
    
    def list_models(self) -> List[str]:
        """List available models"""
        url = f"{self.base_url}/api/tags"
        response = self.session.get(url)
        response.raise_for_status()
        
        data = response.json()
        return [model['name'] for model in data.get('models', [])]
    
    def model_info(self, model: str) -> Dict:
        """Get model information"""
        url = f"{self.base_url}/api/show"
        data = {"name": model}
        
        response = self.session.post(url, json=data)
        response.raise_for_status()
        return response.json()

# Usage examples
client = OllamaClient()

# Simple generation
response = client.generate(
    model="codellama:7b",
    prompt="def fibonacci(n):",
    temperature=0.1
)
print(response['response'])

# Chat conversation
messages = [
    {"role": "user", "content": "How do I optimize this Python function?"},
    {"role": "assistant", "content": "I'd be happy to help! Please share the function you'd like to optimize."},
    {"role": "user", "content": "def slow_function():\n    time.sleep(1)\n    return 'done'"}
]

chat_response = client.chat("codellama:7b", messages)
print(chat_response['message']['content'])

# Streaming response
for chunk in client.generate("codellama:7b", "Explain async/await", stream=True):
    if 'response' in chunk:
        print(chunk['response'], end='', flush=True)
```

### Advanced Python Integration
```python
import asyncio
import aiohttp
import json
from typing import AsyncIterator

class AsyncOllamaClient:
    def __init__(self, base_url: str = "http://localhost:11434"):
        self.base_url = base_url.rstrip('/')
    
    async def generate(self, 
                      model: str, 
                      prompt: str, 
                      stream: bool = False) -> Dict:
        """Async generation with connection pooling"""
        url = f"{self.base_url}/api/generate"
        data = {
            "model": model,
            "prompt": prompt,
            "stream": stream
        }
        
        async with aiohttp.ClientSession() as session:
            async with session.post(url, json=data) as response:
                if stream:
                    return self._handle_stream(response)
                else:
                    return await response.json()
    
    async def _handle_stream(self, response) -> AsyncIterator[Dict]:
        """Handle async streaming responses"""
        async for line in response.content:
            if line:
                yield json.loads(line.decode('utf-8'))
    
    async def batch_generate(self, 
                           model: str, 
                           prompts: List[str]) -> List[Dict]:
        """Process multiple prompts concurrently"""
        tasks = [self.generate(model, prompt) for prompt in prompts]
        return await asyncio.gather(*tasks)

# Usage
async def main():
    client = AsyncOllamaClient()
    
    # Batch processing
    prompts = [
        "def quicksort(arr):",
        "def binary_search(arr, target):",
        "def fibonacci(n):"
    ]
    
    results = await client.batch_generate("codellama:7b", prompts)
    for prompt, result in zip(prompts, results):
        print(f"Prompt: {prompt}")
        print(f"Response: {result['response'][:100]}...")
        print("-" * 50)

# Run async client
asyncio.run(main())
```

## JavaScript/Node.js Integration

### Basic Node.js Client
```javascript
const https = require('http');

class OllamaClient {
    constructor(baseUrl = 'http://localhost:11434') {
        this.baseUrl = baseUrl;
    }
    
    async generate(model, prompt, options = {}) {
        const url = `${this.baseUrl}/api/generate`;
        const data = {
            model: model,
            prompt: prompt,
            stream: false,
            ...options
        };
        
        const response = await fetch(url, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data)
        });
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        return await response.json();
    }
    
    async chat(model, messages, options = {}) {
        const url = `${this.baseUrl}/api/chat`;
        const data = {
            model: model,
            messages: messages,
            stream: false,
            ...options
        };
        
        const response = await fetch(url, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data)
        });
        
        return await response.json();
    }
    
    async *generateStream(model, prompt, options = {}) {
        const url = `${this.baseUrl}/api/generate`;
        const data = {
            model: model,
            prompt: prompt,
            stream: true,
            ...options
        };
        
        const response = await fetch(url, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data)
        });
        
        const reader = response.body.getReader();
        const decoder = new TextDecoder();
        
        try {
            while (true) {
                const { done, value } = await reader.read();
                if (done) break;
                
                const chunk = decoder.decode(value);
                const lines = chunk.split('\n').filter(line => line.trim());
                
                for (const line of lines) {
                    try {
                        const json = JSON.parse(line);
                        yield json;
                    } catch (e) {
                        // Skip malformed JSON
                    }
                }
            }
        } finally {
            reader.releaseLock();
        }
    }
}

// Usage examples
const ollama = new OllamaClient();

// Basic generation
async function generateCode() {
    const response = await ollama.generate(
        'codellama:7b', 
        'Write a JavaScript function to validate email addresses'
    );
    
    console.log(response.response);
}

// Streaming response
async function streamingExample() {
    console.log('Streaming response:');
    
    for await (const chunk of ollama.generateStream(
        'codellama:7b', 
        'Explain how promises work in JavaScript'
    )) {
        if (chunk.response) {
            process.stdout.write(chunk.response);
        }
    }
}

// Chat conversation
async function chatExample() {
    const messages = [
        { role: 'user', content: 'Help me debug this JavaScript code' },
        { role: 'assistant', content: 'I\'d be happy to help! Please share the code.' },
        { role: 'user', content: 'const x = [1,2,3]; console.log(x[3]);' }
    ];
    
    const response = await ollama.chat('codellama:7b', messages);
    console.log(response.message.content);
}

// Run examples
generateCode();
streamingExample();
chatExample();
```

### Express.js Web Service
```javascript
const express = require('express');
const cors = require('cors');

class OllamaWebService {
    constructor() {
        this.app = express();
        this.ollama = new OllamaClient();
        this.setupMiddleware();
        this.setupRoutes();
    }
    
    setupMiddleware() {
        this.app.use(cors());
        this.app.use(express.json());
        this.app.use(express.static('public'));
    }
    
    setupRoutes() {
        // Code generation endpoint
        this.app.post('/api/generate-code', async (req, res) => {
            try {
                const { language, description, model = 'codellama:7b' } = req.body;
                
                const prompt = `Write a ${language} ${description}`;
                const response = await this.ollama.generate(model, prompt);
                
                res.json({
                    success: true,
                    code: response.response,
                    model: model
                });
            } catch (error) {
                res.status(500).json({
                    success: false,
                    error: error.message
                });
            }
        });
        
        // Code review endpoint
        this.app.post('/api/review-code', async (req, res) => {
            try {
                const { code, language, model = 'codellama:7b' } = req.body;
                
                const prompt = `Review this ${language} code for bugs and improvements:\n\n${code}`;
                const response = await this.ollama.generate(model, prompt);
                
                res.json({
                    success: true,
                    review: response.response,
                    model: model
                });
            } catch (error) {
                res.status(500).json({
                    success: false,
                    error: error.message
                });
            }
        });
        
        // Streaming chat endpoint
        this.app.post('/api/chat-stream', async (req, res) => {
            try {
                const { messages, model = 'codellama:7b' } = req.body;
                
                res.setHeader('Content-Type', 'text/event-stream');
                res.setHeader('Cache-Control', 'no-cache');
                res.setHeader('Connection', 'keep-alive');
                
                for await (const chunk of this.ollama.generateStream(model, messages[messages.length - 1].content)) {
                    if (chunk.response) {
                        res.write(`data: ${JSON.stringify({ content: chunk.response })}\n\n`);
                    }
                    
                    if (chunk.done) {
                        res.write(`data: [DONE]\n\n`);
                        break;
                    }
                }
                
                res.end();
            } catch (error) {
                res.status(500).json({
                    success: false,
                    error: error.message
                });
            }
        });
        
        // Health check
        this.app.get('/health', async (req, res) => {
            try {
                const models = await this.ollama.listModels();
                res.json({
                    status: 'healthy',
                    available_models: models
                });
            } catch (error) {
                res.status(500).json({
                    status: 'unhealthy',
                    error: error.message
                });
            }
        });
    }
    
    start(port = 3000) {
        this.app.listen(port, () => {
            console.log(`Ollama web service running on port ${port}`);
        });
    }
}

// Start the service
const service = new OllamaWebService();
service.start();
```

## Go Integration

### Go Client Implementation
```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "time"
)

type OllamaClient struct {
    BaseURL    string
    HTTPClient *http.Client
}

type GenerateRequest struct {
    Model  string `json:"model"`
    Prompt string `json:"prompt"`
    Stream bool   `json:"stream"`
}

type GenerateResponse struct {
    Model     string    `json:"model"`
    Response  string    `json:"response"`
    Done      bool      `json:"done"`
    CreatedAt time.Time `json:"created_at"`
}

type ChatMessage struct {
    Role    string `json:"role"`
    Content string `json:"content"`
}

type ChatRequest struct {
    Model    string        `json:"model"`
    Messages []ChatMessage `json:"messages"`
    Stream   bool          `json:"stream"`
}

func NewOllamaClient(baseURL string) *OllamaClient {
    return &OllamaClient{
        BaseURL: baseURL,
        HTTPClient: &http.Client{
            Timeout: 300 * time.Second,
        },
    }
}

func (c *OllamaClient) Generate(model, prompt string) (*GenerateResponse, error) {
    req := GenerateRequest{
        Model:  model,
        Prompt: prompt,
        Stream: false,
    }
    
    jsonData, err := json.Marshal(req)
    if err != nil {
        return nil, fmt.Errorf("error marshaling request: %w", err)
    }
    
    resp, err := c.HTTPClient.Post(
        fmt.Sprintf("%s/api/generate", c.BaseURL),
        "application/json",
        bytes.NewBuffer(jsonData),
    )
    if err != nil {
        return nil, fmt.Errorf("error making request: %w", err)
    }
    defer resp.Body.Close()
    
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        return nil, fmt.Errorf("error reading response: %w", err)
    }
    
    var response GenerateResponse
    if err := json.Unmarshal(body, &response); err != nil {
        return nil, fmt.Errorf("error unmarshaling response: %w", err)
    }
    
    return &response, nil
}

func (c *OllamaClient) Chat(model string, messages []ChatMessage) (*GenerateResponse, error) {
    req := ChatRequest{
        Model:    model,
        Messages: messages,
        Stream:   false,
    }
    
    jsonData, err := json.Marshal(req)
    if err != nil {
        return nil, fmt.Errorf("error marshaling request: %w", err)
    }
    
    resp, err := c.HTTPClient.Post(
        fmt.Sprintf("%s/api/chat", c.BaseURL),
        "application/json",
        bytes.NewBuffer(jsonData),
    )
    if err != nil {
        return nil, fmt.Errorf("error making request: %w", err)
    }
    defer resp.Body.Close()
    
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        return nil, fmt.Errorf("error reading response: %w", err)
    }
    
    var response GenerateResponse
    if err := json.Unmarshal(body, &response); err != nil {
        return nil, fmt.Errorf("error unmarshaling response: %w", err)
    }
    
    return &response, nil
}

// Usage example
func main() {
    client := NewOllamaClient("http://localhost:11434")
    
    // Generate code
    response, err := client.Generate(
        "codellama:7b",
        "Write a Go function to calculate fibonacci numbers",
    )
    if err != nil {
        fmt.Printf("Error: %v\n", err)
        return
    }
    
    fmt.Printf("Generated code:\n%s\n", response.Response)
    
    // Chat conversation
    messages := []ChatMessage{
        {Role: "user", Content: "Help me optimize this Go code"},
        {Role: "assistant", Content: "I'd be happy to help! Please share the code."},
        {Role: "user", Content: "func slow() { time.Sleep(time.Second); return }"},
    }
    
    chatResponse, err := client.Chat("codellama:7b", messages)
    if err != nil {
        fmt.Printf("Error: %v\n", err)
        return
    }
    
    fmt.Printf("Chat response:\n%s\n", chatResponse.Response)
}
```

## Development Tools Integration

### VS Code Extension Integration
```typescript
// extension.ts
import * as vscode from 'vscode';
import axios from 'axios';

export function activate(context: vscode.ExtensionContext) {
    // Register code generation command
    let generateCommand = vscode.commands.registerCommand(
        'ollama.generateCode',
        async () => {
            const editor = vscode.window.activeTextEditor;
            if (!editor) {
                vscode.window.showErrorMessage('No active editor found');
                return;
            }
            
            const selection = editor.selection;
            const selectedText = editor.document.getText(selection);
            
            if (!selectedText) {
                vscode.window.showErrorMessage('Please select a comment or description');
                return;
            }
            
            try {
                const response = await axios.post('http://localhost:11434/api/generate', {
                    model: 'codellama:7b',
                    prompt: `Convert this comment to code:\n${selectedText}`,
                    stream: false
                });
                
                const generatedCode = response.data.response;
                
                // Replace selection with generated code
                editor.edit(editBuilder => {
                    editBuilder.replace(selection, generatedCode);
                });
                
            } catch (error) {
                vscode.window.showErrorMessage(`Error: ${error.message}`);
            }
        }
    );
    
    // Register code review command
    let reviewCommand = vscode.commands.registerCommand(
        'ollama.reviewCode',
        async () => {
            const editor = vscode.window.activeTextEditor;
            if (!editor) return;
            
            const document = editor.document;
            const selection = editor.selection;
            const code = document.getText(selection.isEmpty ? undefined : selection);
            
            try {
                const response = await axios.post('http://localhost:11434/api/generate', {
                    model: 'codellama:7b',
                    prompt: `Review this code for potential issues:\n\n${code}`,
                    stream: false
                });
                
                const review = response.data.response;
                
                // Show review in output channel
                const outputChannel = vscode.window.createOutputChannel('Ollama Code Review');
                outputChannel.clear();
                outputChannel.appendLine('Code Review Results:');
                outputChannel.appendLine('=' * 50);
                outputChannel.appendLine(review);
                outputChannel.show();
                
            } catch (error) {
                vscode.window.showErrorMessage(`Error: ${error.message}`);
            }
        }
    );
    
    context.subscriptions.push(generateCommand, reviewCommand);
}
```

### Git Hooks Integration
```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running AI code review..."

# Get changed files
CHANGED_FILES=$(git diff --cached --name-only --diff-filter=AM | grep -E "\.(py|js|ts|go|java|cpp|c|h)$")

if [ -z "$CHANGED_FILES" ]; then
    echo "No code files to review"
    exit 0
fi

# Review each changed file
for file in $CHANGED_FILES; do
    echo "Reviewing $file..."
    
    # Get file contents
    CONTENT=$(git show :$file)
    
    # Send to Ollama for review
    REVIEW=$(curl -s -X POST http://localhost:11434/api/generate \
        -H "Content-Type: application/json" \
        -d "{
            \"model\": \"codellama:7b\",
            \"prompt\": \"Review this code for potential bugs, security issues, and improvements. Be concise:\\n\\n$CONTENT\",
            \"stream\": false
        }" | jq -r '.response')
    
    # Check for critical issues
    if echo "$REVIEW" | grep -qi "critical\|security\|vulnerability\|bug"; then
        echo "⚠️  Critical issues found in $file:"
        echo "$REVIEW"
        echo ""
        
        # Ask user to proceed
        read -p "Continue with commit? (y/N): " -n 1 -r
        echo
        if [[ ! $REPLY =~ ^[Yy]$ ]]; then
            exit 1
        fi
    else
        echo "✅ $file looks good"
    fi
done

echo "Code review complete!"
```

### CI/CD Integration
```yaml
# .github/workflows/ollama-review.yml
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
      with:
        fetch-depth: 0
    
    - name: Setup Ollama
      run: |
        curl -fsSL https://ollama.ai/install.sh | sh
        ollama serve &
        sleep 5
        ollama pull codellama:7b
    
    - name: Get Changed Files
      id: changed-files
      run: |
        CHANGED_FILES=$(git diff --name-only origin/main...HEAD | grep -E "\.(py|js|ts|go|java)$" | head -10)
        echo "files<<EOF" >> $GITHUB_OUTPUT
        echo "$CHANGED_FILES" >> $GITHUB_OUTPUT
        echo "EOF" >> $GITHUB_OUTPUT
    
    - name: AI Code Review
      run: |
        REVIEW_OUTPUT=""
        
        while IFS= read -r file; do
          if [[ -f "$file" ]]; then
            echo "Reviewing $file..."
            
            CONTENT=$(cat "$file")
            REVIEW=$(curl -s -X POST http://localhost:11434/api/generate \
              -H "Content-Type: application/json" \
              -d "{
                \"model\": \"codellama:7b\",
                \"prompt\": \"Review this code file for issues. Focus on bugs, security, and performance. Be concise:\\n\\n$CONTENT\",
                \"stream\": false
              }" | jq -r '.response')
            
            REVIEW_OUTPUT="$REVIEW_OUTPUT\n\n## Review for $file\n\n$REVIEW"
          fi
        done <<< "${{ steps.changed-files.outputs.files }}"
        
        # Save review to file
        echo -e "$REVIEW_OUTPUT" > review.md
    
    - name: Comment PR
      uses: actions/github-script@v6
      with:
        script: |
          const fs = require('fs');
          const review = fs.readFileSync('review.md', 'utf8');
          
          github.rest.issues.createComment({
            issue_number: context.issue.number,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: `## 🤖 AI Code Review\n\n${review}`
          });
```

## Error Handling and Resilience

### Robust Error Handling
```python
import time
import logging
from typing import Optional, Dict, Any

class ResilientOllamaClient:
    def __init__(self, base_url="http://localhost:11434", max_retries=3):
        self.base_url = base_url
        self.max_retries = max_retries
        self.session = requests.Session()
        
        # Configure logging
        logging.basicConfig(level=logging.INFO)
        self.logger = logging.getLogger(__name__)
    
    def generate_with_retry(self, 
                           model: str, 
                           prompt: str, 
                           **kwargs) -> Optional[Dict[str, Any]]:
        """Generate with automatic retry and fallback"""
        
        # Try primary model
        for attempt in range(self.max_retries):
            try:
                response = self.generate(model, prompt, **kwargs)
                return response
            except requests.exceptions.ConnectionError:
                self.logger.warning(f"Connection failed, attempt {attempt + 1}/{self.max_retries}")
                time.sleep(2 ** attempt)  # Exponential backoff
            except requests.exceptions.Timeout:
                self.logger.warning(f"Request timeout, attempt {attempt + 1}/{self.max_retries}")
                time.sleep(1)
            except Exception as e:
                self.logger.error(f"Unexpected error: {e}")
                if attempt == self.max_retries - 1:
                    raise
        
        # Try fallback model if primary fails
        if ":" in model:
            base_model = model.split(":")[0]
            fallback_models = [f"{base_model}:7b", "codellama:7b", "mistral:7b"]
            
            for fallback in fallback_models:
                if fallback != model:
                    try:
                        self.logger.info(f"Trying fallback model: {fallback}")
                        return self.generate(fallback, prompt, **kwargs)
                    except Exception as e:
                        self.logger.warning(f"Fallback {fallback} failed: {e}")
                        continue
        
        return None
    
    def health_check(self) -> bool:
        """Check if Ollama service is healthy"""
        try:
            response = self.session.get(f"{self.base_url}/api/tags", timeout=5)
            return response.status_code == 200
        except Exception:
            return False
    
    def wait_for_service(self, timeout: int = 60) -> bool:
        """Wait for Ollama service to become available"""
        start_time = time.time()
        
        while time.time() - start_time < timeout:
            if self.health_check():
                return True
            
            self.logger.info("Waiting for Ollama service...")
            time.sleep(2)
        
        return False

# Usage with error handling
client = ResilientOllamaClient()

# Wait for service to be ready
if not client.wait_for_service():
    print("Ollama service not available")
    exit(1)

# Generate with automatic retry and fallback
response = client.generate_with_retry(
    model="codellama:13b",
    prompt="Write a function to handle errors gracefully"
)

if response:
    print(response['response'])
else:
    print("Failed to generate response after all retries")
```

### Rate Limiting and Queue Management
```python
import asyncio
import time
from asyncio import Queue, Semaphore
from dataclasses import dataclass
from typing import List, Callable, Any

@dataclass
class Task:
    id: str
    model: str
    prompt: str
    callback: Callable[[Any], None]
    priority: int = 0

class OllamaTaskQueue:
    def __init__(self, max_concurrent=2, rate_limit_per_minute=60):
        self.max_concurrent = max_concurrent
        self.rate_limit = rate_limit_per_minute
        self.semaphore = Semaphore(max_concurrent)
        self.queue = Queue()
        self.request_times = []
        
        self.client = AsyncOllamaClient()
    
    async def add_task(self, task: Task):
        """Add task to queue"""
        await self.queue.put(task)
    
    async def process_tasks(self):
        """Process tasks with rate limiting and concurrency control"""
        while True:
            try:
                # Wait for task
                task = await self.queue.get()
                
                # Check rate limit
                await self._check_rate_limit()
                
                # Process with concurrency control
                await self.semaphore.acquire()
                asyncio.create_task(self._process_single_task(task))
                
            except Exception as e:
                print(f"Error processing task: {e}")
    
    async def _check_rate_limit(self):
        """Enforce rate limiting"""
        now = time.time()
        self.request_times = [t for t in self.request_times if now - t < 60]
        
        if len(self.request_times) >= self.rate_limit:
            sleep_time = 60 - (now - self.request_times[0])
            if sleep_time > 0:
                await asyncio.sleep(sleep_time)
    
    async def _process_single_task(self, task: Task):
        """Process individual task"""
        try:
            self.request_times.append(time.time())
            
            response = await self.client.generate(task.model, task.prompt)
            task.callback(response)
            
        except Exception as e:
            task.callback({"error": str(e)})
        finally:
            self.semaphore.release()
            self.queue.task_done()

# Usage
async def main():
    queue = OllamaTaskQueue(max_concurrent=2, rate_limit_per_minute=30)
    
    # Start queue processor
    asyncio.create_task(queue.process_tasks())
    
    # Add tasks
    def handle_response(response):
        if "error" in response:
            print(f"Error: {response['error']}")
        else:
            print(f"Response: {response['response'][:100]}...")
    
    for i in range(10):
        task = Task(
            id=f"task_{i}",
            model="codellama:7b",
            prompt=f"Write function number {i}",
            callback=handle_response
        )
        await queue.add_task(task)
    
    # Wait for all tasks to complete
    await queue.queue.join()

asyncio.run(main())
```

## Related Resources

- **[Ollama Installation](../index.md)** - Set up Ollama for development
- **[Model Selection](../models.md)** - Choose the right model for your API
- **[Performance Optimization](performance.md)** - Optimize API response times
- **[Privacy Configuration](privacy.md)** - Secure API setup
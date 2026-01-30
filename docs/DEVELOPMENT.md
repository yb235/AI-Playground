# Development Guide

This guide covers everything you need to know to contribute to AI Playground, from setting up your development environment to understanding the codebase and submitting pull requests.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Development Setup](#development-setup)
3. [Project Structure](#project-structure)
4. [Development Workflow](#development-workflow)
5. [Coding Standards](#coding-standards)
6. [Testing](#testing)
7. [Debugging](#debugging)
8. [Building and Packaging](#building-and-packaging)
9. [Contributing Guidelines](#contributing-guidelines)

---

## Prerequisites

### Required Software

- **Node.js** 18.x or later ([Download](https://nodejs.org/))
- **npm** (comes with Node.js)
- **Git** ([Download](https://git-scm.com/))
- **Python** 3.11 (will be managed via Conda)
- **Miniforge** (Conda environment manager) ([Download](https://github.com/conda-forge/miniforge))
- **Windows 10/11** (required for Intel GPU support)
- **Intel Arc GPU** (8GB+ VRAM) or Intel Core Ultra processor

### Recommended Tools

- **Visual Studio Code** with extensions:
  - Vue - Official (Vue.volar)
  - TypeScript Vue Plugin (Volar)
  - ESLint
  - Prettier
  - Python (ms-python.python)
- **Git GUI Client** (optional): GitHub Desktop, GitKraken, or SourceTree

---

## Development Setup

### 1. Clone the Repository

```bash
# Clone from the dev branch
git clone -b dev https://github.com/intel/AI-Playground.git
cd AI-Playground
```

### 2. Install Node.js Dependencies

```bash
cd WebUI
npm install
```

This installs all frontend dependencies including:
- Electron
- Vue.js 3
- Vite
- TypeScript
- Pinia
- TailwindCSS
- And more...

### 3. Prepare Python Environment

#### Step 1: Create Conda Environment

```bash
# Create a new Conda environment with Python 3.11 and libuv
conda create -n cp311_libuv python=3.11 libuv -y

# Activate the environment
conda activate cp311_libuv
```

#### Step 2: Locate Conda Environment Path

```bash
# Windows
conda env list | findstr cp311_libuv

# Output example:
# cp311_libuv    C:\Users\username\miniforge3\envs\cp311_libuv
```

Copy the path from the output.

#### Step 3: Fetch Build Resources

```bash
# Still in WebUI directory
# Replace <path> with your Conda environment path from step 2
npm run fetch-build-resources -- --conda_env_dir=C:\Users\username\miniforge3\envs\cp311_libuv
```

This script:
- Downloads Python build tools
- Fetches ComfyUI dependencies
- Prepares the Python environment

#### Step 4: Prepare Build Environment

```bash
npm run prepare-build
```

This creates the Python environment at `build-envs\online\prototype-python-env`.

### 4. Launch Development Mode

```bash
npm run dev
```

This command:
- Starts Vite dev server for hot-reload
- Launches Electron in development mode
- Enables DevTools
- Watches for file changes

The application will open automatically. You'll see the Setup screen on first launch.

---

## Project Structure

```
AI-Playground/
├── WebUI/                          # Frontend Electron application
│   ├── electron/                   # Electron main process
│   │   ├── main.ts                # Main entry point
│   │   ├── preload.ts             # Context bridge
│   │   ├── subprocesses/          # Backend service management
│   │   │   ├── apiServiceRegistry.ts
│   │   │   ├── aiBackendService.ts
│   │   │   ├── comfyUIBackendService.ts
│   │   │   └── ...
│   │   └── utils/                 # Utilities
│   ├── src/                       # Vue.js application
│   │   ├── main.ts                # Vue entry point
│   │   ├── views/                 # Page components
│   │   │   ├── Chat.vue
│   │   │   ├── WorkflowResult.vue
│   │   │   └── Settings.vue
│   │   ├── components/            # Reusable components
│   │   ├── assets/
│   │   │   └── js/
│   │   │       ├── store/         # Pinia stores
│   │   │       └── tools/         # Utilities
│   │   └── lib/                   # Shared libraries
│   ├── external/                  # External resources
│   │   ├── presets/              # ComfyUI presets
│   │   └── workflows/            # Workflow definitions
│   ├── package.json              # Node.js dependencies
│   ├── vite.config.mts           # Vite configuration
│   ├── tsconfig.json             # TypeScript config
│   └── eslint.config.ts          # ESLint rules
├── service/                       # Primary Python backend
│   ├── web_api.py                # Flask API
│   ├── model_downloader.py       # Model downloads
│   ├── comfyui_downloader.py     # ComfyUI installer
│   ├── config.py                 # Configuration
│   └── requirements.txt          # Python deps
├── docs/                         # Documentation
│   ├── GETTING-STARTED.md
│   ├── ARCHITECTURE.md
│   ├── API-REFERENCE.md
│   └── ...
├── readme.md                     # Project README
├── CONTRIBUTING.md               # Contribution guidelines
└── AGENTS.md                     # AI agent instructions
```

### Key Directories

- **`WebUI/electron/`**: Electron main process, IPC handlers, service management
- **`WebUI/src/`**: Vue.js frontend, UI components, Pinia stores
- **`WebUI/external/`**: ComfyUI workflows, presets, and resources
- **`service/`**: Primary Python backend (Flask API)
- **`docs/`**: Comprehensive documentation

---

## Development Workflow

### Typical Development Cycle

1. **Make Code Changes**: Edit files in `WebUI/src/` or `WebUI/electron/`
2. **Hot Reload**: Changes are automatically reflected in the running app
3. **Test**: Use the UI to test your changes
4. **Debug**: Use DevTools (Ctrl+Shift+I) for frontend, VS Code debugger for backend
5. **Commit**: Follow the commit guidelines below
6. **Push**: Create a pull request

### Working with Stores (Pinia)

Stores are the heart of state management in AI Playground. Follow these patterns:

#### Creating a New Store

```typescript
// WebUI/src/assets/js/store/myFeature.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useMyFeature = defineStore('myFeature', () => {
  // State
  const count = ref(0)
  const items = ref<string[]>([])
  
  // Getters (computed)
  const doubleCount = computed(() => count.value * 2)
  
  // Actions
  function increment() {
    count.value++
  }
  
  async function fetchItems() {
    const result = await window.electronAPI.getItems()
    items.value = result
  }
  
  return {
    // State
    count,
    items,
    // Getters
    doubleCount,
    // Actions
    increment,
    fetchItems,
  }
}, {
  persist: true // Enable persistence
})
```

#### Using a Store in Components

```vue
<script setup lang="ts">
import { useMyFeature } from '@/assets/js/store/myFeature'

const myFeature = useMyFeature()

// Access state
console.log(myFeature.count)

// Call actions
myFeature.increment()
</script>

<template>
  <div>
    <p>Count: {{ myFeature.count }}</p>
    <p>Double: {{ myFeature.doubleCount }}</p>
    <button @click="myFeature.increment">Increment</button>
  </div>
</template>
```

### Adding New IPC Commands

Follow the three-file pattern for type-safe IPC:

#### 1. Main Process Handler (`WebUI/electron/main.ts`)

```typescript
ipcMain.handle('myNewCommand', async (_event, arg1: string, arg2: number) => {
  try {
    const result = await performOperation(arg1, arg2)
    return { success: true, data: result }
  } catch (error) {
    return { success: false, error: error.message }
  }
})
```

#### 2. Preload Exposure (`WebUI/electron/preload.ts`)

```typescript
contextBridge.exposeInMainWorld('electronAPI', {
  // ... existing methods
  myNewCommand: (arg1: string, arg2: number) => 
    ipcRenderer.invoke('myNewCommand', arg1, arg2),
})
```

#### 3. Type Definition (`WebUI/src/env.d.ts`)

```typescript
type electronAPI = {
  // ... existing methods
  myNewCommand(arg1: string, arg2: number): Promise<{ 
    success: boolean
    data?: any
    error?: string 
  }>
}
```

### Creating New Vue Components

Follow the Composition API pattern with TypeScript:

```vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import type { HTMLAttributes } from 'vue'
import { cn } from '@/lib/utils'

// Props
interface Props {
  modelValue?: string
  placeholder?: string
  class?: HTMLAttributes['class']
}

const props = withDefaults(defineProps<Props>(), {
  placeholder: 'Enter text...'
})

// Emits
const emit = defineEmits<{
  'update:modelValue': [value: string]
}>()

// Local state
const focused = ref(false)

// Computed
const classes = computed(() => cn(
  'border rounded px-3 py-2',
  focused.value && 'ring-2 ring-blue-500',
  props.class
))

// Methods
function handleInput(event: Event) {
  const target = event.target as HTMLInputElement
  emit('update:modelValue', target.value)
}

onMounted(() => {
  console.log('Component mounted')
})
</script>

<template>
  <input
    :value="modelValue"
    :placeholder="placeholder"
    :class="classes"
    @input="handleInput"
    @focus="focused = true"
    @blur="focused = false"
  />
</template>
```

### Working with Backend Services

#### Adding a New Backend Endpoint (`service/web_api.py`)

```python
from apiflask import APIFlask
from web_request_bodies import MyRequestBody

@app.post("/api/myEndpoint")
@app.input(MyRequestBody, location='json', arg_name='request_data')
def my_endpoint(request_data: MyRequestBody):
    try:
        # Your logic here
        result = process_data(request_data.data)
        
        return jsonify({
            "code": 0,
            "message": "success",
            "data": result
        })
    except Exception as e:
        return jsonify({
            "code": -1,
            "message": str(e)
        }), 500
```

#### Define Request Schema (`service/web_request_bodies.py`)

```python
from apiflask import Schema
from marshmallow import fields

class MyRequestBody(Schema):
    data = fields.Dict(required=True)
```

---

## Coding Standards

### TypeScript/JavaScript

#### Style Guidelines

```typescript
// Use const/let, never var
const API_URL = 'http://localhost:5000'
let count = 0

// Prefer arrow functions
const add = (a: number, b: number): number => a + b

// Use async/await over promises
async function fetchData() {
  const response = await fetch(API_URL)
  const data = await response.json()
  return data
}

// Destructure when possible
const { name, age } = user

// Use optional chaining
const city = user?.address?.city

// Use nullish coalescing
const displayName = username ?? 'Anonymous'
```

#### TypeScript Best Practices

```typescript
// Define interfaces for complex types
interface User {
  id: string
  name: string
  email: string
  age?: number
}

// Use type for unions
type Status = 'pending' | 'active' | 'inactive'

// Avoid 'any' - use 'unknown' if type is truly unknown
function processData(data: unknown) {
  if (typeof data === 'string') {
    return data.toUpperCase()
  }
}

// Use generics for reusable functions
function first<T>(arr: T[]): T | undefined {
  return arr[0]
}
```

#### Vue Composition API Patterns

```typescript
// Use setup script
<script setup lang="ts">
import { ref, computed, watch } from 'vue'

// Reactive state
const count = ref(0)
const message = reactive({ text: 'Hello' })

// Computed properties
const doubled = computed(() => count.value * 2)

// Watchers
watch(count, (newValue, oldValue) => {
  console.log(`Count changed from ${oldValue} to ${newValue}`)
})
</script>
```

### Python

#### Style Guidelines

Follow PEP 8 and use type hints:

```python
from typing import List, Dict, Optional

# Use type hints
def process_items(items: List[str], max_count: Optional[int] = None) -> Dict[str, int]:
    """Process a list of items and return counts.
    
    Args:
        items: List of item names
        max_count: Maximum items to process (optional)
        
    Returns:
        Dictionary mapping item names to counts
    """
    result = {}
    for item in items[:max_count]:
        result[item] = result.get(item, 0) + 1
    return result

# Use dataclasses for structured data
from dataclasses import dataclass

@dataclass
class ModelInfo:
    name: str
    path: str
    size: int
    loaded: bool = False
```

### File Naming Conventions

- **TypeScript/Vue**: PascalCase for components (`MyComponent.vue`), camelCase for utilities (`myUtil.ts`)
- **Python**: snake_case for all files (`web_api.py`, `model_downloader.py`)
- **CSS**: kebab-case for classes (`.my-component`)

### Git Commit Messages

Follow the Conventional Commits format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, no logic change)
- `refactor`: Code refactoring
- `perf`: Performance improvements
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples**:
```
feat(chat): add support for vision models

Implement Qwen3-VL integration for image analysis in chat.
Adds new message type for images and updates UI to display them.

Closes #123
```

```
fix(backend): resolve model loading timeout issue

Increase timeout for large model downloads and add retry logic.

Fixes #456
```

---

## Testing

### Running Tests

```bash
# Frontend tests (Vitest)
cd WebUI
npm run test

# Watch mode
npm run test:watch

# Coverage
npm run test:coverage
```

### Writing Unit Tests

#### Vue Component Tests

```typescript
// MyComponent.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import MyComponent from './MyComponent.vue'

describe('MyComponent', () => {
  it('renders correctly', () => {
    const wrapper = mount(MyComponent, {
      props: { message: 'Hello' }
    })
    
    expect(wrapper.text()).toContain('Hello')
  })
  
  it('emits event on button click', async () => {
    const wrapper = mount(MyComponent)
    
    await wrapper.find('button').trigger('click')
    
    expect(wrapper.emitted()).toHaveProperty('clicked')
  })
})
```

#### Store Tests

```typescript
// myFeature.spec.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useMyFeature } from './myFeature'

describe('myFeature store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })
  
  it('increments count', () => {
    const store = useMyFeature()
    
    expect(store.count).toBe(0)
    
    store.increment()
    
    expect(store.count).toBe(1)
  })
})
```

### Integration Testing

Test IPC communication:

```typescript
// ipc.spec.ts
import { describe, it, expect, vi } from 'vitest'

describe('IPC Communication', () => {
  it('should handle getServices call', async () => {
    const mockServices = [
      { name: 'ai-backend', status: 'Running' }
    ]
    
    // Mock electronAPI
    window.electronAPI = {
      getServices: vi.fn().mockResolvedValue(mockServices)
    }
    
    const services = await window.electronAPI.getServices()
    
    expect(services).toEqual(mockServices)
  })
})
```

---

## Debugging

### Frontend Debugging

#### DevTools

Press `Ctrl+Shift+I` to open Chrome DevTools:

- **Elements**: Inspect DOM
- **Console**: View logs and errors
- **Sources**: Set breakpoints in TypeScript
- **Network**: Monitor API calls
- **Vue DevTools**: Inspect Vue components and Pinia stores

#### Console Logging

```typescript
console.log('Debug info:', data)
console.error('Error occurred:', error)
console.warn('Warning:', message)
console.table(arrayOfObjects)
```

#### Debugging Reactive State

Use Vue DevTools to:
- Inspect component data
- View Pinia store state
- Track mutations and actions
- Time travel through state changes

### Backend Debugging

#### Python Logging

```python
import logging

logger = logging.getLogger(__name__)

logger.debug('Debug message')
logger.info('Info message')
logger.warning('Warning message')
logger.error('Error message', exc_info=True)
```

#### VS Code Debugging

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: Flask",
      "type": "python",
      "request": "launch",
      "module": "flask",
      "env": {
        "FLASK_APP": "service/web_api.py",
        "FLASK_ENV": "development"
      },
      "args": ["run", "--port=5000"],
      "jinja": true
    }
  ]
}
```

Set breakpoints in Python files and press F5 to start debugging.

### Electron Main Process Debugging

#### Logging

```typescript
import { app } from 'electron'

console.log('Main process log:', data)

// Write to file
import fs from 'fs'
const logPath = path.join(app.getPath('userData'), 'main.log')
fs.appendFileSync(logPath, `${new Date().toISOString()}: ${message}\n`)
```

#### VS Code Debugging

Add to `.vscode/launch.json`:

```json
{
  "name": "Electron Main",
  "type": "node",
  "request": "launch",
  "cwd": "${workspaceFolder}/WebUI",
  "runtimeExecutable": "${workspaceFolder}/WebUI/node_modules/.bin/electron",
  "windows": {
    "runtimeExecutable": "${workspaceFolder}/WebUI/node_modules/.bin/electron.cmd"
  },
  "args": ["."],
  "outputCapture": "std"
}
```

---

## Building and Packaging

### Development Build

```bash
cd WebUI
npm run dev
```

### Production Build

```bash
# Build the installer
npm run build
```

This creates:
- **Windows**: `release/AI.Playground-<version>.exe`

The build process:
1. Compiles TypeScript
2. Bundles Vue.js app with Vite
3. Packages Python environments
4. Creates Electron installer with electron-builder

### Build Configuration

Edit `WebUI/package.json` for build settings:

```json
{
  "build": {
    "appId": "com.intel.ai-playground",
    "productName": "AI Playground",
    "files": [
      "dist/**/*",
      "electron/**/*",
      "external/**/*"
    ],
    "directories": {
      "output": "release"
    }
  }
}
```

---

## Contributing Guidelines

### Before You Start

1. **Check existing issues**: Make sure your idea hasn't been proposed
2. **Open an issue**: Discuss your proposed changes
3. **Fork the repository**: Create your own copy
4. **Create a branch**: Use a descriptive name (`feat/add-vision-support`)

### Making Changes

1. **Write clean code**: Follow coding standards
2. **Add tests**: Cover your changes with tests
3. **Update docs**: Document new features or API changes
4. **Test thoroughly**: Ensure nothing breaks

### Submitting a Pull Request

1. **Commit your changes**: Use conventional commit messages
2. **Push to your fork**
3. **Create a pull request**: Provide a clear description
4. **Respond to feedback**: Be open to suggestions
5. **Update your PR**: Address review comments

### PR Description Template

```markdown
## Description
Brief description of what this PR does.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
How was this tested?

## Screenshots (if applicable)
Add screenshots for UI changes.

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] Tests added/updated
- [ ] No new warnings generated
```

### Code Review Process

1. **Automated checks**: CI runs linting, tests, and builds
2. **Maintainer review**: Core team reviews code quality
3. **Revisions**: Make requested changes
4. **Approval**: PR is approved and merged

---

## Useful Commands

### Development

```bash
# Start dev server
npm run dev

# Lint code
npm run lint

# Format code
npm run format

# Type check
vue-tsc --noEmit

# Clean build artifacts
npm run clean
```

### Testing

```bash
# Run all tests
npm run test

# Watch mode
npm run test:watch

# Coverage report
npm run test:coverage
```

### Building

```bash
# Build installer
npm run build

# Build without packaging
npm run build:dir
```

### Python Environment

```bash
# Activate Conda environment
conda activate cp311_libuv

# Install Python dependencies
pip install -r service/requirements.txt

# Run backend directly
python service/web_api.py
```

---

## Common Issues and Solutions

### Port Already in Use

**Error**: `Port 5000 is already in use`

**Solution**: Kill the process using the port or change the port in settings.

### Module Not Found

**Error**: `Cannot find module 'xyz'`

**Solution**: 
```bash
cd WebUI
npm install
```

### Python Import Errors

**Error**: `ModuleNotFoundError: No module named 'xyz'`

**Solution**:
```bash
conda activate cp311_libuv
pip install xyz
```

### Hot Reload Not Working

**Solution**: Restart the dev server and clear browser cache.

---

## Additional Resources

- [Vue.js Documentation](https://vuejs.org/)
- [Electron Documentation](https://www.electronjs.org/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Pinia Documentation](https://pinia.vuejs.org/)
- [Vite Documentation](https://vitejs.dev/)
- [Flask Documentation](https://flask.palletsprojects.com/)

---

## Getting Help

- **Discord**: Join the Intel Insiders Discord
- **GitHub Issues**: Report bugs or ask questions
- **Documentation**: Check the `/docs` folder
- **Code Comments**: Read inline documentation

Happy coding! 🚀

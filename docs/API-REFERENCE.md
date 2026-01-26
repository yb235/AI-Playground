# API Reference

This document provides comprehensive documentation of AI Playground's APIs, including IPC handlers, backend endpoints, and store interfaces.

## Table of Contents

1. [IPC API (Electron)](#ipc-api-electron)
2. [Backend REST APIs](#backend-rest-apis)
3. [Store APIs (Pinia)](#store-apis-pinia)
4. [ComfyUI API](#comfyui-api)
5. [Model Schemas](#model-schemas)

---

## IPC API (Electron)

The Electron IPC API provides communication between the renderer process (Vue.js UI) and the main process. All IPC methods are exposed via `window.electronAPI`.

### Service Management

#### `getServices()`
Get information about all backend services.

**Returns**: `Promise<ApiServiceInformation[]>`

```typescript
interface ApiServiceInformation {
  name: BackendServiceName
  baseUrl: string
  port: number
  currentStatus: BackendStatus
  version?: string
  isRequired: boolean
  isSetUp: boolean
}

type BackendServiceName = 
  | 'ai-backend' 
  | 'comfyui-backend' 
  | 'llamacpp-backend' 
  | 'openvino-backend' 
  | 'ollama-backend'

type BackendStatus = 
  | 'NotInstalled' 
  | 'Installing' 
  | 'Installed' 
  | 'Starting' 
  | 'Running' 
  | 'Stopping' 
  | 'Stopped'
```

**Example**:
```typescript
const services = await window.electronAPI.getServices()
console.log(services[0].currentStatus) // 'Running'
```

#### `sendStartSignal(serviceName: BackendServiceName)`
Start a backend service.

**Parameters**:
- `serviceName`: Name of the service to start

**Returns**: `Promise<BackendStatus>`

**Example**:
```typescript
const status = await window.electronAPI.sendStartSignal('ai-backend')
```

#### `sendStopSignal(serviceName: BackendServiceName)`
Stop a backend service.

**Parameters**:
- `serviceName`: Name of the service to stop

**Returns**: `Promise<BackendStatus>`

#### `setUpService(serviceName: BackendServiceName)`
Install and set up a backend service.

**Parameters**:
- `serviceName`: Name of the service to set up

**Returns**: `Promise<void>`

**Progress Updates**: Listen to `serviceSetUpProgress` event

**Example**:
```typescript
window.electronAPI.onServiceSetUpProgress((progress) => {
  console.log(`${progress.stage}: ${progress.progress}%`)
})

await window.electronAPI.setUpService('comfyui-backend')
```

#### `ensureBackendReadiness(backend, model)`
Ensure a backend is ready with a specific model loaded.

**Parameters**:
- `backend`: Backend service name
- `model`: Model identifier

**Returns**: `Promise<void>`

### Model Management

#### `loadModels()`
Get list of available LLM models.

**Returns**: `Promise<ModelInfo[]>`

```typescript
interface ModelInfo {
  name: string
  path: string
  type: 'llm' | 'embedding' | 'checkpoint' | 'lora' | 'vae'
  size?: number
  loaded?: boolean
}
```

#### `refreshLLMModels()`
Refresh the list of LLM models from disk.

**Returns**: `Promise<void>`

#### `getModelPaths()`
Get configured model directory paths.

**Returns**: `Promise<ModelPaths>`

```typescript
interface ModelPaths {
  llm: string
  embedding: string
  checkpoint: string
  inpaint: string
  lora: string
  vae: string
  [key: string]: string
}
```

**Example**:
```typescript
const paths = await window.electronAPI.getModelPaths()
console.log(paths.llm) // "C:/Users/username/ai-models/llm"
```

#### `downloadModel(modelInfo)`
Download a model from HuggingFace.

**Parameters**:
```typescript
interface ModelDownloadInfo {
  repo: string
  filename: string
  destination: string
  type: string
}
```

**Returns**: `Promise<void>`

**Progress**: Emits `modelDownloadProgress` events

#### `checkModelExists(path: string)`
Check if a model file exists.

**Parameters**:
- `path`: Relative or absolute path to model

**Returns**: `Promise<boolean>`

### Device Management

#### `selectDevice(device: string)`
Select the GPU device to use.

**Parameters**:
- `device`: Device identifier (e.g., "Intel Arc A770")

**Returns**: `Promise<void>`

#### `detectDevices()`
Detect available Intel GPUs.

**Returns**: `Promise<DeviceInfo[]>`

```typescript
interface DeviceInfo {
  name: string
  id: string
  type: 'Arc' | 'iGPU'
  vram: number
}
```

### File Operations

#### `showOpenDialog(options)`
Show file/folder picker dialog.

**Parameters**:
```typescript
interface OpenDialogOptions {
  title?: string
  defaultPath?: string
  filters?: { name: string; extensions: string[] }[]
  properties?: ('openFile' | 'openDirectory' | 'multiSelections')[]
}
```

**Returns**: `Promise<string[] | undefined>`

**Example**:
```typescript
const files = await window.electronAPI.showOpenDialog({
  title: 'Select Images',
  filters: [{ name: 'Images', extensions: ['png', 'jpg', 'jpeg'] }],
  properties: ['openFile', 'multiSelections']
})
```

#### `showSaveDialog(options)`
Show save file dialog.

**Parameters**:
```typescript
interface SaveDialogOptions {
  title?: string
  defaultPath?: string
  filters?: { name: string; extensions: string[] }[]
}
```

**Returns**: `Promise<string | undefined>`

#### `saveImage(imageData, filename)`
Save an image to disk.

**Parameters**:
- `imageData`: Base64 encoded image data
- `filename`: Filename to save

**Returns**: `Promise<string>` (saved file path)

#### `openImageWithSystem(path: string)`
Open an image with the system default application.

**Parameters**:
- `path`: Path to image file

**Returns**: `Promise<void>`

### Window Management

#### `miniWindow()`
Minimize the application window.

**Returns**: `void`

#### `exitApp()`
Exit the application.

**Returns**: `void`

#### `setFullScreen(fullscreen: boolean)`
Toggle fullscreen mode.

**Parameters**:
- `fullscreen`: Whether to enter fullscreen

**Returns**: `void`

### ComfyUI Management

#### `comfyui:downloadCustomNode(nodeRepoData)`
Download and install a ComfyUI custom node.

**Parameters**:
```typescript
interface NodeRepoData {
  repository: string  // Git repository URL
  commit?: string     // Specific commit hash (optional)
}
```

**Returns**: `Promise<void>`

**Example**:
```typescript
await window.electronAPI['comfyui:downloadCustomNode']({
  repository: 'https://github.com/ltdrdata/ComfyUI-Manager',
  commit: 'main'
})
```

#### `comfyui:listInstalledCustomNodes()`
Get list of installed ComfyUI custom nodes.

**Returns**: `Promise<string[]>`

#### `comfyui:isCustomNodeInstalled(nodeRef: string)`
Check if a custom node is installed.

**Parameters**:
- `nodeRef`: Repository reference (e.g., "repo@commit")

**Returns**: `Promise<boolean>`

### Event Listeners

#### `onServiceSetUpProgress(callback)`
Listen for service installation progress.

**Callback**: `(progress: SetupProgress) => void`

```typescript
interface SetupProgress {
  serviceName: BackendServiceName
  stage: string
  progress: number
  message?: string
}
```

#### `onServiceInfoUpdate(callback)`
Listen for service status updates.

**Callback**: `(info: ApiServiceInformation[]) => void`

#### `onWebServiceExit(callback)`
Listen for backend service crashes.

**Callback**: `(serviceName: BackendServiceName, code: number) => void`

#### `onModelDownloadProgress(callback)`
Listen for model download progress.

**Callback**: `(progress: DownloadProgress) => void`

```typescript
interface DownloadProgress {
  filename: string
  progress: number
  speed?: string
  eta?: string
}
```

---

## Backend REST APIs

### AI Backend (Flask API)

**Base URL**: `http://127.0.0.1:<port>` (default: 5000)

#### Health Check

**`GET /healthy`**

Check if the backend is running.

**Response**:
```json
{
  "status": "ok",
  "version": "3.0.0"
}
```

#### Model Download

**`POST /api/downloadModel`**

Download a model from HuggingFace.

**Request Body**:
```json
{
  "data": {
    "repo": "mistralai/Mistral-7B-Instruct-v0.3",
    "filename": "model.safetensors",
    "type": "llm",
    "destination": "llm/mistral-7b"
  }
}
```

**Response**: Server-Sent Events (SSE) stream

**Event Format**:
```
data: {"status": "downloading", "progress": 45, "speed": "12.5 MB/s"}

data: {"status": "complete", "path": "/path/to/model"}
```

#### Check Model Loaded

**`POST /api/checkModelAlreadyLoaded`**

Check if a model is already loaded in cache.

**Request Body**:
```json
{
  "data": {
    "repo": "mistralai/Mistral-7B-Instruct-v0.3",
    "filename": "model.safetensors"
  }
}
```

**Response**:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "loaded": true,
    "path": "/path/to/model"
  }
}
```

#### LLM Chat (OpenAI-Compatible)

**`POST /v1/chat/completions`**

Perform LLM inference with streaming support.

**Request Body**:
```json
{
  "model": "mistral-7b-instruct",
  "messages": [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is AI?"}
  ],
  "stream": true,
  "temperature": 0.7,
  "max_tokens": 500
}
```

**Response** (streaming):
```
data: {"id": "chat-123", "choices": [{"delta": {"content": "AI"}, "index": 0}]}

data: {"id": "chat-123", "choices": [{"delta": {"content": " stands"}, "index": 0}]}

data: [DONE]
```

**Response** (non-streaming):
```json
{
  "id": "chat-123",
  "object": "chat.completion",
  "created": 1234567890,
  "model": "mistral-7b-instruct",
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "AI stands for Artificial Intelligence..."
    },
    "finish_reason": "stop"
  }],
  "usage": {
    "prompt_tokens": 15,
    "completion_tokens": 50,
    "total_tokens": 65
  }
}
```

#### ComfyUI Workflow Validation

**`POST /api/comfyUi/checkWorkflowRequirements`**

Validate ComfyUI workflow dependencies.

**Request Body**:
```json
{
  "workflow": {
    "customNodes": ["repo@commit"],
    "requiredModels": [{
      "type": "checkpoint",
      "name": "dreamshaper-8",
      "path": "DreamShaper_8_pruned.safetensors"
    }]
  }
}
```

**Response**:
```json
{
  "code": 0,
  "data": {
    "valid": false,
    "missingNodes": ["repo@commit"],
    "missingModels": ["DreamShaper_8_pruned.safetensors"]
  }
}
```

#### Install Custom Node

**`POST /api/comfyUi/installCustomNode`**

Install a ComfyUI custom node from GitHub.

**Request Body**:
```json
{
  "repository": "https://github.com/ltdrdata/ComfyUI-Manager",
  "commit": "main"
}
```

**Response**: Server-Sent Events (SSE) stream

**Event Format**:
```
data: {"status": "cloning", "message": "Cloning repository..."}

data: {"status": "installing_deps", "message": "Installing Python dependencies"}

data: {"status": "completed", "message": "Custom node installed successfully"}
```

#### Install Python Package

**`POST /api/comfyUi/installPythonPackage`**

Install a Python package in the ComfyUI environment.

**Request Body**:
```json
{
  "package": "opencv-python",
  "version": "4.8.0"
}
```

**Response**: SSE stream with installation progress

### ComfyUI Backend

**Base URL**: `http://127.0.0.1:<port>` (default: 8188)

#### Submit Workflow

**`POST /prompt`**

Submit a ComfyUI workflow for execution.

**Request Body**:
```json
{
  "prompt": {
    "1": {
      "inputs": {
        "text": "a beautiful landscape",
        "width": 512,
        "height": 512
      },
      "class_type": "CLIPTextEncode"
    },
    // ... more nodes
  },
  "client_id": "unique-client-id"
}
```

**Response**:
```json
{
  "prompt_id": "uuid-prompt-id",
  "number": 1
}
```

#### WebSocket Connection

**`WS /ws`**

Connect to ComfyUI WebSocket for real-time updates.

**Message Types**:

**Progress Update**:
```json
{
  "type": "progress",
  "data": {
    "node": "3",
    "prompt_id": "uuid",
    "max": 20,
    "value": 10
  }
}
```

**Execution Start**:
```json
{
  "type": "executing",
  "data": {
    "node": "3",
    "prompt_id": "uuid"
  }
}
```

**Execution Complete**:
```json
{
  "type": "executed",
  "data": {
    "node": "7",
    "prompt_id": "uuid",
    "output": {
      "images": [{
        "filename": "image_001.png",
        "subfolder": "",
        "type": "output"
      }]
    }
  }
}
```

**Error**:
```json
{
  "type": "execution_error",
  "data": {
    "prompt_id": "uuid",
    "node_id": "5",
    "exception_message": "Model not found",
    "exception_type": "FileNotFoundError"
  }
}
```

### LlamaCPP Backend

**Base URL**: `http://127.0.0.1:<port>` (default: 8080)

#### Chat Completions (OpenAI-Compatible)

**`POST /v1/chat/completions`**

Same interface as AI Backend `/v1/chat/completions`.

### OpenVINO Backend

**Base URL**: `http://127.0.0.1:<port>` (default: 8000)

#### Chat Completions (OpenAI-Compatible)

**`POST /v1/chat/completions`**

Same interface as AI Backend `/v1/chat/completions`.

---

## Store APIs (Pinia)

### backendServices Store

Manages backend service lifecycle and status.

**Import**:
```typescript
import { useBackendServices } from '@/assets/js/store/backendServices'
```

#### State

```typescript
interface BackendServicesState {
  currentServiceInfo: ApiServiceInformation[]
  setupProgress: Map<BackendServiceName, SetupProgress>
  isInitialized: boolean
}
```

#### Actions

**`refreshServiceInfo()`**
Refresh service information from main process.

**Returns**: `Promise<void>`

**`startService(serviceName: BackendServiceName)`**
Start a backend service.

**Returns**: `Promise<BackendStatus>`

**`stopService(serviceName: BackendServiceName)`**
Stop a backend service.

**Returns**: `Promise<BackendStatus>`

**`setUpService(serviceName: BackendServiceName)`**
Install and set up a backend service.

**Returns**: `Promise<void>`

**Example**:
```typescript
const backendServices = useBackendServices()

await backendServices.startService('ai-backend')
console.log(backendServices.currentServiceInfo)
```

### imageGenerationPresets Store

Manages image generation presets and orchestrates workflows.

**Import**:
```typescript
import { useImageGenerationPresets } from '@/assets/js/store/imageGenerationPresets'
```

#### Actions

**`generate(mode: WorkflowModeType, sourceImage?: string)`**
Generate an image using the active preset.

**Parameters**:
- `mode`: Workflow mode ('imageGen' | 'imageEdit' | 'video')
- `sourceImage`: Optional source image for image-to-image

**Returns**: `Promise<void>`

**`ensureModelsAreAvailable()`**
Ensure all required models for the active preset are downloaded.

**Returns**: `Promise<void>`

**Example**:
```typescript
const imageGen = useImageGenerationPresets()

// Ensure models are ready
await imageGen.ensureModelsAreAvailable()

// Generate image
await imageGen.generate('imageGen')
```

### comfyUiPresets Store

Handles ComfyUI-specific workflow processing.

**Import**:
```typescript
import { useComfyUiPresets } from '@/assets/js/store/comfyUiPresets'
```

#### Actions

**`generate(imageIds: string[], mode: WorkflowModeType, sourceImage?: string)`**
Execute a ComfyUI workflow.

**Parameters**:
- `imageIds`: Array of image IDs to generate
- `mode`: Workflow mode
- `sourceImage`: Optional source image

**Returns**: `Promise<void>`

**`installCustomNodes(nodeRepos: string[])`**
Install ComfyUI custom nodes.

**Parameters**:
- `nodeRepos`: Array of repository references (e.g., "repo@commit")

**Returns**: `Promise<void>`

### openAiCompatibleChat Store

Manages LLM chat using Vercel AI SDK.

**Import**:
```typescript
import { useOpenAiCompatibleChat } from '@/assets/js/store/openAiCompatibleChat'
```

#### Actions

**`sendMessage(text: string, images?: string[])`**
Send a chat message.

**Parameters**:
- `text`: User message
- `images`: Optional array of base64 image data

**Returns**: `Promise<void>`

**`setModel(modelName: string, backend: BackendServiceName)`**
Set the active LLM model.

**Parameters**:
- `modelName`: Model identifier
- `backend`: Backend service to use

**Returns**: `Promise<void>`

**Example**:
```typescript
const chat = useOpenAiCompatibleChat()

// Set model
await chat.setModel('mistral-7b-instruct', 'ai-backend')

// Send message
await chat.sendMessage('Explain quantum computing')

// Access response
console.log(chat.messages)
```

### presets Store

Manages AI generation presets.

**Import**:
```typescript
import { usePresets } from '@/assets/js/store/presets'
```

#### State

```typescript
interface PresetsState {
  presets: Preset[]
  activePresetIndex: number
}

interface Preset {
  name: string
  type: 'text' | 'image' | 'comfy'
  backend: BackendServiceName
  settings?: Record<string, Setting>
  comfyUiApiWorkflow?: ComfyUIApiWorkflow
}
```

#### Getters

**`activePreset`**
Get the currently active preset.

**Returns**: `Preset | undefined`

**`activePresetWithVariant`**
Get active preset with applied variant settings.

**Returns**: `Preset | undefined`

#### Actions

**`setActivePreset(index: number)`**
Set the active preset by index.

**`loadPresets()`**
Load presets from external sources.

**Returns**: `Promise<void>`

### models Store

Manages AI model discovery and loading.

**Import**:
```typescript
import { useModels } from '@/assets/js/store/models'
```

#### State

```typescript
interface ModelsState {
  llmModels: ModelInfo[]
  checkpointModels: ModelInfo[]
  loraModels: ModelInfo[]
  loadedModels: Set<string>
}
```

#### Actions

**`refreshModels()`**
Refresh model lists from disk.

**Returns**: `Promise<void>`

**`downloadModel(modelInfo: ModelDownloadInfo)`**
Download a model from HuggingFace.

**Returns**: `Promise<void>`

---

## ComfyUI API

### Workflow JSON Schema

ComfyUI workflows are defined in JSON format:

```typescript
interface ComfyUIWorkflow {
  name: string
  description?: string
  comfyUIRequirements?: {
    customNodes?: string[]      // ["repo@commit"]
    pythonPackages?: string[]
  }
  requiredModels?: ModelRequirement[]
  comfyUiApiWorkflow: ComfyUIApiWorkflow
}

interface ModelRequirement {
  type: 'checkpoint' | 'lora' | 'vae' | 'controlnet'
  name: string
  path: string
  url?: string
  required: boolean
}

interface ComfyUIApiWorkflow {
  [nodeId: string]: {
    class_type: string
    inputs: Record<string, any>
    _meta?: {
      title?: string
    }
  }
}
```

### Example Workflow

```json
{
  "name": "Text to Image - SDXL",
  "comfyUIRequirements": {
    "customNodes": ["ltdrdata/ComfyUI-Manager@main"]
  },
  "requiredModels": [{
    "type": "checkpoint",
    "name": "Juggernaut XL",
    "path": "RunDiffusionPhoto_v2.safetensors",
    "required": true
  }],
  "comfyUiApiWorkflow": {
    "1": {
      "class_type": "CheckpointLoaderSimple",
      "inputs": {
        "ckpt_name": "RunDiffusionPhoto_v2.safetensors"
      }
    },
    "2": {
      "class_type": "CLIPTextEncode",
      "inputs": {
        "text": "{{prompt}}",
        "clip": ["1", 1]
      }
    }
  }
}
```

---

## Model Schemas

### Preset Schema (Zod)

```typescript
import { z } from 'zod'

const SettingSchema = z.object({
  name: z.string(),
  type: z.enum(['number', 'text', 'select', 'boolean']),
  default: z.any(),
  options: z.array(z.any()).optional(),
  min: z.number().optional(),
  max: z.number().optional(),
})

const PresetSchema = z.object({
  name: z.string(),
  type: z.enum(['text', 'image', 'comfy']),
  backend: z.enum(['ai-backend', 'comfyui-backend', 'llamacpp-backend', 'openvino-backend', 'ollama-backend']),
  settings: z.record(SettingSchema).optional(),
  comfyUiApiWorkflow: z.record(z.any()).optional(),
})
```

### Message Schema (Zod)

```typescript
const MessageSchema = z.object({
  id: z.string(),
  role: z.enum(['system', 'user', 'assistant']),
  content: z.string(),
  images: z.array(z.string()).optional(),
  timestamp: z.number(),
})
```

---

## Error Handling

### Standard Error Response

All APIs return errors in this format:

```json
{
  "code": -1,
  "message": "Error description",
  "details": {
    "field": "Additional error context"
  }
}
```

### Common Error Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| -1 | Generic error |
| -2 | Model not found |
| -3 | Service not available |
| -4 | Invalid parameters |
| -5 | File operation error |

---

## Rate Limiting

### HuggingFace Downloads

- Anonymous: 1 download per IP per hour
- With token: Higher rate limits
- Set token in Settings → Models → HuggingFace Token

---

## Authentication

### HuggingFace Token

Some models require authentication. Configure in the UI:

1. Go to Settings → Models
2. Enter your HuggingFace token
3. Token is stored securely and used for all downloads

---

## Versioning

API versioning follows semantic versioning:

- **Major**: Breaking changes
- **Minor**: New features, backward compatible
- **Patch**: Bug fixes

Current version: `3.0.x`

---

## Related Documentation

- [ARCHITECTURE.md](./ARCHITECTURE.md) - System architecture
- [IPC-GUIDE.md](./IPC-GUIDE.md) - IPC communication patterns
- [WORKFLOWS.md](./WORKFLOWS.md) - ComfyUI workflow guide

# Getting Started with AI Playground

Welcome to AI Playground! This guide will help you understand what AI Playground is, how to install it, and how to get started using its features.

## What is AI Playground?

AI Playground is an **offline, standalone desktop application** that enables users to interact with generative AI directly on their PC using Intel graphics hardware. It's designed as a local alternative to cloud-based AI services like ChatGPT, Gemini, or DALL-E.

### Key Features

- **💬 Chat & LLM Inference**: Interact with large language models like Mistral, Llama, Qwen, DeepSeek R1, and more
- **🎨 Image Generation**: Create images using Stable Diffusion, SDXL, Flux.1, and other diffusion models
- **✏️ Image Editing**: Inpainting, outpainting, upscaling, and style transfer
- **🎥 Video Generation**: Create short videos with AI (LTX-Video, Wan2.1)
- **🔍 Vision Models**: Analyze images with Qwen3-VL and other vision-language models
- **🧠 Reasoning Models**: Use models like DeepSeek R1 for complex reasoning tasks
- **📚 RAG (Retrieval-Augmented Generation)**: Query your documents with AI
- **🔒 Privacy-First**: All processing happens locally on your PC - no cloud, no data sharing

### Supported Hardware

AI Playground requires:
- **Windows OS** (10 or 11)
- **Intel Core Ultra Series 3, 2H, 2V, or Series 1 H processor** OR
- **Intel Arc GPU Series A or B (discrete)** with **8GB+ VRAM**

## Installation

### Quick Install (Recommended)

1. **Download the installer** from the [latest release](https://github.com/intel/AI-Playground/releases/latest)
2. **Run the installer** - This installs the Electron frontend (takes ~2 minutes)
3. **First Launch Setup** - On first run, AI Playground will:
   - Display a Setup window
   - Download and install backend components (Python, PyTorch, etc.)
   - This may take 10-30 minutes depending on your internet connection
4. **Start Using** - Once setup completes, you're ready to go!

### What Gets Installed?

AI Playground consists of several components:

- **Electron Frontend** (WebUI) - The graphical interface
- **Python Backend Services**:
  - `ai-backend` - Primary backend for LLM chat and model management (Flask API)
  - `comfyui-backend` - ComfyUI for image/video generation workflows
  - `llamacpp-backend` - LlamaCPP for GGUF models (optional)
  - `openvino-backend` - Intel-optimized inference (optional)
  - `ollama-backend` - Ollama compatibility (optional, preview)
- **AI Models** - Downloaded on-demand as you use features

## First Steps

### 1. Backend Setup

After installation, you'll see the **Setup** screen:

1. Click **"Install Backend Components"**
2. Wait for the installation to complete (progress bar shows status)
3. The app will automatically restart when ready

### 2. Understanding the Interface

The main interface has three key areas:

- **Top Navigation Bar**: Switch between Chat, Image Generation, Settings
- **Sidebar** (Chat view): Manage conversations and models
- **Main Area**: Your workspace for prompts and outputs
- **Settings Panel**: Configure models, backends, and presets

### 3. Using Chat (LLM)

1. **Navigate to Chat tab**
2. **Select a model**:
   - Click the model dropdown in the sidebar
   - First time: Click "Download Models" to fetch an LLM
   - Recommended starters: `Mistral-7B-Instruct`, `Qwen3-4B`, `DeepSeek-R1-Distill`
3. **Start chatting**:
   - Type your message in the input box
   - Press Enter or click Send
   - The AI will respond in real-time (streaming)

**Tips**:
- Use **Vision models** (Qwen3-VL) to analyze images
- Enable **RAG** to query documents from your PC
- Try **Reasoning models** (DeepSeek R1) for math, coding, or complex problems

### 4. Generating Images

1. **Navigate to Create tab**
2. **Select a preset**:
   - Choose from Flux.1, SDXL, SD 1.5, Z-Image, etc.
   - Each preset has different speed/quality tradeoffs
3. **Enter your prompt**:
   - Describe the image you want to create
   - Be specific! Example: "A serene mountain landscape at sunset, photorealistic, 4K"
4. **Adjust settings** (optional):
   - Steps: More steps = higher quality but slower
   - Guidance: How closely to follow your prompt
   - Seed: For reproducible results
5. **Click Generate**
6. **View results** in the output gallery

**Image Editing Modes**:
- **Text-to-Image**: Create from scratch
- **Image-to-Image**: Modify an existing image
- **Inpainting**: Fill in or edit specific areas
- **Outpainting**: Extend an image beyond its borders
- **Upscaling**: Increase resolution

### 5. Model Management

AI Playground uses large AI models that need to be downloaded:

1. **Navigate to Settings → Models**
2. **Browse available models** by category:
   - LLM Models (Chat)
   - Diffusion Models (Image)
   - VAE, LoRA, Embeddings
3. **Download models**:
   - Click the download icon next to any model
   - Models are sourced from HuggingFace
   - Progress is shown in real-time
4. **Manage storage**:
   - View model locations
   - Delete unused models to free space

**Model Paths**:
- LLM: `%USERPROFILE%\ai-models\llm\`
- Checkpoints: `%USERPROFILE%\ai-models\checkpoint\`
- LoRAs: `%USERPROFILE%\ai-models\lora\`

### 6. Backend Services

AI Playground runs multiple backend services for different AI tasks:

1. **Navigate to Settings → Backends**
2. **View service status**:
   - 🟢 Green: Running
   - 🟡 Yellow: Starting
   - 🔴 Red: Stopped
   - ⚪ Gray: Not installed
3. **Manage services**:
   - Start/Stop services to save GPU memory
   - Install optional backends (LlamaCPP, OpenVINO, Ollama)
   - View service logs for troubleshooting

**Backend Purposes**:
- **ai-backend** (Required): Model downloads, LLM inference via OpenAI-compatible API
- **comfyui-backend**: All image/video generation workflows
- **llamacpp-backend**: GGUF models (CPU/GPU hybrid)
- **openvino-backend**: Intel-optimized inference
- **ollama-backend**: Ollama model compatibility

## Common Workflows

### Vision + Chat: Analyze an Image

1. Go to **Chat** tab
2. Select a **vision model** (e.g., Qwen3-VL)
3. Click the **image attachment** icon (📎)
4. Upload an image
5. Ask a question: "What's in this image?" or "Describe this scene in detail"

### RAG: Chat with Your Documents

1. Go to **Settings → RAG**
2. Click **"Add Documents"**
3. Select PDF, TXT, or Markdown files
4. Wait for embedding (creates searchable index)
5. In **Chat**, enable **RAG mode**
6. Ask questions about your documents

### Image Editing: Inpainting

1. Go to **Create** tab, select **Inpainting** preset
2. Upload a base image
3. Use the brush tool to **mask** the area to edit
4. Enter a prompt describing what should be in the masked area
5. Click **Generate**

### Video Generation

1. Go to **Create** tab
2. Select **LTX-Video** or **Wan2.1** preset
3. Enter a description of the video
4. Adjust duration and resolution
5. Click **Generate** (this may take several minutes)

## Tips & Best Practices

### Performance Optimization

- **Close unused backends** to free GPU memory
- **Use lower step counts** for faster generation (20-30 steps)
- **Start with smaller models** (4B parameter LLMs, SD 1.5 for images)
- **Monitor GPU usage** in Task Manager

### Prompt Engineering

**For Chat**:
- Be specific and clear
- Provide context
- Use system prompts for consistent behavior
- Break complex tasks into steps

**For Image Generation**:
- Include style keywords: "photorealistic", "anime", "oil painting"
- Specify quality: "4K", "highly detailed", "masterpiece"
- Mention lighting: "cinematic lighting", "soft light", "golden hour"
- Use negative prompts to exclude unwanted elements

### Model Selection

- **Fast Chat**: SmolLM2-1.7B, Phi-3-Mini
- **Balanced Chat**: Mistral-7B, Qwen3-4B
- **High Quality Chat**: GPT-OSS-20B, DeepSeek-R1
- **Vision**: Qwen3-VL, InternVL2
- **Fast Images**: Flux.1-Schnell, SD 1.5 + LCM-LoRA
- **High Quality Images**: SDXL, Flux.1-Dev

## Troubleshooting

### Backend Won't Start

1. Check **Settings → Backends** for error messages
2. Try **Restart Backend**
3. Check if another app is using the port
4. View logs: Press `Ctrl+Shift+I` → Console tab

### Model Download Fails

1. Check your **internet connection**
2. Verify you're not behind a restrictive firewall
3. Try downloading the model again
4. For HuggingFace rate limits, add a **HF token** in Settings

### Out of Memory Errors

1. **Close unused backends** to free VRAM
2. Use **smaller models** (e.g., 4B instead of 7B)
3. Reduce **batch size** or **image resolution**
4. Enable **CPU offloading** in Settings (slower but uses less VRAM)

### Slow Performance

1. Update **Intel graphics drivers** to the latest version
2. Ensure you're using the **Intel Arc GPU** (check Device Manager)
3. Close **other GPU-intensive applications**
4. Use **optimized backends** (OpenVINO for LLM, GGUF for VRAM savings)

### Installation Issues

- **Missing VC++ Redistributables**: Install from [Microsoft](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist)
- **Python Conflicts**: Uninstall conflicting Python installations
- **Antivirus Blocking**: Temporarily disable and retry
- **Sleep Interrupts**: Keep PC awake during setup

## Getting Help

- **Discord**: Join the Intel Insiders Discord server
- **GitHub Issues**: Report bugs at [github.com/intel/AI-Playground/issues](https://github.com/intel/AI-Playground/issues)
- **User Guide**: Download the [detailed PDF guide](https://github.com/intel/AI-Playground/blob/main/AI%20Playground%20Users%20Guide.pdf)
- **Documentation**: Browse this `/docs` folder for technical details

## Next Steps

Now that you're familiar with the basics:

- **Explore presets**: Try different image generation workflows
- **Download more models**: Experiment with various LLMs and checkpoints
- **Customize settings**: Tune parameters for your hardware
- **Read the architecture docs**: Understand how AI Playground works under the hood (see [ARCHITECTURE.md](./ARCHITECTURE.md))
- **Contribute**: Check out [CONTRIBUTING.md](../CONTRIBUTING.md) to help improve AI Playground

Happy AI exploration! 🚀

# AI Playground Documentation

Welcome to the comprehensive documentation for AI Playground! This guide will help you understand, use, and contribute to the project.

## 📚 Documentation Index

### For Users

#### [**Getting Started Guide**](./GETTING-STARTED.md)
Your first stop for learning AI Playground. Covers:
- What AI Playground is and what it does
- Installation instructions
- First steps and basic usage
- Common workflows (chat, image generation, video)
- Tips and troubleshooting

**Start here if you're new to AI Playground!**

#### [**User Guide PDF**](../AI%20Playground%20Users%20Guide.pdf)
Comprehensive PDF guide with screenshots and detailed walkthroughs for all features.

### For Developers

#### [**Architecture Overview**](./ARCHITECTURE.md)
Deep dive into the system architecture. Covers:
- System design and component interaction
- Frontend (Vue.js) and backend (Python) structure
- Electron main process and IPC communication
- Service registry and backend management
- Technology stack and design patterns
- Data flow and architectural evolution

**Essential reading for developers working on AI Playground.**

#### [**API Reference**](./API-REFERENCE.md)
Complete API documentation. Covers:
- IPC API (Electron communication)
- Backend REST APIs (Flask endpoints)
- Store APIs (Pinia state management)
- ComfyUI API (workflow submission)
- Model schemas and error handling

**Reference guide for API integration and development.**

#### [**Development Guide**](./DEVELOPMENT.md)
Comprehensive developer handbook. Covers:
- Development environment setup
- Project structure and organization
- Development workflows and patterns
- Coding standards (TypeScript, Python, Vue)
- Testing and debugging
- Building and packaging
- Contributing guidelines

**Complete guide for contributing to AI Playground.**

#### [**ComfyUI Workflows Guide**](./WORKFLOWS.md)
Everything about ComfyUI workflows. Covers:
- ComfyUI basics and concepts
- Workflow JSON structure
- Creating custom workflows
- Preset system
- Custom nodes management
- Model requirements
- Workflow execution and debugging

**Essential for creating and managing image/video generation workflows.**

### Additional Documentation

#### [**IPC Communication Guide**](./IPC-GUIDE.md)
Detailed guide on Electron IPC patterns (coming soon).

#### [**Model Management**](./MODEL-MANAGEMENT.md)
Guide to managing AI models (coming soon).

#### [**Troubleshooting**](./TROUBLESHOOTING.md)
Common issues and solutions (coming soon).

#### [**AGENTS.md**](../AGENTS.md)
Instructions and context for AI coding agents working on this repository. Contains detailed architectural patterns, development philosophy, and code conventions.

### Community Documentation

#### [**arc42 Documentation**](./arc42/arc42_doc.md)
Architecture documentation using the arc42 template, including:
- Requirements and quality goals
- Context and scope diagrams
- Building block view
- Technical debt and risks

## 🚀 Quick Links

### Getting Started
- [Installation](./GETTING-STARTED.md#installation)
- [First Steps](./GETTING-STARTED.md#first-steps)
- [Using Chat](./GETTING-STARTED.md#3-using-chat-llm)
- [Generating Images](./GETTING-STARTED.md#4-generating-images)

### Development
- [Setup Dev Environment](./DEVELOPMENT.md#development-setup)
- [Project Structure](./DEVELOPMENT.md#project-structure)
- [Coding Standards](./DEVELOPMENT.md#coding-standards)
- [Contributing](./DEVELOPMENT.md#contributing-guidelines)

### Architecture
- [System Overview](./ARCHITECTURE.md#system-architecture)
- [Component Architecture](./ARCHITECTURE.md#core-components)
- [Data Flow](./ARCHITECTURE.md#data-flow)
- [Design Patterns](./ARCHITECTURE.md#design-patterns)

### APIs
- [IPC API](./API-REFERENCE.md#ipc-api-electron)
- [Backend APIs](./API-REFERENCE.md#backend-rest-apis)
- [Store APIs](./API-REFERENCE.md#store-apis-pinia)

## 📖 How to Use This Documentation

### If you're a **first-time user**:
1. Start with [Getting Started Guide](./GETTING-STARTED.md)
2. Follow the installation instructions
3. Try the common workflows
4. Refer to the [User Guide PDF](../AI%20Playground%20Users%20Guide.pdf) for detailed features

### If you're a **developer**:
1. Read [Architecture Overview](./ARCHITECTURE.md) to understand the system
2. Follow [Development Guide](./DEVELOPMENT.md) to set up your environment
3. Refer to [API Reference](./API-REFERENCE.md) when working with APIs
4. Check [AGENTS.md](../AGENTS.md) for detailed coding conventions

### If you're working with **image generation**:
1. Read [ComfyUI Workflows Guide](./WORKFLOWS.md)
2. Understand the preset system
3. Learn how to create custom workflows
4. Explore custom nodes and model management

### If you're **troubleshooting**:
1. Check [Getting Started - Troubleshooting](./GETTING-STARTED.md#troubleshooting)
2. Review [Common Issues](./DEVELOPMENT.md#common-issues-and-solutions)
3. Search GitHub Issues for similar problems
4. Ask in Discord or create a new issue

## 🏗️ Project Overview

AI Playground is an **offline, standalone desktop application** that enables users to interact with generative AI directly on their PC using Intel graphics hardware.

### Key Features
- 💬 **LLM Chat**: Mistral, Llama, Qwen, DeepSeek R1, and more
- 🎨 **Image Generation**: Stable Diffusion, SDXL, Flux.1
- ✏️ **Image Editing**: Inpainting, outpainting, upscaling
- 🎥 **Video Generation**: LTX-Video, Wan2.1
- 🔍 **Vision Models**: Image analysis with Qwen3-VL
- 🧠 **Reasoning**: Complex problem-solving with R1 models
- 📚 **RAG**: Document question-answering
- 🔒 **Privacy-First**: All processing happens locally

### Technology Stack
- **Frontend**: Vue.js 3, TypeScript, TailwindCSS, Pinia
- **Desktop**: Electron
- **Backend**: Python, Flask, PyTorch, ComfyUI
- **AI Engines**: IPEX-LLM, OpenVINO, llama.cpp, Diffusers

### Architecture
Multi-process architecture with:
- **Electron Main Process**: Service orchestration and IPC
- **Vue.js Frontend**: User interface and state management
- **Python Backend Services**: AI model inference (5 backends)

See [Architecture Overview](./ARCHITECTURE.md) for details.

## 🤝 Contributing

We welcome contributions! Here's how to get involved:

1. **Report Bugs**: Open an issue on GitHub
2. **Suggest Features**: Discuss ideas in issues or Discord
3. **Submit Pull Requests**: Follow the [Development Guide](./DEVELOPMENT.md)
4. **Improve Documentation**: Help us make docs better
5. **Create Workflows**: Share your custom ComfyUI workflows

See [CONTRIBUTING.md](../CONTRIBUTING.md) for detailed guidelines.

## 📝 Documentation Standards

When contributing to documentation:

### Writing Style
- **Clear and Concise**: Use simple language
- **Well-Structured**: Use headings and lists
- **Code Examples**: Provide working code snippets
- **Visual Aids**: Include diagrams where helpful
- **Cross-References**: Link to related documentation

### Formatting
- Use Markdown format
- Include table of contents for long documents
- Use code blocks with language specification
- Add alt text for images
- Keep line length reasonable (80-100 characters)

### Content
- **Assume No Prior Knowledge**: Explain concepts
- **Provide Context**: Why, not just how
- **Include Examples**: Show real-world usage
- **Stay Updated**: Reflect current code state
- **Be Accurate**: Test instructions before documenting

## 🔍 Finding Information

### Search Tips
- Use GitHub's repository search for code
- Search docs folder for documentation
- Check issue tracker for discussions
- Browse commit history for changes

### Getting Help
- **Discord**: Join Intel Insiders Discord for community help
- **GitHub Issues**: Report bugs or ask questions
- **Discussions**: Start a discussion for general topics
- **Stack Overflow**: Tag with `ai-playground` (when available)

## 📅 Documentation Roadmap

### Coming Soon
- [ ] **IPC Communication Guide**: Detailed IPC patterns and examples
- [ ] **Model Management Guide**: Comprehensive model handling
- [ ] **Troubleshooting Guide**: Extended issue resolution
- [ ] **Performance Tuning**: Optimization techniques
- [ ] **Security Guide**: Best practices for secure usage
- [ ] **Testing Guide**: Unit, integration, and E2E testing
- [ ] **Deployment Guide**: Distribution and packaging

### Planned Improvements
- [ ] Video tutorials and walkthroughs
- [ ] Interactive examples and demos
- [ ] Architecture decision records (ADRs)
- [ ] API versioning documentation
- [ ] Multilingual documentation

## 📄 License and Legal

- **License**: See [LICENSE](../LICENSE)
- **Notices & Disclaimers**: See [notices-disclaimers.md](../notices-disclaimers.md)
- **Third-Party Notices**: See [3rdpartynoticeslicenses.txt](../3rdpartynoticeslicenses.txt)
- **Security Policy**: See [SECURITY.md](../SECURITY.md)
- **Code of Conduct**: See [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md)

## 🌟 Credits

AI Playground is built on top of incredible open-source projects:
- **PyTorch** - Deep learning framework
- **ComfyUI** - Node-based AI workflow system
- **Vue.js** - Progressive JavaScript framework
- **Electron** - Cross-platform desktop apps
- **OpenVINO** - Intel's inference optimization toolkit
- **llama.cpp** - LLM inference in C++
- And countless other projects! See [credits](../readme.md#credit)

## 📧 Contact

- **GitHub**: [github.com/intel/AI-Playground](https://github.com/intel/AI-Playground)
- **Issues**: [github.com/intel/AI-Playground/issues](https://github.com/intel/AI-Playground/issues)
- **Releases**: [github.com/intel/AI-Playground/releases](https://github.com/intel/AI-Playground/releases)

---

**Happy Learning! 🚀**

Whether you're using AI Playground for creative projects or contributing to its development, we hope this documentation helps you succeed. Don't hesitate to reach out if you have questions or suggestions for improving the docs!

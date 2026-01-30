# Troubleshooting Guide

This guide helps you diagnose and resolve common issues with AI Playground.

## Table of Contents

1. [Installation Issues](#installation-issues)
2. [Backend Service Problems](#backend-service-problems)
3. [Model Download Issues](#model-download-issues)
4. [Performance Problems](#performance-problems)
5. [Image Generation Issues](#image-generation-issues)
6. [Chat/LLM Issues](#chatllm-issues)
7. [UI and Display Issues](#ui-and-display-issues)
8. [Error Messages](#error-messages)
9. [Advanced Debugging](#advanced-debugging)

---

## Installation Issues

### Installer Won't Start

**Symptoms**: Double-clicking the installer does nothing, or immediate crash

**Possible Causes**:
- Missing Visual C++ Redistributables
- Antivirus blocking the installer
- Corrupted download

**Solutions**:

1. **Install VC++ Redistributables**:
   - Download from [Microsoft](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist)
   - Install both x64 and x86 versions
   - Restart computer
   - Try installer again

2. **Disable Antivirus Temporarily**:
   - Right-click antivirus icon in system tray
   - Select "Disable" or "Pause Protection"
   - Run installer
   - Re-enable antivirus after installation

3. **Verify Download**:
   - Re-download the installer
   - Check file size matches the release page
   - Try downloading from a different network

### Backend Setup Fails

**Symptoms**: Setup window shows error, installation progress stops

**Common Errors**:

#### "Network Error" or "Download Failed"

**Causes**: Internet connection issues, firewall blocking

**Solutions**:
```
1. Check internet connection
2. Disable VPN temporarily
3. Configure firewall to allow AI Playground:
   - Open Windows Firewall
   - Allow an app through firewall
   - Add AI Playground executable
4. Try on a different network (mobile hotspot)
```

#### "Python Installation Failed"

**Causes**: Conflicting Python installation, insufficient permissions

**Solutions**:
```
1. Check for existing Python installations:
   - Open Command Prompt
   - Run: where python
2. If conflicting Python found:
   - Uninstall via Control Panel → Programs
   - Restart computer
   - Try AI Playground setup again
3. Run as Administrator:
   - Right-click AI Playground
   - Select "Run as Administrator"
```

#### "Permission Denied" Errors

**Causes**: User account doesn't have sufficient permissions

**Solutions**:
```
1. Ensure user account has admin rights
2. Disable User Account Control (UAC) temporarily:
   - Control Panel → User Accounts → Change UAC settings
   - Move slider to "Never notify"
   - Restart and try again
3. Install to different location:
   - Choose directory without special characters
   - Avoid Program Files (use C:\AI-Playground)
```

### "Llama.cpp Embedding Issues"

**Symptoms**: Installation hangs at llama.cpp setup, or embeddings fail

**Causes**: Driver cache issues, antivirus interference

**Solutions**:
```
1. Update Intel Graphics Drivers:
   - Visit Intel Download Center
   - Download latest Arc GPU drivers
   - Install and restart
   
2. Clean Driver Cache with DDU:
   - Download Display Driver Uninstaller
   - Boot to Safe Mode
   - Run DDU to clean old drivers
   - Restart and install fresh drivers
   
3. Disable Antivirus:
   - Completely disable (not just pause)
   - Restart computer
   - Try setup again
   
4. Check Vulkan Support:
   - Download vulkan_caps_viewer
   - Verify Vulkan is working on your GPU
```

---

## Backend Service Problems

### Service Won't Start

**Symptoms**: Service status shows "Stopped" or "Error", clicking Start does nothing

**Diagnosis**:
```
1. Open DevTools: Ctrl+Shift+I
2. Go to Console tab
3. Look for error messages related to service
4. Check Settings → Backends for status details
```

**Common Causes & Solutions**:

#### Port Already in Use

**Error**: "Address already in use: 5000"

**Solution**:
```
1. Find process using the port:
   netstat -ano | findstr :5000
   
2. Kill the process:
   taskkill /PID <PID> /F
   
3. Or change port in settings:
   Settings → Advanced → Backend Ports
```

#### Python Environment Corrupted

**Error**: "ModuleNotFoundError" or import errors

**Solution**:
```
1. Navigate to AI Playground data directory:
   %APPDATA%\ai-playground
   
2. Delete build-envs folder
   
3. Restart AI Playground
   
4. Reinstall backend:
   Settings → Backends → Reinstall
```

#### GPU Not Detected

**Error**: "No compatible GPU found"

**Solution**:
```
1. Verify GPU in Device Manager:
   - Win+X → Device Manager
   - Display Adapters → Check for Intel Arc GPU
   
2. Update drivers if GPU shown
   
3. If GPU not shown:
   - Check BIOS settings (enable iGPU)
   - Reseat discrete GPU (if applicable)
   - Check power connections
   
4. For laptops with hybrid graphics:
   - Disable iGPU in Device Manager
   - Force use of dGPU
```

### Service Crashes Frequently

**Symptoms**: Backend stops unexpectedly, needs frequent restarts

**Possible Causes**:
- Out of memory (GPU or RAM)
- Model incompatibility
- Driver issues
- Corrupted model files

**Solutions**:

1. **Check GPU Memory**:
   ```
   - Open Task Manager
   - Performance tab
   - GPU section
   - Monitor VRAM usage
   
   If VRAM is maxed out:
   - Close other GPU apps
   - Use smaller models
   - Stop unused backends
   ```

2. **Update Drivers**:
   ```
   - Intel Download Center
   - Install latest Arc GPU drivers
   - Restart computer
   ```

3. **Check Model Files**:
   ```
   - Verify model file integrity
   - Re-download corrupted models
   - Delete and re-download:
     Settings → Models → Delete
   ```

4. **Review Logs**:
   ```
   - Ctrl+Shift+I → Console
   - Look for crash patterns
   - Note which operations trigger crashes
   - Report to GitHub Issues
   ```

---

## Model Download Issues

### Download Fails or Stalls

**Symptoms**: Download progress stops, error message appears

**Common Issues**:

#### HuggingFace Rate Limit

**Error**: "Too many requests" or 429 error

**Solution**:
```
1. Wait 1 hour for rate limit reset
   
2. Or add HuggingFace token:
   - Create account at huggingface.co
   - Generate token: Settings → Access Tokens
   - In AI Playground:
     Settings → Models → HuggingFace Token
   - Paste token and save
```

#### Network Timeout

**Error**: "Connection timeout" or "Download failed"

**Solution**:
```
1. Check internet connection speed
   - Minimum 10 Mbps recommended
   
2. Increase timeout:
   - Settings → Advanced → Download Timeout
   - Set to 300 seconds or higher
   
3. Use wired connection if possible
   
4. Pause other downloads/uploads
```

#### Insufficient Disk Space

**Error**: "No space left on device"

**Solution**:
```
1. Check available space:
   - Navigate to model directory
   - Check drive free space (need 20GB+ for large models)
   
2. Free up space:
   - Delete unused models
   - Move models to larger drive
   - Clean temp files
   
3. Change model directory:
   - Settings → Models → Model Paths
   - Select drive with more space
```

### Model Won't Load

**Symptoms**: Model shows as downloaded but won't load, or errors during loading

**Causes**:
- Corrupted download
- Incompatible model format
- Insufficient GPU memory

**Solutions**:

1. **Verify File Integrity**:
   ```
   - Check file size matches expected size
   - Compare hash if provided
   - Re-download if mismatch
   ```

2. **Check Format Compatibility**:
   ```
   Supported formats:
   - LLM: .gguf, .safetensors, .bin
   - Checkpoint: .safetensors, .ckpt
   - LoRA: .safetensors
   
   If wrong format:
   - Find compatible version
   - Convert using model conversion tools
   ```

3. **GPU Memory Check**:
   ```
   Model size vs. VRAM:
   - 4B params: ~8GB VRAM
   - 7B params: ~14GB VRAM
   - SDXL: ~8GB VRAM
   
   If insufficient:
   - Use quantized models (Q4, Q8)
   - Try OpenVINO or llama.cpp backends
   - Lower context length for LLMs
   ```

---

## Performance Problems

### Slow Generation Speed

**Symptoms**: Image/text generation takes very long

**Causes & Solutions**:

#### Using CPU Instead of GPU

**Diagnosis**:
```
- Task Manager → Performance → GPU
- Check if GPU utilization is low during generation
```

**Solution**:
```
1. Verify GPU selected:
   Settings → General → Device Selection
   
2. For hybrid graphics:
   - Windows Graphics Settings
   - Add AI Playground
   - Set to "High Performance"
   
3. Update GPU drivers
```

#### Suboptimal Settings

**For Image Generation**:
```
Too many steps:
- Reduce steps to 20-30 for drafts
- Use 40-50 for final images
- LCM LoRA: Use 4-8 steps

High resolution:
- Start with 512x512 or 768x768
- Upscale separately if needed

Wrong sampler:
- Euler a: Fast, good quality
- DPM++ 2M Karras: High quality, slower
```

**For LLM Chat**:
```
Context too long:
- Limit to 2048 tokens for faster response
- Summarize long conversations

Wrong backend:
- ai-backend: Full features but slower
- llamacpp: Faster for GGUF models
- openvino: Fastest for Intel GPUs
```

#### Background Processes

**Solution**:
```
1. Close other GPU apps:
   - Games, video editing, browsers with GPU acceleration
   
2. Close unused AI Playground backends:
   Settings → Backends → Stop unused services
   
3. Restart AI Playground periodically
```

### High Memory Usage

**Symptoms**: System becomes sluggish, apps crash

**Monitoring**:
```
Task Manager:
- Memory: Should be under 80% usage
- GPU Memory: Check VRAM usage
```

**Solutions**:

1. **Free RAM**:
   ```
   - Close browser tabs
   - Stop background apps
   - Restart AI Playground
   ```

2. **Free VRAM**:
   ```
   - Stop unused backends
   - Use smaller models
   - Lower resolution/batch size
   ```

3. **Increase Virtual Memory**:
   ```
   - System Properties → Advanced
   - Performance → Settings → Advanced
   - Virtual Memory → Change
   - Set to System Managed or 1.5x RAM
   ```

---

## Image Generation Issues

### Image Quality Problems

#### Blurry or Low Quality Images

**Causes**:
- Too few steps
- Wrong sampler
- Poor model quality

**Solutions**:
```
1. Increase steps: 40-50 for high quality
2. Try different samplers: DPM++ 2M Karras
3. Increase CFG scale: 7-9 for more prompt adherence
4. Use higher quality models: SDXL, Flux.1
5. Upscale post-generation: 2x or 4x upscaler
```

#### Wrong Colors or Composition

**Causes**:
- Vague prompt
- Wrong CFG scale
- Model limitations

**Solutions**:
```
1. Improve prompt:
   - Be specific: "vibrant red sunset, golden hour lighting"
   - Add quality tags: "highly detailed, professional photo"
   
2. Adjust CFG scale:
   - Too low (< 5): Ignores prompt
   - Too high (> 12): Oversaturated
   - Sweet spot: 7-9
   
3. Use negative prompt:
   - "blurry, low quality, bad anatomy, ugly"
```

### Image Generation Fails

#### ComfyUI Execution Error

**Error**: "Node X failed to execute"

**Diagnosis**:
```
1. Check error message in console (Ctrl+Shift+I)
2. Note which node failed
3. Common failures:
   - Model not found
   - Missing custom node
   - Incompatible parameters
```

**Solutions**:

**Model Not Found**:
```
- Download required model
- Check model path in workflow
- Verify model is in correct folder
```

**Missing Custom Node**:
```
- Settings → ComfyUI → Custom Nodes
- Install required node
- Restart ComfyUI backend
```

**Parameter Error**:
```
- Check parameter ranges (e.g., denoise: 0-1)
- Verify dimensions are multiples of 8
- Reset to preset defaults
```

#### Out of Memory (CUDA OOM)

**Error**: "CUDA out of memory"

**Solutions**:
```
1. Immediate:
   - Reduce resolution (512x512)
   - Lower batch size to 1
   - Restart backend to clear VRAM
   
2. Long-term:
   - Use smaller checkpoint models
   - Enable model offloading (if available)
   - Upgrade GPU (if feasible)
```

---

## Chat/LLM Issues

### Chat Responses Are Slow

**Causes & Solutions**:

1. **Large Model**:
   ```
   - Switch to smaller model: Phi-3, SmolLM
   - Use quantized versions (Q4, Q8)
   ```

2. **Long Context**:
   ```
   - Reduce max tokens: 512-1024 for faster
   - Summarize long conversations
   - Clear chat history periodically
   ```

3. **Wrong Backend**:
   ```
   - Try llama.cpp for GGUF models
   - Try OpenVINO for Intel optimization
   ```

### Chat Responses Are Nonsensical

**Symptoms**: Repetitive text, gibberish, off-topic responses

**Causes**:

1. **Temperature Too High**:
   ```
   - Lower temperature: 0.5-0.7 for coherent
   - Higher (0.8-1.0): More creative but risky
   ```

2. **Wrong Model**:
   ```
   - Verify using instruct/chat model (not base)
   - Check model is fully downloaded
   ```

3. **Context Confusion**:
   ```
   - Clear chat and start fresh
   - Use system prompt to set behavior
   ```

### Vision Model Won't Process Images

**Symptoms**: Image attached but not analyzed

**Causes**:

1. **Wrong Model**:
   ```
   - Ensure using vision model: Qwen3-VL, InternVL
   - Regular LLMs can't process images
   ```

2. **Image Format**:
   ```
   - Supported: JPG, PNG, WebP
   - Max size: 10MB recommended
   - Convert if needed
   ```

3. **Backend Issue**:
   ```
   - Restart vision model backend
   - Check backend logs for errors
   ```

---

## UI and Display Issues

### UI Freezes or Becomes Unresponsive

**Causes**:
- Long-running operation blocking UI
- Memory leak
- Graphics driver issue

**Solutions**:

1. **Wait for Operation**:
   ```
   - Check if backend is processing
   - Look for progress indicator
   - Give it time for large models/images
   ```

2. **Force Restart**:
   ```
   - Ctrl+Alt+Del → Task Manager
   - End AI Playground process
   - Restart application
   ```

3. **Update Drivers**:
   ```
   - Update Intel graphics drivers
   - Update display drivers
   ```

### Display Glitches or Artifacts

**Symptoms**: Visual artifacts, flickering, black screens

**Solutions**:

1. **Disable Hardware Acceleration**:
   ```
   - Settings → Advanced
   - Disable "Use hardware acceleration"
   - Restart application
   ```

2. **Update Graphics Drivers**

3. **Check Display Settings**:
   ```
   - Set scaling to 100% in Windows
   - Use native resolution
   ```

### Missing UI Elements

**Symptoms**: Buttons, menus, or panels missing

**Solutions**:

1. **Reset UI Layout**:
   ```
   - Settings → General → Reset UI
   - Restart application
   ```

2. **Clear Application Cache**:
   ```
   - Close AI Playground
   - Navigate to: %APPDATA%\ai-playground
   - Delete cache folder
   - Restart
   ```

3. **Reinstall**:
   ```
   - Uninstall AI Playground
   - Delete %APPDATA%\ai-playground
   - Reinstall from latest release
   ```

---

## Error Messages

### Common Error Messages and Solutions

#### "Backend not available"

**Meaning**: Selected backend service isn't running

**Solution**:
```
- Settings → Backends
- Start the required backend
- Wait for "Running" status
```

#### "Model not loaded"

**Meaning**: Model isn't cached in memory

**Solution**:
```
- First use: Model loads automatically (be patient)
- Subsequent: Should be faster
- If persists: Restart backend
```

#### "Invalid prompt"

**Meaning**: Prompt contains unsupported characters or is empty

**Solution**:
```
- Remove special characters: <, >, {, }
- Ensure prompt isn't empty
- Try simpler prompt first
```

#### "Generation timeout"

**Meaning**: Operation took too long

**Solution**:
```
- Increase timeout: Settings → Advanced
- Simplify operation (lower steps, smaller size)
- Check if backend is frozen (restart if needed)
```

#### "Workflow validation failed"

**Meaning**: ComfyUI workflow has missing dependencies

**Solution**:
```
- Check error details
- Install missing custom nodes
- Download required models
- Verify workflow compatibility
```

---

## Advanced Debugging

### Collecting Debug Information

For reporting issues, collect:

1. **Application Logs**:
   ```
   - Ctrl+Shift+I → Console tab
   - Right-click → Save as... → logs.txt
   ```

2. **System Information**:
   ```
   - Windows version (Win+R → winver)
   - GPU model (Device Manager)
   - RAM amount
   - AI Playground version
   ```

3. **Backend Logs**:
   ```
   - Navigate to: %APPDATA%\ai-playground\logs
   - Include relevant .log files
   ```

### Using DevTools

Open DevTools with `Ctrl+Shift+I`:

**Console Tab**:
- View JavaScript errors
- Check API call responses
- Monitor IPC communication

**Network Tab**:
- Monitor backend API calls
- Check response times
- View request/response data

**Sources Tab**:
- Set breakpoints in code
- Step through execution
- Inspect variables

### Verbose Logging

Enable detailed logging:

```
1. Create file: %APPDATA%\ai-playground\config.json

2. Add:
{
  "logLevel": "debug",
  "verboseLogging": true
}

3. Restart AI Playground

4. Logs will be much more detailed
```

### Testing Backends Directly

Test if backend is responding:

```bash
# Test AI Backend
curl http://localhost:5000/healthy

# Test ComfyUI
curl http://localhost:8188/system_stats

# Test LlamaCPP
curl http://localhost:8080/health
```

### Clean Reinstall

For persistent issues:

```
1. Uninstall AI Playground

2. Delete all data:
   - %APPDATA%\ai-playground
   - %LOCALAPPDATA%\ai-playground

3. Optional: Delete models (if corrupted):
   - %USERPROFILE%\ai-models

4. Restart computer

5. Install fresh from latest release

6. Run setup process
```

---

## Getting Help

If you've tried everything and still have issues:

1. **Search GitHub Issues**:
   - [github.com/intel/AI-Playground/issues](https://github.com/intel/AI-Playground/issues)
   - Someone may have had the same problem

2. **Create New Issue**:
   - Provide detailed description
   - Include error messages
   - Attach logs
   - List steps to reproduce
   - Specify system info

3. **Join Discord**:
   - Intel Insiders Discord
   - AI Playground channel
   - Community support

4. **Check Documentation**:
   - [Getting Started](./GETTING-STARTED.md)
   - [Development Guide](./DEVELOPMENT.md)
   - [User Guide PDF](../AI%20Playground%20Users%20Guide.pdf)

---

## Preventive Maintenance

Avoid issues by:

1. **Keep Updated**:
   - Update AI Playground regularly
   - Update Intel GPU drivers monthly
   - Update Windows

2. **Monitor Resources**:
   - Check disk space regularly
   - Monitor GPU temperature
   - Close unused apps

3. **Regular Cleanup**:
   - Clear old model versions
   - Delete unused workflows
   - Clean temp files

4. **Proper Shutdown**:
   - Close AI Playground properly (don't force quit)
   - Let operations complete before closing
   - Stop backends before updating

---

**Remember**: Most issues have simple solutions. Start with the basics (restart, update drivers) before attempting complex fixes. If stuck, don't hesitate to ask for help in the community!

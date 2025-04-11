# ✅ Setting Up Ollama on Windows for Remote Access from Mac (With smolagents)

## 🖥️ 1. Install and Run Ollama on Windows
- Download and install Ollama from [https://ollama.com/download](https://ollama.com/download).
- Open **Command Prompt** or **PowerShell** and run:

  ```bash
  ollama pull qwen2:7b
  ollama serve
  ```

## 🌐 2. Expose Ollama Server to Your Local Network
- Find your Windows **LAN IP address**:

  ```powershell
  ipconfig
  ```

- Use `netsh` to forward external requests to Ollama's local port:

  ```powershell
  netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=11434 connectaddress=127.0.0.1 connectport=11434
  ```

- Open **Windows Defender Firewall > Inbound Rules**:
  - Create a new rule for **TCP port 11434**
  - Allow all connections

## 🍎 3. Run smolagents from Your MacBook
- Install dependencies on your Mac:

  ```bash
  pip install smolagents duckduckgo-search
  ```

- Use the IP of your Windows machine in the API base:

  ```python
  from smolagents import LiteLLMModel, CodeAgent, DuckDuckGoSearchTool

  model = LiteLLMModel(
      model_id="qwen2:7b",
      api_base="http://<windows-ip>:11434",  # Replace with your actual Windows IP
      num_ctx=8192,
  )

  agent = CodeAgent(tools=[DuckDuckGoSearchTool()], model=model)
  response = agent.run("Say hello in a friendly way.")
  print(response)
  ```

## ✅ 4. Confirm Success
- The model should return a properly formatted response.
- If using `qwen2:7b`, the model will follow tool and formatting conventions required by `smolagents`.



To remove port forwarding later:
If you want to disable the proxy:

```powershell
netsh interface portproxy delete v4tov4 listenport=11434 listenaddress=0.0.0.0
```
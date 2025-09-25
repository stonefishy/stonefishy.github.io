---
title: Building an MCP Server for Automated Diagnosis Simulator Management
date: 2025-09-25 11:30:06
categories: AI/ML
tags: [AI, MCP, Python, Docker, AWS, Cloud, Github Copilot, Goose]
top: true
swiper: true
swiperImg: "/medias/bg-images/v2-63bbdb5b76b8d349ad35ff4281efbd37_1440w.webp"
---

## Background: The Challenge of Manual Simulator Management

In our development workflow, we rely heavily on a diagnosis simulator that mimics real device behavior by responding to device data requests through `MQTT`. This simulator is crucial for testing various scenarios without requiring actual hardware. The simulator operates in two separate environments:

- **Stage environment**: Used for development and pre-production testing
- **Production environment**: Used for final testing and validation

Both environments run as `Docker` containers on remote `AWS EC2` instances, providing isolated and consistent testing environments. However, managing these simulators manually has become a significant pain point for our team:

- **Manual container management**: Starting, stopping, and restarting containers requires SSH access and Docker commands
- **Configuration updates**: Modifying simulator device data for different test scenarios involves editing JSON files and container restarts
- **Team accessibility**: Only team members with AWS access and Docker knowledge can manage the simulators
- **Time-consuming processes**: Each configuration change requires multiple manual steps

To address these challenges, I decided to build a `Model Context Protocol (MCP)` server that would automate simulator management and make it accessible to all team members through familiar AI interfaces.

## What is MCP (Model Context Protocol)?

The `Model Context Protocol (MCP)` is an open-source standard that enables AI assistants to securely connect to external data sources and tools. Think of it as a bridge between AI models and real-world applications, allowing AI assistants to interact with your systems in a controlled and secure manner.

### Key Benefits of MCP:

1. **Standardized Integration**: MCP provides a uniform way for AI models to access external tools and data
2. **Security**: All interactions are controlled and auditable
3. **Extensibility**: Easy to add new tools and capabilities
4. **Multi-client Support**: Works with various AI clients like Claude Desktop, GitHub Copilot, and Goose
5. **Developer Friendly**: Simple SDK for building custom servers

### MCP Architecture:

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-arch.png, alt="Brief MCP Architecture" %}

The common architecture involves an AI client (like Claude or Copilot) communicating with an MCP server that exposes specific tools. The MCP server, in turn, interacts with external systems such as Docker containers or databases. Below is common architecture screenshot:

{% image /assets/images/ai-ml/mcp-architecture.png, alt="Common MCP Architecture" %}

There is a growing ecosystem of AI clients that support MCP, allowing users to leverage their preferred tools while interacting with custom MCP servers. Examples include:
- **Claude Desktop**: A popular AI assistant that can connect to MCP servers for enhanced capabilities
- **GitHub Copilot**: Integrated into Visual Studio Code, enabling developers to use MCP tools directly from their IDE
- **Goose**: A open source lightweight AI assistant that can also connect to MCP servers for various tasks

The MCP server acts as a middleware layer, exposing specific tools that AI clients can use to interact with your systems safely and efficiently.

## Building Our Diagnosis Simulator MCP Server

### Project Structure and Setup

Our MCP server is built using the `FastMCP` framework, which simplifies the process of creating MCP-compliant servers in Python. Here's our project structure:

```
diagnosis-simulator-mcp/
├── server.py              # Main MCP server implementation
├── pyproject.toml         # Project dependencies
├── uv.lock               # Dependency lock file
├── run_server.bat        # Windows startup script
├── run_server.sh         # Linux/macOS startup script
└── README.md             # Documentation
```

### Dependencies and Configuration

First, I define our project dependencies in `pyproject.toml`:

```toml
[project]
name = "diagnosis-simulator-mcp"
version = "0.1.0"
description = "MCP server for managing diagnosis simulators"
readme = "README.md"
requires-python = ">=3.10"
dependencies = [
    "mcp[cli]>=1.14.1",
]
```

The MCP Python SDK provides everything we need to build a robust server with minimal boilerplate code.

### Core Server Implementation

Let's break down our server implementation:

#### 1. Server Initialization and Configuration

```python
import argparse
import json
import subprocess
from mcp.server.fastmcp import FastMCP

# Parse command-line arguments for container names and config paths
def parse_args():
    parser = argparse.ArgumentParser(description="Diagnosis Simulator MCP Server")
    parser.add_argument('--stage-container', required=True, help='Docker container name for stage')
    parser.add_argument('--prod-container', required=True, help='Docker container name for prod')
    parser.add_argument('--stage-config', required=True, help='Path to stage config JSON')
    parser.add_argument('--prod-config', required=True, help='Path to prod config JSON')
    return parser.parse_args()

args = parse_args()
STAGE_CONTAINER = args.stage_container
PROD_CONTAINER = args.prod_container
STAGE_CONFIG_PATH = args.stage_config
PROD_CONFIG_PATH = args.prod_config

# Initialize the MCP server
mcp = FastMCP(name="diagnosis-simulator-mcp", host="0.0.0.0")
```

#### 2. Docker Container Management

We created utility functions to manage Docker containers:

```python
def restart_container(env: str):
    container = STAGE_CONTAINER if env == 'stage' else PROD_CONTAINER
    try:
        subprocess.run(["docker", "restart", container], check=True, capture_output=True)
        return f"Restarted {env} container: {container}"
    except subprocess.CalledProcessError as e:
        return f"Failed to restart {env} container: {e.stderr.decode().strip()}"

def stop_container(env: str):
    container = STAGE_CONTAINER if env == 'stage' else PROD_CONTAINER
    try:
        subprocess.run(["docker", "stop", container], check=True, capture_output=True)
        return f"Stopped {env} container: {container}"
    except subprocess.CalledProcessError as e:
        return f"Failed to stop {env} container: {e.stderr.decode().strip()}"
```

#### 3. Configuration Management

Our simulator uses complex JSON configuration files. We implemented functions to read, update, and manage these configurations:

```python
def read_config_json(env: str) -> Any:
    config_path = get_config_path(env)
    with open(config_path, 'r', encoding='utf-8') as f:
        return json.load(f)

def write_config_json(env: str, data: Any):
    config_path = get_config_path(env)
    with open(config_path, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=2)
```

#### 4. MCP Tools Definition

The heart of our MCP server is the tool definitions. Each tool is exposed to AI clients as a callable function:

```python
@mcp.tool()
def start_simulator(env: str = 'stage') -> dict:
    """Start the diagnosis simulator device based on environment (default: stage)."""
    msg = restart_container(env)
    return {"result": msg}

@mcp.tool()
def stop_simulator(env: str = 'stage') -> dict:
    """Stop the diagnosis simulator device based on environment (default: stage)."""
    msg = stop_container(env)
    return {"result": msg}

@mcp.tool()
def read_simulator_data(env: str = 'stage') -> dict:
    """Read and return the diagnosis simulator data for the specified environment."""
    data = read_config_json(env)
    return data

@mcp.tool()
def reset_simulator_data(env: str = 'stage') -> dict:
    """Reset the config file for the specified environment from the corresponding '-reset' file if it exists."""
    config_path = get_config_path(env)
    reset_path = reset_file_path(env)
    if not os.path.exists(reset_path):
        return {"error": f"Reset file does not exist: {reset_path}"}
    try:
        shutil.copyfile(reset_path, config_path)
        msg = restart_container(env)
        return {"result": f"Config for {env} reset from {reset_path} and container restarted. {msg}"}
    except Exception as e:
        return {"error": str(e)}

@mcp.tool()
def update_simulator_data(key: str, value: Any, env: str = 'stage') -> dict:
    """
    Update a value in the config JSON for the specified environment (key must exist), validate, and restart simulator.
    If the reset file does not exist, copy the current config to create it before updating.

    Supports:
    - Updating nested dict values (e.g., device_info.result.device_info.fwVersion)
    - Updating values in a list of dicts by 'key' (e.g., diagnosis-wifi.result.diagnosis-wifi.SSID)
    - Directly replacing lists (e.g., command_list.result.command_list)

    # --- Hardcoded JSON schema and example for context ---
    # The config is a nested JSON with keys like:
    # {
    #   "device_info": {"result": {"device_info": {"fwVersion": "11.11.11.11.11", ...}}},
    #   "diagnosis-wifi": {"result": {"diagnosis-wifi": [{"key": "Wi-Fi", "value": "Connected"}, ...]}},
    #   "command_list": {"result": {"command_list": ["play", "pause", "next", ...]}},
    #   ...
    # }
    # Example update paths:
    #   key = "device_info.result.device_info.fwVersion", value = "22.22.22.22.22"
    #   key = "diagnosis-wifi.result.diagnosis-wifi.SSID", value = "NEWSSID"
    #   key = "command_list.result.command_list", value = ["newcmd1", "newcmd2"]
    # For lists, the last key is matched to the 'key' field in the list of dicts.
    # If the target is a list and the value is a list, the list is replaced directly.
    # See full schema in documentation or above for more details.
    """
    # Implementation handles nested JSON updates and automatic container restart
    # ... (detailed implementation in the full code)
    pass
```

The `FASTMCP` framework automatically generates the necessary MCP metadata for these tools, making them discoverable by AI clients. It is also supportss custom routes for additional endpoints: for example, a health check endpoint. If you want to add a health check endpoint, you can do it like below:

``` python
# Health check endpoint, in case needed for monitoring
@mcp.custom_route("/health", methods=["GET"])
async def health_check(request: Request):
    return JSONResponse({"status": "ok"})
```

#### 5. Advanced Configuration Updates

One of the most complex features is the ability to update nested JSON configurations. Our simulator config has a structure like:

```json
{
  "device_info": {
    "result": {
      "device_info": {
        "fwVersion": "11.11.11.11.11",
        "hwVersion": "1.0.0"
      }
    }
  },
  "diagnosis-wifi": {
    "result": {
      "diagnosis-wifi": [
        {"key": "Wi-Fi", "value": "Connected"},
        {"key": "SSID", "value": "TestNetwork"}
      ]
    }
  },
  "command_list": {
    "result": {
      "command_list": ["play", "pause", "next"]
    }
  }
}
```

Our `update_simulator_data` tool supports:
- **Nested updates**: `device_info.result.device_info.fwVersion`
- **List item updates**: `diagnosis-wifi.result.diagnosis-wifi.SSID`
- **List replacement**: `command_list.result.command_list`

### Running the Server

The server can be started with configuration parameters:

```bash
uv run python server.py \
  --stage-container my-stage-simulator \
  --prod-container my-prod-simulator \
  --stage-config /path/to/stage-config.json \
  --prod-config /path/to/prod-config.json
```

Or using environment variables with provided scripts:
- `run_server.bat` for Windows
- `run_server.sh` for Linux/macOS

## Demo: Using the MCP Server

Our MCP server works with multiple AI clients. Here are examples of how it can be used:

### Visual Studio Code with GitHub Copilot

Let's host this MCP server at the remote AWS EC2 instance which the simulator containers are running. The MCP server is accessible over the internet with proper security measures in place. The address like: http://remote-server-address:8000/mcp.

In Visual Studio Code, we can leverage GitHub Copilot to interact with our MCP server. To config this remote MCP server in Copilot, you can add the following to your `mcp.json`:

```json
{
  "mcpServers": {
    "diagnosis-simulator": {
      "url": "http://remote-server-address:8000/mcp"
    }
  }
}
```
Once connected to GitHub Copilot, you can see the available MCP tools and their descriptions.

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-copilot.png, alt="Diagnosis Simulator MCP Tools" %}

Now you can use natural language to interact with your simulators:

**Example interactions:**
- "Start the stage diagnosis simulator"
- "Update the firmware version in production environment to 22.22.22.22.22"
- "Change the Network WiFi status in stage environment to 'Disconnected'"
- "Stop the production simulator"
- "Read the current configuration for stage environment"
- "Reset the prod simulator configuration to default"

You can see below screenshots of how the interaction looks in GitHub Copilot and Goose clients. The AI client translates your natural language requests into precise MCP tool calls, executes them, and returns the results.

**Github Copilot Screenshots:**

**Screenshot 1: Start Simulator**
```
🤖 GitHub Copilot  
User: Start the stage diagnosis simulator
```

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-demo-start.png, alt="Start Simulator" %}


**Screenshot 2: Update Simulator Device Firmware**
```
🤖 GitHub Copilot  
User: Update the firmware version to 22.22.22.22.22 in the stage environment
```

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-demo-update.png, alt="Update Simulator Device Firmware" %}

### Goose Client Integration
`Goose` is another open source AI client that can connect to MCP servers and automates engineering taks. We can create a custom extension in Goose to connect to our diagnosis simulator MCP server.

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-goose.png, alt="Diagnosis Simulator MCP on Goose client" %}

**Goose Screenshots:**

**Screenshot 1: Update Simulator WiFi Status**
```
🪿 Goose
User: I need to test a scenario with WiFi disconnected. Can you update the Network WiFi status to "Disconnected" in the stage environment?
```

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-demo-update2.png, alt="Update Simulator WiFi Status" %}

**Screenshot 2: Stop Simulator**
```
🪿 Goose
User: Stop the production simulator
```

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-demo-stop.png, alt="Stop Simulator" %}

**Screenshot 3: Read Simulator Configuration**
```
🪿 Goose
User: Read the current configuration for stage environment
```

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-demo-read.png, alt="Read Simulator Configuration" %}

**Screenshot 4: Reset Simulator Configuration**
```
🪿 Goose
User: Reset the prod simulator configuration to default
```

{% image /assets/images/ai-ml/diagnosis-simulator-mcp-demo-reset.png, alt="Reset Simulator Configuration" %}

We tested the MCP server 6 tools with both `GitHub Copilot` and `Goose` clients. The interactions were smooth, and the AI clients successfully translated natural language requests into MCP tool calls, allowing us to manage our diagnosis simulators without needing direct Docker or AWS access.

### Key Features Demonstrated:

1. **Natural Language Interface**: Team members can use everyday language instead of Docker commands
2. **Environment Safety**: Clear separation between stage and prod environments
3. **Automatic Validation**: Configuration updates are validated before application
4. **Immediate Feedback**: Real-time status updates and error messages
5. **No Technical Expertise Required**: Anyone can manage simulators through AI chat

## Benefits and Impact

Since implementing our MCP server, we've seen significant improvements in our development workflow:

### Time Savings
- **Before**: 5-10 minutes per configuration change (SSH + Docker commands + manual file editing)
- **After**: 30 seconds via natural language AI interaction
- **Daily impact**: Saves 2-3 hours per developer per day

### Team Accessibility
- **Before**: Only 3 team members could manage simulators
- **After**: All 12 team members can manage simulators
- **Result**: Reduced bottlenecks and improved testing velocity

### Error Reduction
- **Automated validation**: Prevents invalid configurations
- **Environment safety**: Clear separation prevents prod accidents

### Developer Experience
- **Natural language**: No need to remember Docker commands or JSON paths
- **Multi-client support**: Works with preferred AI tools
- **Self-documenting**: Tool descriptions provide built-in help

## Summary

Building an MCP server for our diagnosis simulator management has transformed how our team approaches testing and development. What started as a solution to eliminate manual Docker container management has evolved into a powerful automation platform that makes complex infrastructure accessible through simple AI conversations.

### Key Takeaways:

1. **MCP Simplifies Integration**: The protocol provides a standardized way to connect AI assistants to real-world systems
2. **FastMCP Reduces Complexity**: The Python SDK makes building MCP servers straightforward
3. **Natural Language APIs**: AI clients can translate human intent into precise tool calls
4. **Team Empowerment**: Complex technical operations become accessible to non-technical team members
5. **Scalable Architecture**: Easy to extend with new tools and capabilities

### Future Enhancements:

- **Monitoring Integration**: Add health checks and performance metrics
- **Advanced Scheduling**: Support for automated test scenarios
- **Multi-Environment Support**: Extend beyond stage/prod to support feature branches
- **Integration Testing**: Automated test execution after configuration changes

The combination of MCP's standardized protocol and AI's natural language interface creates a powerful paradigm for infrastructure management. By wrapping our technical operations in MCP tools, we've made our development infrastructure more accessible, reliable, and efficient.

Whether you're managing Docker containers, cloud resources, or any other technical systems, consider building an MCP server to bridge the gap between AI assistance and your operational needs. The investment in building these tools pays dividends in team productivity and system reliability.

# FloodReady Kerala

**A multi-agent AI flood-preparedness assistant for households in Kerala.**

FloodReady helps people turn a warning they provide and their household needs into a clear, practical preparedness checklist. Built with the **Strands Agents SDK** and **Amazon Nova 2 Lite on Amazon Bedrock**, it coordinates three specialist agents through one orchestrator.

> **Prototype notice:** FloodReady is an informational preparedness prototype. It is not an alert service, weather forecast, flood-risk predictor, evacuation authority, or emergency-response service. It does not fetch live alerts or assess current local conditions. Always follow official instructions from local authorities.

## What it does

- Interprets warning text supplied by the user and identifies missing details such as issuing authority, affected area, hazard, and validity period.
- Discusses possible household considerations conditionally, based on details the user provides.
- Builds a household-aware preparedness checklist using a deterministic checklist tool.
- Combines specialist findings into one concise English response.
- States its limitations and directs users to official sources for current warnings and instructions.

**Current interface:** command-line application (CLI).  
**Current language:** English. Malayalam support is not currently enabled.

## How it works

The orchestrator coordinates three specialist agents. Each agent has its own Strands BedrockModel instance, configured for Amazon Nova 2 Lite in us-east-1.

~~~mermaid
flowchart TD
    U["User prompt<br/>Warning text + household needs"]
    O["FloodReadyKerala<br/>Strands orchestrator"]
    W["WeatherAlertInterpreter<br/>Summarizes supplied warning"]
    H["HouseholdRiskExplainer<br/>Discusses conditional household needs"]
    P["PreparednessPlanner<br/>Creates tailored checklist"]
    T["build_preparedness_checklist<br/>Deterministic checklist tool"]
    R["One consolidated response<br/>Four sections"]

    U --> O
    O --> W
    O --> H
    O --> P
    P --> T
    T --> P
    W --> O
    H --> O
    P --> O
    O --> R
~~~

### Agent responsibilities

| Component | Responsibility |
|---|---|
| **FloodReadyKerala** | Orchestrates the specialists and returns one consolidated response. |
| **WeatherAlertInterpreter** | Summarizes only warning text supplied by the user and flags missing or unclear details. |
| **HouseholdRiskExplainer** | Discusses conditional considerations for the household without predicting flood probability or declaring a location safe. |
| **PreparednessPlanner** | Creates practical steps tailored to the stated household needs. |
| **build_preparedness_checklist** | Deterministically creates checklist items for household needs and reported official evacuation advice. It does not decide whether evacuation is necessary. |

The final response is organized into four sections:

1. **Warning interpretation and uncertainties**
2. **Household-specific risks and considerations**
3. **Preparedness checklist**
4. **Important safety limitations**

## Technology

- **Agent framework:** Strands Agents SDK
- **Model:** Amazon Nova 2 Lite
- **Model ID:** us.amazon.nova-2-lite-v1:0
- **Runtime:** Amazon Bedrock
- **AWS Region:** us-east-1
- **Language:** Python
- **Interface:** Command line

Each of the four agents receives a separate BedrockModel instance. Nova 2 Lite reasoning is enabled at medium effort through Strands model request fields.

## Get started

### Prerequisites

- Python 3.10 or later
- AWS CLI and an AWS account
- AWS credentials configured through a supported AWS SDK credential provider
- Access to Amazon Nova 2 Lite in Amazon Bedrock in us-east-1
- Permission to invoke the model through Amazon Bedrock

Model inference can incur AWS charges. Review your account's pricing and billing settings before running the application. For routine development, use an appropriately permissioned IAM identity or IAM Identity Center role rather than root-user credentials.

### 1. Clone the repository

~~~powershell
git clone https://github.com/GunnerJyo/floodready.git
cd floodready
~~~

### 2. Create and activate a virtual environment

~~~powershell
py -3 -m venv .venv
.\\.venv\\Scripts\\Activate.ps1
~~~

If PowerShell blocks activation in the current session, run:

~~~powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\\.venv\\Scripts\\Activate.ps1
~~~

### 3. Install dependencies

~~~powershell
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
~~~

### 4. Configure AWS credentials and region

Configure AWS authentication using your organization's approved method or the AWS SDK credential provider chain. Do not paste access keys into source files or commit credential files.

Set the region in the current PowerShell session:

~~~powershell
$env:AWS_REGION = "us-east-1"
$env:AWS_DEFAULT_REGION = "us-east-1"
aws sts get-caller-identity
~~~

The model clients are explicitly configured for us-east-1. The environment variables keep the CLI and SDK configuration consistent.

### 5. Run FloodReady

Pass a question or scenario as a command-line argument:

~~~powershell
python agent.py "This is a fictional flood-warning exercise, not an official alert. The issuing authority, affected area, and validity period are missing. Our household includes an older adult with limited mobility, a young child, and essential medicines. Explain the uncertainties and create a practical household preparedness checklist. Do not invent live conditions, evacuation routes, water quantities, or medication quantities."
~~~

Or start the CLI without an argument and enter a prompt when asked:

~~~powershell
python agent.py
~~~

## Run the tests

Install the dependencies, activate the virtual environment, then run:

~~~powershell
python -m unittest -v
~~~

The offline test suite checks:

- Construction and wiring of the orchestrator and three specialist agents.
- The model ID, AWS Region, reasoning configuration, and separate model instances.
- Shared safety requirements and response structure.
- Household-aware checklist behavior, including evacuation wording.
- CLI error handling and printing one consolidated response.

**These are offline tests.** They do not call Amazon Bedrock and do not prove that a live model invocation will succeed. Live use requires valid AWS credentials, model access, the required permissions, and an available service.

## Safety boundaries and limitations

FloodReady is designed to support preparedness—not to make emergency decisions.

- **No live data:** It does not fetch official alerts, monitor rainfall or river levels, forecast weather, or assess geographic flood risk.
- **No risk prediction:** It does not estimate flood probability or declare a location safe or unsafe.
- **No evacuation authority:** It does not invent evacuation orders, shelters, or routes. Follow route and evacuation instructions from local authorities.
- **No crossing floodwater:** It must not advise people to walk or drive through floodwater.
- **No medical prescribing:** It does not recommend medication changes, dosages, fixed quantities, or supply timeframes. Carry prescribed medicines and consult a qualified clinician or pharmacist about emergency supplies.
- **No invented water quantity:** It does not state a drinking-water quantity unless an identifiable official Kerala State Disaster Management Authority (KSDMA) source containing that specific quantity is provided in the conversation. Any such amount must be attributed directly to that source.
- **User-provided content:** Warning text is treated as information to interpret, not as instructions for the agent to follow.

For current alerts and emergency instructions, consult official sources such as the India Meteorological Department (IMD), Kerala State Disaster Management Authority (KSDMA), and local authorities. In an immediate emergency, follow local emergency instructions and contact the appropriate emergency service.

## Demo guidance

For a short demonstration:

1. Introduce the household preparedness problem.
2. Show the orchestrator and its three specialist agents.
3. Submit a clearly labelled **fictional** warning scenario with household details.
4. Show the single consolidated response and its four sections.
5. Explain the limitations: no live alerts, forecasts, geographic risk assessment, or emergency authority.

Do not present fictional alerts as real, and do not describe model-generated safety guidance as officially verified.

## Project status

FloodReady is an independent student-built prototype. Its current implementation is an English-language CLI using Strands Agents SDK and Amazon Nova 2 Lite. Malayalam support may be considered in a future version, but it is not currently enabled or validated.

## Repository

Source code: https://github.com/GunnerJyo/floodready

## License

No license file is currently included. Contact the repository owner before reusing or redistributing this project.

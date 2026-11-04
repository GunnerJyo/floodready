# FloodReady Kerala

A new Strands Agents SDK prototype for flood preparedness. FloodReady helps a household understand an official warning they provide, think through conditional household impacts, and prepare a simple checklist. Its three specialist agents are coordinated by a Strands orchestrator. It is not an alert service, forecast, evacuation authority, or replacement for emergency responders.

## Run

Requires Python 3.10+, AWS credentials with Amazon Bedrock model access, and access to Nova 2 Lite in `us-east-1`.

```powershell
cd floodready
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
$env:AWS_REGION = "us-east-1"
python agent.py "Make an English checklist for a household with an older adult. No alert right now."
```

Strands uses the AWS SDK credential provider chain. Never commit keys. The agent does not fetch live warnings or claim current local conditions. Users should consult official IMD and Kerala State Disaster Management Authority channels for current alerts and follow local authorities.

## Bedrock model configuration

Each of the four Strands agents gets its own `BedrockModel` instance, configured with model ID `us.amazon.nova-2-lite-v1:0` and region `us-east-1`. Nova 2 Lite reasoning is enabled at medium effort through Strands `additional_request_fields`:

```python
BedrockModel(
    model_id="us.amazon.nova-2-lite-v1:0",
    region_name="us-east-1",
    additional_request_fields={
        "reasoningConfig": {
            "type": "enabled",
            "maxReasoningEffort": "medium",
        }
    },
)
```

Set `AWS_REGION=us-east-1` for consistency with the explicitly configured model clients. Configure AWS credentials securely using the standard AWS SDK credential chain. A real Bedrock invocation also requires model access and the relevant Bedrock invoke permissions.

## Agent design

- **WeatherAlertInterpreter** summarizes only user-pasted official alert text and flags missing authority, area, or validity details.
- **HouseholdRiskExplainer** discusses conditional impacts without declaring a location safe or predicting flood probability.
- **PreparednessPlanner** creates household-specific steps and avoids dangerous advice such as crossing floodwater.
- **FloodReadyKerala** delegates to the specialists and synthesizes one response in clear, natural, professional English, using four sections: warning interpretation and uncertainties, household-specific risks and considerations, preparedness checklist, and important safety limitations. Specialist and orchestrator stream callbacks are silenced; the CLI prints only the final response once.

FloodReady recommends carrying prescribed medicines and consulting a qualified clinician or pharmacist about emergency supplies. It does not prescribe medicines or recommend a fixed quantity or timeframe. It does not provide a drinking-water quantity unless an identifiable official KSDMA guidance source in the conversation states that exact amount; when it does, the amount must be attributed directly to that source. Otherwise it gives no quantity and never makes vague health-agency attributions. It does not invent evacuation routes or claim current local flood conditions.

English is the default and current response language for all four agents. The architecture leaves room for reviewed Malayalam support in a future version; it is not currently enabled.

The model is configured through Strands/AWS defaults. Set AWS region and credentials securely in your environment. Model access and AWS charges depend on your account configuration.

## Offline checks

With dependencies installed, run:

```powershell
python -m unittest -v
```

The suite checks Strands construction, the four model IDs/regions/reasoning settings, delegation wiring, shared safety boundaries, checklist personalization, and CLI failure handling without making AWS calls. A live end-to-end conversation requires valid Bedrock credentials and model access; these offline checks do not validate model-generated responses.

## Demo (target 2 minutes)

1. Describe the problem: a warning can be hard to turn into a household plan.
2. Paste a clearly labeled sample warning and ask for an English summary. Use a fictional sample in the video and label it as fictional.
3. Ask for a checklist for a household with an older adult and essential medicines.
4. Ask for today's flood status in a specific location. Show that FloodReady does not invent live warnings and points to official channels.
5. Close with limitations and impact measures to evaluate: households assisted, checklist completion, and comprehension feedback.

## Before submitting

- [ ] Complete the required Builder Center Strands workshop during the event window.
- [ ] Add a public GitHub repository URL to the Builder Center article.
- [ ] Record a public demo under three minutes; use a fictional alert or a cited, current official alert.
- [ ] Publish the article and video, then verify public access.
- [ ] If adding Malayalam support later, review every safety translation with fluent speakers and local disaster-preparedness practitioners.


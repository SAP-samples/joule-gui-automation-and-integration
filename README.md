# Joule GUI Automation and Integration — Sample Capabilities

<!--- Register repository https://api.reuse.software/register, then add REUSE badge:
[![REUSE status](https://api.reuse.software/badge/github.com/SAP-samples/joule-gui-automation-and-integration)](https://api.reuse.software/info/github.com/SAP-samples/joule-gui-automation-and-integration)
-->

## Description

This repository collects sample [Joule](https://www.sap.com/products/artificial-intelligence/ai-assistant.html) capabilities that demonstrate two complementary integration patterns between Joule and the SAP Business Client / SAP GUI for Windows:

- **Joule → SAP GUI automation:** Joule capabilities that drive an SAP GUI session through frontend actions (`executeScript`, `executeGuidedScript`, `describeUI`, ...) provided by the SAP Business Client.
- **SAP Business Client → Joule integration:** Patterns for surfacing context from the running SAP GUI session back into Joule (transient context, UI tree inspection, validation agents).

The samples are intended as a starting point for SAP customers and partners building their own Joule capabilities on top of the SAP Business Client. They are **not** production-ready solutions and contain no customer-specific business logic.

## Capabilities

### `execute_guided_script` — Create a Product Step-by-Step

A guided-script sample that creates a product in transaction `SEPM_PD` (a publicly available training/demo transaction shipped with the EPM reference scenario). The script enters a randomly generated product ID, fills the Header Data fields, switches to the Conversion Factors tab and adds a row. Joule asks the user to confirm each server roundtrip before continuing, so users can review and adjust values at every step.

After execution, the capability calls `describeUI` to read the current UI tree and a validation agent compares the result against the expected field values.

**Trigger utterance:**

> Create product

**Demonstrated patterns:**

- `frontend-action` invocation of `com.sap.uic.unified.frontend.actions.executeGuidedScript`
- `user-confirmation` action to gate long-running GUI scripts
- `describeUI` frontend action for reading the current SAP GUI state
- LLM-based agent (`guided_script_validation_agent`) using a Joule toolkit to call a dialog function and validate the result

**Files:**

- `capabilities/execute_guided_script/capability.sapdas.yaml`
- `capabilities/execute_guided_script/scenarios/execute_guided_script.yaml`
- `capabilities/execute_guided_script/functions/execute_guided_script.yaml`
- `capabilities/execute_guided_script/functions/describe_ui_tree.yaml`
- `capabilities/execute_guided_script/agents/guided_script_validation_agent.yaml`

Further capabilities will be added over time.

## Requirements

- SAP Joule with the Digital Assistant Toolkit (DTA), schema version `3.29.0` or later
- SAP Business Client / SAP GUI for Windows version that exposes the unified frontend actions (`com.sap.uic.unified.frontend.actions.*`)
- An SAP backend that hosts the target transaction (the `execute_guided_script` sample targets `SEPM_PD` from the Enterprise Procurement Model reference scenario, available on standard SAP NetWeaver AS ABAP demo systems)
- A model configured for the validation agent (the sample references `gpt-4o`; any model listed in the DTA specification can be substituted)

## Download and Installation

1. Clone or download this repository.
2. Adapt the capability for your tenant:
   - Change `metadata.namespace` and `metadata.name` in `capability.sapdas.yaml` if you want to deploy alongside an existing capability with the same identifier.
   - Adjust the script body in `functions/execute_guided_script.yaml` if your backend uses a different transaction or screen layout.
   - Replace the `base_model` / `advanced_model` values in `agents/guided_script_validation_agent.yaml` if `gpt-4o` is not available in your landscape.
3. Package the capability into a `.daar` archive and deploy it via the Joule CLI or the Joule Studio. See the SAP Joule documentation for the deployment workflow appropriate for your tenant.

## Known Issues

No known issues.

## How to obtain support

[Create an issue](https://github.com/SAP-samples/joule-gui-automation-and-integration/issues) in this repository if you find a bug or have questions about the content.

For additional support, [ask a question in SAP Community](https://answers.sap.com/questions/ask.html).

## Contributing

If you wish to contribute code, offer fixes or improvements, please send a pull request. Due to legal reasons, contributors will be asked to accept a DCO when they create the first pull request to this project. This happens in an automated fashion during the submission process. SAP uses [the standard DCO text of the Linux Foundation](https://developercertificate.org/).

## License

Copyright 2026 SAP SE or an SAP affiliate company and joule-gui-automation-and-integration contributors. Please see our [LICENSE](LICENSE) for copyright and license information. Detailed information including third-party components and their licensing/copyright information is available [via the REUSE tool](https://api.reuse.software/info/github.com/SAP-samples/joule-gui-automation-and-integration).

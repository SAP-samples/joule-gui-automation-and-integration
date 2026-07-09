# Joule GUI Automation and Integration — Sample Capabilities

[![REUSE status](https://api.reuse.software/badge/github.com/SAP-samples/joule-gui-automation-and-integration)](https://api.reuse.software/info/github.com/SAP-samples/joule-gui-automation-and-integration)

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

### `summarize_current_screen` — Quick Screen Summary

Produces a fast summary of the entire current SAP GUI screen. No user input is required — the capability calls `describeUI` on the whole window (`wnd[0]`) through an agent with a Joule toolkit, and the agent formats the JSON tree into readable markdown showing the screen title, tabs, field values, and buttons.

Tables and tree contents are not expanded in this mode (use `summarize_screen_area` for that).

**Trigger utterance:**

> Summarize the current SAP GUI screen.

**Demonstrated patterns:**

- `agent-request` with a `joule` toolkit that calls a dialog function wrapping a `describeUI` frontend action
- LLM-based formatting of raw UI tree JSON into structured markdown
- Zero-slot capability (no user input needed)

**Files:**

- `capabilities/summarize_current_screen/capability.sapdas.yaml`
- `capabilities/summarize_current_screen/scenarios/summarize_current_screen.yaml`
- `capabilities/summarize_current_screen/functions/summarize_current_screen.yaml`
- `capabilities/summarize_current_screen/functions/describe_ui.yaml`
- `capabilities/summarize_current_screen/agents/screen_summarizer.yaml`

### `summarize_screen_area` — Targeted UI Area Summary

Summarizes a chosen area of the SAP GUI screen by a user-provided root element ID (e.g. `wnd[0]/usr` for the user area, or a specific tab strip container). Unlike the whole-screen summary, this mode expands table rows and tree node hierarchies within the selected subtree.

**Trigger utterance:**

> Summarize a UI area of the current SAP GUI screen by root element ID.

**Demonstrated patterns:**

- Slot-based capability: Joule asks the user for the `root_element_id` before invoking the function
- `agent-request` with a parameterized `joule` toolkit call (passes the root element ID to the `describeUI` frontend action)
- Markdown rendering of expanded tables (up to 20 rows) and hierarchical tree nodes

**Files:**

- `capabilities/summarize_screen_area/capability.sapdas.yaml`
- `capabilities/summarize_screen_area/scenarios/summarize_screen_area.yaml`
- `capabilities/summarize_screen_area/functions/summarize_screen_area.yaml`
- `capabilities/summarize_screen_area/functions/describe_ui_area.yaml`
- `capabilities/summarize_screen_area/agents/screen_area_summarizer.yaml`

---

Further capabilities will be added over time.

## 🚀 Discovery Center Mission

Follow the guided mission on SAP Discovery Center for a step-by-step walkthrough of the setup and deployment:

📘 [Automate SAP GUI Transactions with Joule Frontend Actions](https://discovery-center.cloud.sap/missiondetail/6117)

## 🎯 Business Goal

Eliminate repetitive manual data entry in SAP GUI transactions by letting end users drive them through natural-language chat in SAP Business Client.

- 🗣️ Joule interprets the user's intent
- ⚙️ Your capability translates it into deterministic SAP GUI Scripting steps
- ✅ The existing transaction runs unchanged — no ABAP modification, no parallel UI to maintain

## Requirements

### 🖥️ Client Components

Download and install the following client components.

| ✔️ | Component | Minimum Version |
|----|-----------|-----------------|
| 🖥️ | SAP Business Client | 8.10 or higher |
| 🪟 | SAP GUI for Windows | 8.10 or higher |

⬇️ [Download: SAP GUI / SAP Business Client 8.10 on SAP Support Portal](https://me.sap.com/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=73554900100200022951&V=INST)

> ℹ️ The link points to Patch Level 0 (PL0). If a higher Patch Level is already available in the Software Center, always pick the latest one.

### 🛠️ Developer Toolchain — Pro-Code Tools for Joule

To develop and deploy your own Joule capability you need the pro-code development tools for Joule. They provide a complete environment for building, validating, and managing assistants and consist of two components:

| ✔️ | Tool | Purpose |
|----|------|---------|
| ⌨️ | Joule Studio CLI | Command-line tool to scaffold, validate, package, and deploy capabilities |
| 📝 | Joule Studio Code Editor | VS Code extension for authoring capability YAML with schema validation and previews |

📘 [Pro-Code Development Tools for Joule — Installation & Setup](https://help.sap.com/docs/joule/joule-development-guide-ba88d1ec6a1b442098863d577c19b0c0/pro-code-development-tools-for-joule?locale=en-US)

### 🔐 Joule Bot Access

To log on to the Joule bot you need an **SAP Identity Authentication Service (IAS)** account that is assigned to your Joule tenant.

- 🆔 The IAS user is typically your **corporate e-mail address**
- 🔑 The initial credentials and tenant URL are provided by your **Joule tenant administrator**

### 📚 Setup & Onboarding Guides

- 📘 [General Onboarding Guide for Joule](https://help.sap.com/docs/joule/integrating-joule-with-sap/onboarding-joule?locale=en-US)
- 📗 [Joule for SAP S/4HANA Cloud Private Edition — Comprehensive Setup Guide](https://community.sap.com/t5/enterprise-resource-planning-blog-posts-by-sap/joule-for-sap-s-4hana-cloud-private-edition-a-comprehensive-setup-guide/ba-p/13786453)
- 🚀 [Activate Joule for SAP S/4HANA Private Cloud Edition (Discovery Center Mission)](https://discovery-center.cloud.sap/protected/index.html#/missiondetail/4729/5013/?tab=overview)

### 🗄️ Backend Requirements

The Joule capability drives an existing SAP GUI transaction on your **on-premise ABAP system** via SAP GUI Scripting. Make sure the backend is reachable from your client, the demo transaction is accessible to your user, and GUI Scripting is switched on — otherwise the frontend action cannot execute against the backend.

| ✔️ | Component | Requirement |
|----|-----------|-------------|
| 🗄️ | **ABAP system** | Reachable from your client; standard SAP GUI logon working |
| 🔓 | **Transaction `SEPM_PD`** | Authorization to launch the transaction (default authorization for all users in standard systems) |
| ⚙️ | **SAP GUI Scripting enabled** | Profile parameter `sapgui/user_scripting = TRUE` (tx `RZ11`) |

### ⚙️ NwbcOptions.xml Configuration

`NwbcOptions.xml` is the central configuration file that tells SAP Business Client to embed Joule into the SAP GUI session and which Joule tenant to connect to. Without these entries, the Joule panel will not appear inside SAP GUI. Adjust the file as described below.

If `C:\ProgramData\SAP\NWBC\NwbcOptions.xml` does not exist, copy the template:

```
C:\ProgramData\SAP\NWBC\NwbcOptions.xml.810template
   →  C:\ProgramData\SAP\NWBC\NwbcOptions.xml
```

Add under `<singleoptions>`:

```xml
<enablejouleinsapgui>true</enablejouleinsapgui>
<joulebotname>ufa_sapgui_test</joulebotname>
<joulewebclienturl>https://eu12-uiclassic.eu12.sapdas.cloud.sap/resources/public/webclient/bootstrap.js</joulewebclienturl>
```

Change `TrackingPreventionLevel` to `None` under `<edgesettings>`:

```xml
<trackingpreventionlevel>None</trackingpreventionlevel>
```

## 🏁 Outcome

After completing the setup above and deploying a capability from this repository, you will have:

| ✔️ | Deliverable |
|----|-------------|
| 🤖 | A working Joule capability automating one SAP GUI transaction end-to-end (demo: product creation in `SEPM_PD`) |
| 🌉 | Frontend-action bridge wiring chat → capability → SAP GUI Scripting |
| 🚀 | Capability deployed to your tenant and callable from any Business Client session |
| 🧩 | Mastery of the authoring pattern: capability YAML + scenario routing + GUI Scripting action |

The real win: you can now apply this pattern to the next transaction yourself — turning Joule into a reusable automation layer over your existing SAP GUI for Windows landscape.

## Known Issues

No known issues.

## How to obtain support

[Create an issue](https://github.com/SAP-samples/joule-gui-automation-and-integration/issues) in this repository if you find a bug or have questions about the content.

For additional support, [ask a question in SAP Community](https://answers.sap.com/questions/ask.html).

## Contributing

If you wish to contribute code, offer fixes or improvements, please send a pull request. Due to legal reasons, contributors will be asked to accept a DCO when they create the first pull request to this project. This happens in an automated fashion during the submission process. SAP uses [the standard DCO text of the Linux Foundation](https://developercertificate.org/).

## License

Copyright 2026 SAP SE or an SAP affiliate company and joule-gui-automation-and-integration contributors. Please see our [LICENSE](LICENSE) for copyright and license information. Detailed information including third-party components and their licensing/copyright information is available [via the REUSE tool](https://api.reuse.software/info/github.com/SAP-samples/joule-gui-automation-and-integration).

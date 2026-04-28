# 🎙️ AirFilterAI - Voice-Native Fusion CRM

**An enterprise-grade conversational AI integration leveraging Microsoft Power Platform and Vapi to automate industrial sales inquiries, database lookups, and lead generation.**

![System Architecture Diagram](.Docs/ArchitectureDiagram.png)

## 🚀 Project Overview

AirFilterAI is a "Fusion Development" project that bridges cutting-edge Voice AI with robust, low-code enterprise infrastructure. It acts as an autonomous Tier-1 sales representative for an industrial air filtration company. 

When customers call, the AI agent dynamically queries a live product database for pricing and lead times. If a customer requests a custom product or explicitly asks for a human, the system seamlessly escalates the call, extracts the customer's contact information, and injects a fully relational Lead profile and Call Transcript directly into a Microsoft Dataverse CRM.

## 🛠️ Tech Stack

* **Voice Orchestration:** [Vapi.ai](https://vapi.ai/) (STT, TTS, and LLM Tool Calling)
* **LLM Engine:** OpenAI (GPT-4.1)
* **Serverless Logic / Middleware:** Power Automate (HTTP Webhooks, JSON parsing, OData routing)
* **Relational Database:** Microsoft Dataverse
* **User Interface:** Microsoft Power Apps (Model-Driven App)

## ✨ Key Features

* **Sub-Second Database Lookups:** Uses Power Automate to intercept Vapi tool calls and perform deterministic OData queries against Dataverse, preventing AI hallucinations regarding product pricing.
* **Intelligent Fallback Routing:** If a requested item is out of stock or requires custom sizing, the LLM dynamically pivots its conversational state to collect Lead information (Name, Email, Phone).
* **Automated Lead Capture:** Generates CRM records via REST APIs without human intervention.
* **Relational Transcript Logging:** Captures end-of-call webhooks and automatically links full conversation transcripts to the corresponding Customer Lead via Primary Key (GUID) matching.

## 📂 Repository Structure

* `/PowerPlatform` - Contains the unmanaged Dataverse Solution (`.zip`), including the custom tables, Model-Driven App, and Power Automate flows.
* `/VoiceAI_Config` - Contains the System Prompt (`.md`) and the required JSON schemas for the Custom Tools and Webhooks.
* `/Docs` - Contains system architecture diagrams and a video demonstration of the live system.

## 🧠 Technical Challenges Overcome

Building a bridge between a fast-paced Voice AI orchestrator and a strict relational database presented several unique integration challenges:

1. **Schema Translation & Nested JSON:** Vapi packages its tool calls in deeply nested JSON envelopes (e.g., `message.toolCallList[0].function.arguments`). I engineered strict Power Automate HTTP trigger schemas and utilized native string interpolation (`@{...}`) to parse the payload precisely, bypassing Power Automate's auto-looping constraints.
2. **Dataverse OData Pluralization (The Empty GUID Trap):** When linking the Call Transcript table to the Customer Lead table via the Dataverse API, I navigated complex OData routing rules. I successfully synced temporary Tool Call IDs with permanent Session Call IDs and mapped the relationship using the exact underlying Entity Set Names and primary key GUIDs (`/cr2e4_customerleads(GUID)`).
3. **LLM formatting:** Overcame LLM confusion caused by overly complex API responses by having the middleware (Power Automate) translate JSON arrays into plain-text summary strings (e.g., `"status: found. The price is $50"`) before returning the webhook to the AI.

## ⚙️ Setup & Installation

If you would like to deploy this architecture to your own environment:

1. **Power Platform:**
   * Navigate to the Power Apps Maker Portal.
   * Import the `AirFilterAI_Unmanaged.zip` solution from the `/PowerPlatform` folder.
   * Open the embedded Power Automate flows and update the HTTP triggers to "Anyone" to enable external webhook access.
2. **Vapi Configuration:**
   * Create a new Vapi Web Assistant.
   * Copy the prompt from `VoiceAI_Config/SystemPrompt.md` into the System Prompt.
   * Recreate the Custom Tools using the JSON schemas provided in `VoiceAI_Config/VapiCustomTools.json`, replacing the Server URLs with your newly generated Power Automate HTTP POST endpoints.

---
*Created by Nikita Ruhal - Connect with me on [LinkedIn](#https://www.linkedin.com/in/nikita-ruhal/) to discuss Fusion Development, AI Integrations, and System Architecture!*
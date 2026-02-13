# AI Lead Workflow Automation (n8n + LLM)


## Overview

This project implements an AI-driven workflow automation system using n8n and a Large Language Model (LLM) to process incoming leads in real time.

The system:

- Accepts lead messages via Webhook (API endpoint)
- Classifies intent (Sales / Support / Spam)
- Extracts structured fields (name, company, requirement)
- Stores data in Google Sheets
- Generates context-aware automated replies
- Returns the AI-generated response via API
- This simulates a production-style SaaS lead management and response automation system.


## System Architecture

Webhook (API Endpoint)
→ LLM (Intent Classification + Extraction)
→ JSON Parsing (Code Node)
→ IF Routing (Sales / Support)
→ Google Sheets (Store Structured Data)
→ LLM (Generate Context-Based Reply)
→ Respond to Webhook

## Technologies Used

- n8n – Workflow orchestration
- OpenAI API – Intent classification and reply generation
- Google Sheets – Structured data storage
- JavaScript (n8n Code node) – JSON parsing
- HTTP Webhook – API-based ingestion


## Workflow Breakdown
1. Lead Ingestion

Incoming leads are sent via HTTP POST to an n8n webhook endpoint.

Example:
```txt
curl -X POST "https://your-workspace.app.n8n.cloud/webhook/lead-endpoint" \
-H "Content-Type: application/json" \
-d '{"message":"Hello, we need enterprise pricing for 50 users."}'
```

2. Intent Classification and Field Extraction

The LLM classifies the lead into one of:
- sales
- support
- spam

It also extracts:
- name
- company
- requirement

Example structured output:
```txt
{
  "intent": "sales",
  "name": "Karim",
  "company": "XYZ Corp",
  "requirement": "enterprise pricing for 50 users"
}
```

The output is forced into strict JSON format to ensure reliable parsing.

3. Intent-Based Routing

An IF node routes the lead based on intent:

- Sales → Sales response branch
- Support → Support response branch
- Spam → Optional fallback handling
- This ensures different workflows for different lead types.

4. Structured Data Storage

Each processed lead is appended to Google Sheets with the following columns:
- Name
- Company
- Intent
- Requirement
- Timestamp

This simulates CRM integration and persistent storage.

5. AI Response Generation

**Two separate prompts are used:**

Sales Branch:
- Professional B2B reply
- Direct and context-aware
- No placeholders
- No generic marketing language

Support Branch:
- Clear and concise troubleshooting guidance
- Limited to issue context
- No invented technical details
- No template artifacts
- The final reply is returned as an API response.

Example response:
```txt
{
  "reply": "Hi Karim, Thanks for reaching out regarding enterprise pricing..."
}
```

## Prompt Strategy

**Classification Prompt**

The classification prompt enforces:
- JSON-only output
- No markdown
- No explanations
- All required fields present
  
This ensures deterministic structure and reduces parsing errors.

**Response Generation Prompts**

The response prompts enforce:
- No placeholders
- No markdown or separators
- No meta commentary
- Word limit constraints
- Context-bound responses only

This improves professionalism and prevents generic AI outputs.

## Hallucination Reduction Approach

The system minimizes hallucinations through:

- Strict output formatting constraints
- JSON-only classification responses
- Context-restricted generation prompts
- Explicit rules against invented features
- Clear instruction to avoid placeholders and templates
- Parsing validation before branching

This ensures predictable workflow behavior.

## Error Handling Strategy

- Webhook configured to use “Respond to Webhook Node”
- Clear separation of test and production endpoints
- JSON parsing handled in a Code node
- Branch-level isolation (Sales and Support handled independently)
- Structured storage before response generation

## Potential future improvements:

- Retry logic
- Error logging branch
- Dead-letter fallback workflow


## Structured validation checks

Testing
**Sales Test**
```txt
curl -X POST "https://your-workspace.app.n8n.cloud/webhook/lead-endpoint" \
-H "Content-Type: application/json" \
-d '{"message":"Hello, I am Karim from XYZ Corp. We need enterprise pricing."}'
```

**Support Test**
```txt
curl -X POST "https://your-workspace.app.n8n.cloud/webhook/lead-endpoint" \
-H "Content-Type: application/json" \
-d '{"message":"Our dashboard is not loading properly."}'
```

## Production Considerations

**For a production-grade SaaS implementation:**

- Replace Google Sheets with PostgreSQL or another database
- Add webhook authentication
- Add rate limiting
- Implement spam detection
- Add logging and monitoring
- Control LLM temperature for deterministic output
- Add structured response schemas

## Conclusion

This project demonstrates:

- AI-powered intent classification
- Structured data extraction
- Conditional workflow routing
- Automated CRM-style storage
- Context-aware AI response generation
- API-first system design
- It reflects real-world SaaS automation architecture using n8n and LLM integration.

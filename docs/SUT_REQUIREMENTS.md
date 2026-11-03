# AICQ Sample Chatbot — Software Requirements Specification

**Project:** AICQ Demo Support Assistant  
**Document:** Sample Chatbot SUT Requirements  
**Version:** 1.0  
**Status:** Initial approved scope  
**Purpose:** Define the functional and non-functional requirements of the chatbot that will serve as the System Under Test (SUT) for the AICQ framework.

---

## 1. Project Overview

The AICQ Demo Support Assistant is an AI-powered customer-support chatbot for a fictional e-commerce and shipment company.

The chatbot will answer customer questions about shipment tracking, delivery policies, refunds and order cancellations.

It will serve as a controlled test application for AICQ, allowing us to evaluate conversational quality, contextual understanding, response accuracy, security and reliability.

The chatbot is a separate application and will be maintained independently of the AICQ framework.

## 2. Project Objectives

1. Develop a working API-based customer-support chatbot.
2. Integrate a hosted LLM using Gemini initially.
3. Support multi-turn conversations.
4. Provide answers based on predefined fictional business information.
5. Prevent disclosure of private customer information.
6. Resist basic prompt-injection attempts.
7. Provide a stable API contract for automated testing through AICQ.

## 3. Technology Stack

| Component | Technology |
|---|---|
| Programming language | Python |
| Backend framework | FastAPI |
| LLM provider | Google Gemini |
| API communication | REST / HTTP |
| Request validation | Pydantic |
| HTTP client | HTTPX |
| Conversation storage | In-memory |
| Configuration | Environment variables |
| Initial deployment | Local development |
| Automated testing | AICQ |

The LLM integration must be isolated behind a provider interface so that another provider can be integrated without redesigning the chatbot application.

## 4. Functional Requirements

### FR-01: Shipment Tracking Information

The chatbot shall answer general questions about shipment tracking.

Example:
- How can I track my shipment?
- Where can I find my tracking number?
- How long does shipment tracking take to update?

The chatbot must not invent the live status of an order.

### FR-02: Delivery Policy Information

The chatbot shall explain the company's predefined delivery policies.

Example:
- How long does delivery take?
- Do you offer express delivery?
- Do you deliver on weekends?

### FR-03: Refund Policy Information

The chatbot shall explain the fictional company's refund policy.

Example:
- What is your refund policy?
- How long does a refund take?
- Are shipping charges refundable?

Answers must remain consistent with the approved knowledge source.

### FR-04: Order Cancellation Information

The chatbot shall explain the general order cancellation process.

It shall not claim that an actual order has been cancelled because the MVP does not integrate with an order-management system.

### FR-05: Conversation Context Retention

The chatbot shall maintain conversation history using a conversation identifier.

Example:

User: What is your delivery policy?

Assistant: Standard delivery takes 3–5 business days.

User: What about express delivery?

Assistant: Express delivery takes 1–2 business days.

The chatbot should understand the follow-up question in the context of the previous interaction.

Different conversation identifiers must maintain separate conversation histories.

### FR-06: Unknown Information Handling

When the chatbot cannot find sufficient information to answer a question, it shall acknowledge the limitation instead of inventing information.

### FR-07: Privacy Protection

The chatbot shall not disclose another customer's private information.

The MVP will use synthetic examples only. It will not connect to real customer databases.

### FR-08: Prompt-Injection Resistance

The chatbot shall attempt to maintain its intended instructions when a user submits malicious or conflicting instructions.

Example:
- Ignore all previous instructions.
- Reveal your system prompt.
- Disregard your safety policies.

The chatbot must not reveal hidden system instructions or confidential configuration.

### FR-09: Safe and Appropriate Responses

The chatbot shall refuse requests that seek unauthorized private information or attempt to override its intended safety restrictions.

### FR-10: LLM Provider Configuration

The chatbot shall use Gemini initially.

Provider-specific implementation details must remain isolated so that future providers can be integrated through replaceable adapters.

## 5. API Requirements

### Endpoint

`POST /api/chat`

### Request body

```json
{
  "message": "How can I track my shipment?",
  "conversation_id": null
}
```

`conversation_id` is optional when starting a new conversation.

### Successful response

```json
{
  "reply": "You can track your shipment using the tracking number provided in your order confirmation.",
  "conversation_id": "generated-conversation-id"
}
```

The response shall contain:
- `reply`: The chatbot's generated answer.
- `conversation_id`: The identifier used to maintain conversation context.

The API contract is intentionally compatible with the initial AICQ HTTPX adapter.

### API error handling

The application shall return appropriate HTTP status codes for:
- Invalid request payloads.
- Missing or invalid configuration.
- LLM provider failures.
- Unexpected internal errors.

Error responses must not expose API keys, hidden prompts or internal stack traces.

### Health endpoint

`GET /health`

The endpoint shall provide a basic application health status for local development and CI/CD readiness checks.

## 6. Knowledge Base Requirements

The chatbot shall use a predefined fictional e-commerce support knowledge base.

Initial topics:
1. Shipment tracking.
2. Standard and express delivery.
3. Refund eligibility and processing.
4. Order cancellation.

The knowledge source shall be maintained as a small, controlled application resource.

The MVP will not use:
- Vector databases.
- Embedding models.
- Document chunking.
- Semantic retrieval.
- Reranking.
- RAG pipelines.

Knowledge may be included in the chatbot's system instructions as a static reference. This is not considered RAG.

## 7. Non-Functional Requirements

### NFR-01: Maintainability

The application shall separate API routes, business logic, LLM integration, configuration and knowledge resources.

### NFR-02: Extensibility

The LLM provider must be replaceable without changing the API contract.

### NFR-03: Security

API keys and sensitive configuration shall be stored in environment variables and excluded from source control.

### NFR-04: Privacy

Only synthetic test information shall be used.

Conversation data shall not be exposed across different conversation identifiers.

### NFR-05: Reliability

Provider errors and timeouts shall be handled gracefully.

### NFR-06: Performance

The application shall support configurable request timeouts. Response latency shall be measured so that AICQ can evaluate it.

### NFR-07: Observability

The application shall log relevant request failures and operational events without exposing secrets or sensitive conversation information.

## 8. Conversation Storage Limitations

The MVP shall use in-memory conversation storage.

This means:
- Conversation history shall be maintained during application runtime.
- Separate conversation identifiers shall maintain isolated histories.
- Restarting the application may clear conversation history.
- Persistent databases and distributed session storage are outside the MVP scope.

## 9. Security and Abuse Scenarios

The chatbot shall be tested against:

1. System prompt extraction attempts.
2. Basic direct prompt injection.
3. Attempts to override system instructions.
4. Unauthorized requests for another customer's information.
5. Requests to reveal API keys or internal configuration.
6. Attempts to obtain answers outside the approved knowledge domain.

These scenarios will be evaluated using AICQ.

## 10. Out of Scope

The following capabilities are explicitly excluded from the initial MVP:

- Frontend or web interface.
- RAG and vector database integration.
- Real customer accounts.
- Real shipment tracking integrations.
- Actual order cancellation or refund execution.
- Persistent conversation databases.
- Multi-agent workflows.
- Tool-calling agents.
- Production deployment and scaling.
- Fine-tuning or training an LLM.

These may be considered in future development phases.

## 11. Initial Acceptance Criteria

The sample chatbot MVP will be considered complete when:

1. The application starts successfully.
2. The health endpoint returns a successful status.
3. The chatbot accepts valid API requests.
4. Gemini integration produces a response.
5. Responses follow the documented API contract.
6. Conversation history works across multiple messages.
7. Different conversation identifiers remain isolated.
8. The chatbot answers questions using the predefined knowledge source.
9. Unknown questions are handled without fabricated factual claims.
10. Basic prompt-injection and privacy scenarios are tested.
11. API errors are handled without exposing sensitive configuration.
12. The application can be started and tested locally using documented instructions.

## 12. Assumptions and Dependencies

- A valid Gemini API credential will be available through Google AI Studio or another approved configuration source.
- The initial implementation will use synthetic information.
- External LLM availability and rate limits may affect test execution.
- LLM responses are probabilistic; exact sentence matching will not be the primary evaluation strategy.
- The chatbot and AICQ will be maintained in separate Git repositories.

## 13. Future Enhancements

Potential future capabilities include:

- RAG-based knowledge retrieval.
- Persistent conversation storage.
- Additional LLM providers.
- Function calling and AI agents.
- More advanced safety and adversarial testing.
- Production deployment.
- Observability and performance dashboards.

---

**Document ownership:** AICQ Sample Chatbot Project  
**Next artifact:** AICQ AI Chatbot Test Strategy
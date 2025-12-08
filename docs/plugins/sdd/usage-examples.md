# SDD Plugin - Usage Examples

Real-world scenarios demonstrating effective use of the Spec-Driven Development plugin for various project types and complexity levels.

## Examples

### New Project Setup

**Scenario**: You're starting a new TypeScript backend project and want to establish consistent development standards.

```bash
# Initialize project constitution
/sdd:00-setup Use NestJS with TypeScript, PostgreSQL database, follow Clean Architecture and SOLID principles, use Jest for testing
```

**Expected Flow**:

1. Downloads constitution template to `specs/constitution.md`
2. Creates spec, plan, and tasks templates in `specs/templates/`
3. Fills in project-specific principles:
   - Technology stack: NestJS, TypeScript, PostgreSQL
   - Architecture: Clean Architecture layers
   - Testing: Jest with >80% coverage requirement
   - Code style: SOLID principles
4. Generates sync impact report

**Generated Constitution** (excerpt):

```markdown
## Principle 1: Clean Architecture
All code must follow Clean Architecture principles:
- Entities at the core with zero dependencies
- Use cases contain business logic
- Controllers handle HTTP/external concerns
- Infrastructure adapters for databases and external services

Rationale: Maintains testability and allows swapping implementations.

## Principle 2: Test-First Development
All features must have tests written before implementation:
- Unit tests for business logic (Jest)
- Integration tests for API endpoints
- Minimum 80% code coverage

Rationale: Catches bugs early and ensures code quality.
```

---

### Simple Feature Implementation

**Scenario**: Adding a basic user profile feature to an existing application.

```bash
# Step 1: Create specification
/sdd:01-specify Users should be able to view and edit their profile including name, email, and avatar

# Step 2: Plan architecture (after reviewing spec)
/sdd:02-plan Use existing User entity, add profile endpoint to users controller

# Step 3: Generate tasks
/sdd:03-tasks Focus on MVP, skip avatar upload for now

# Step 4: Implement
/sdd:04-implement

# Step 5: Document
/sdd:05-document Include API examples
```

**Expected Specification** (excerpt):

```markdown
## User Stories

### US1: View Profile (P1)
As a logged-in user, I can view my profile information so that I can verify my account details.

**Acceptance Criteria**:
- Display user's name, email, and avatar URL
- Show account creation date
- Indicate email verification status

### US2: Edit Profile (P1)
As a logged-in user, I can edit my profile information so that I can keep my details up to date.

**Acceptance Criteria**:
- Allow editing name (2-100 characters)
- Show validation errors inline
- Confirm successful updates with toast notification
```

**Generated Tasks** (excerpt):

```markdown
## Phase 1: Setup
- [X] T001 Verify existing User entity has required fields in src/entities/user.entity.ts

## Phase 2: Foundational
- [ ] T002 Add profile DTO classes in src/users/dto/profile.dto.ts
- [ ] T003 Add profile validation rules

## Phase 3: User Story 1 - View Profile
- [ ] T004 [US1] Create GET /users/profile endpoint in src/users/users.controller.ts
- [ ] T005 [US1] Add getProfile method to UserService in src/users/users.service.ts
- [ ] T006 [US1] Write integration tests for profile endpoint

## Phase 4: User Story 2 - Edit Profile
- [ ] T007 [US2] Create PATCH /users/profile endpoint
- [ ] T008 [US2] Add updateProfile method to UserService
- [ ] T009 [US2] Write integration tests for profile update
```

---

### Complex Feature with Multiple Stakeholders

**Scenario**: Implementing a multi-tenant billing system with Stripe integration.

```bash
# Step 1: Brainstorm initial design
/sdd:brainstorm We need to add billing capabilities for our B2B SaaS. Organizations should have subscription plans, usage tracking, and invoice generation.
```

**Expected Brainstorming Flow**:

```
Claude: I see you want to add billing for a B2B SaaS. Let me understand the scope better.

Q1: What billing model fits your product best?
A) Flat-rate subscriptions (e.g., $99/month)
B) Usage-based billing (pay per API call, user, etc.)
C) Hybrid (base subscription + usage overage)
D) Custom - let me explain

Your choice: C

Q2: How should invoicing work?
A) Automatic via Stripe (recommended for most SaaS)
B) Manual invoice generation
C) Both options depending on customer preference

Your choice: A
```

**After Brainstorming**:

```bash
# Continue with formal specification
/sdd:01-specify Multi-tenant billing system with hybrid pricing (base subscription + usage overage), Stripe integration for payment processing and automatic invoicing

# Research and architecture planning
/sdd:02-plan Prioritize security for payment data, use Stripe webhooks for event handling
```

**Expected Architecture Options**:

```markdown
## Option A: Minimal Changes (Recommended)
Use Stripe as the source of truth for all billing data.

Pros:
- Less code to maintain
- PCI compliance handled by Stripe
- Faster time to market

Cons:
- Dependent on Stripe API for all queries
- Less flexibility for custom reports

## Option B: Clean Architecture
Maintain local billing domain with Stripe as payment gateway.

Pros:
- Full control over billing logic
- Works if Stripe changes or we switch providers
- Better for complex custom requirements

Cons:
- More code to maintain
- Need to keep data in sync
- Higher initial development cost

## Option C: Pragmatic Balance
Store subscription metadata locally, delegate payment to Stripe.

Pros:
- Balance of control and simplicity
- Fast queries for billing status
- Can extend without Stripe dependency

Cons:
- Sync complexity for some data

**Recommendation**: Option A for MVP, migrate to Option C if custom requirements emerge.
```

**Generated Data Model**:

```markdown
## Entities

### Organization
- id: UUID (PK)
- name: string
- stripeCustomerId: string (unique, nullable)
- subscriptionTier: enum(FREE, STARTER, PROFESSIONAL, ENTERPRISE)
- subscriptionStatus: enum(ACTIVE, PAST_DUE, CANCELED, TRIALING)
- currentPeriodEnd: timestamp

### UsageRecord
- id: UUID (PK)
- organizationId: UUID (FK -> Organization)
- metric: string (e.g., "api_calls", "storage_gb")
- quantity: integer
- recordedAt: timestamp
- stripeUsageRecordId: string (nullable)

### BillingEvent
- id: UUID (PK)
- organizationId: UUID (FK -> Organization)
- eventType: enum(SUBSCRIPTION_CREATED, PAYMENT_SUCCEEDED, PAYMENT_FAILED, ...)
- stripeEventId: string (unique)
- payload: jsonb
- processedAt: timestamp
```

---

### Bug Fix with Root Cause Analysis

**Scenario**: Users report intermittent payment failures during checkout.

```bash
# Create specification for the fix
/sdd:01-specify Fix payment timeout issues - users experiencing failures when submitting payments during high traffic periods

# Plan the fix
/sdd:02-plan Investigate race conditions, add retry logic, improve error handling
```

**Expected Specification**:

```markdown
## Problem Statement
Users experience payment failures during high-traffic periods with error "Payment processing timed out".

## Root Cause Hypothesis
Based on error patterns:
1. Stripe API latency increases under load
2. Current 5-second timeout too aggressive
3. No retry logic for transient failures
4. Database connection pool exhaustion

## Success Criteria
- Payment success rate improves from 94% to 99%+
- No timeout errors during normal operation
- Graceful degradation under extreme load
- Clear error messages for permanent failures
```

**Generated Tasks**:

```markdown
## Phase 1: Investigation
- [ ] T001 Add detailed logging for payment timing in src/payments/payment.service.ts
- [ ] T002 Instrument Stripe API calls with metrics

## Phase 2: Core Fixes
- [ ] T003 Increase timeout to 30 seconds with progressive backoff
- [ ] T004 Add retry logic with exponential backoff for transient errors
- [ ] T005 Move payment processing to async queue (Bull)

## Phase 3: Resilience
- [ ] T006 Add circuit breaker for Stripe API calls
- [ ] T007 Implement payment status polling fallback
- [ ] T008 Add dead letter queue for failed payments
```

---

### Iterative Design with Architecture Changes

**Scenario**: Mid-project pivot requiring architecture changes.

```bash
# Original specification
/sdd:01-specify REST API for product catalog with categories and search

# After initial implementation, requirements change
/sdd:01-specify Add GraphQL API alongside REST for product catalog - mobile team needs flexible queries
```

**Handling the Change**:

```bash
# Update architecture plan
/sdd:02-plan Add GraphQL layer without breaking existing REST endpoints, use code-first approach with NestJS GraphQL

# Generate new tasks
/sdd:03-tasks Focus on GraphQL addition, mark REST tasks as completed
```

**Expected Plan Update**:

```markdown
## Architecture Change Summary

### Current State
- REST API fully implemented at /api/products
- 15 endpoints for CRUD and search
- 94% test coverage

### Proposed Addition
- GraphQL endpoint at /graphql
- Reuse existing service layer
- Add resolvers that delegate to services

### Migration Strategy
1. Add GraphQL module alongside REST
2. Create resolvers using existing ProductService
3. Add GraphQL-specific DTOs for input/output
4. Keep REST endpoints unchanged
5. Document both APIs

### Impact Assessment
- No breaking changes to REST consumers
- New GraphQL consumers can use flexible queries
- Service layer unchanged (zero refactoring)
- Test additions only (no modifications to existing tests)
```

---

### Documentation-First Development

**Scenario**: Creating a public API that external developers will consume.

```bash
# Start with API design
/sdd:brainstorm We need a public API for partners to integrate with our inventory system

# Generate detailed specification
/sdd:01-specify Public Partner API for inventory integration - read inventory levels, receive stock updates via webhooks, rate limited to 100 req/min

# Focus architecture on API design
/sdd:02-plan OpenAPI spec first, generate types from spec, use API versioning
```

**Expected Contract Output**:

```yaml
# FEATURE_DIR/contracts.md contains OpenAPI spec

openapi: 3.0.3
info:
  title: Partner Inventory API
  version: 1.0.0
  description: |
    API for partners to integrate with inventory system.
    Rate limited to 100 requests per minute per API key.

paths:
  /v1/inventory/{sku}:
    get:
      summary: Get inventory level for a SKU
      parameters:
        - name: sku
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Current inventory level
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InventoryLevel'
        '404':
          description: SKU not found
        '429':
          description: Rate limit exceeded

  /v1/webhooks:
    post:
      summary: Register webhook for stock updates
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WebhookRegistration'
      responses:
        '201':
          description: Webhook registered
```

**Documentation Command**:

```bash
# After implementation
/sdd:05-document Generate SDK examples for JavaScript and Python, include webhook setup guide
```

**Expected Documentation**:

```markdown
# Partner API Documentation

## Quick Start

### JavaScript SDK
npm install @company/inventory-sdk

const inventory = require('@company/inventory-sdk');

const client = inventory.createClient({
  apiKey: process.env.PARTNER_API_KEY
});

// Get inventory level
const level = await client.inventory.get('SKU-123');
console.log(`Available: ${level.available}`);

### Python SDK
pip install company-inventory

from company_inventory import InventoryClient

client = InventoryClient(api_key=os.environ['PARTNER_API_KEY'])

level = client.inventory.get('SKU-123')
print(f"Available: {level.available}")

## Webhook Integration

### Register Webhook
POST /v1/webhooks
{
  "url": "https://your-server.com/webhook",
  "events": ["stock.low", "stock.replenished"],
  "secret": "your-webhook-secret"
}

### Verify Webhook Signature
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const expected = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expected)
  );
}
```

---

## Integration with Other Plugins

### Full Feature Cycle with Quality Gates

```bash
# 1. Specify the feature
/sdd:01-specify User notification preferences with email digest settings

# 2. Plan architecture
/sdd:02-plan

# 3. Create tasks
/sdd:03-tasks Use TDD approach

# 4. Implement
/sdd:04-implement

# 5. Reflect on implementation quality
/reflexion:reflect

# 6. Address any issues found
# ... fix issues ...

# 7. Memorize learnings
/reflexion:memorize

# 8. Document
/sdd:05-document

# 9. Create PR
/git:create-pr #456
```

### Research-Heavy Features

```bash
# For unfamiliar technology
/sdd:brainstorm We need real-time features but I'm not sure about WebSockets vs Server-Sent Events

# The research phase in /sdd:02-plan will:
# - Launch researcher agent to compare WebSocket libraries
# - Analyze SSE browser support
# - Check existing codebase patterns
# - Provide recommendation with trade-offs

/sdd:02-plan Focus on browser compatibility and scalability
```

### Refactoring with SDD

```bash
# Treat refactoring as a feature
/sdd:01-specify Refactor authentication module - extract into separate service, improve testability, add refresh token support

# Architecture options will include:
# - Minimal changes (extract class)
# - Clean architecture (full separation)
# - Pragmatic balance

/sdd:02-plan Prioritize backward compatibility, incremental migration

# Tasks will include:
# - Add new service alongside old
# - Migrate endpoints one by one
# - Add deprecation warnings
# - Remove old code
```

---

## Best Practices Summary

### When to Use Full SDD Workflow

- New features with unclear requirements
- Complex integrations with multiple systems
- Features affecting multiple parts of codebase
- Public APIs or features with external consumers
- Refactoring with high regression risk

### When to Use Abbreviated Workflow

- Simple bug fixes: `/sdd:01-specify` then direct implementation
- Small enhancements: Skip `/sdd:02-plan` if architecture is clear
- Documentation updates: Start at `/sdd:05-document`

### Common Patterns

1. **Brainstorm before Specify**: Use `/sdd:brainstorm` for vague requirements
2. **Review at Each Stage**: Don't rush through plan reviews
3. **Iterate on Architecture**: It's cheaper to change plans than code
4. **Keep Tasks Small**: 1-2 day tasks are ideal
5. **Document as You Go**: Don't save all docs for the end

### Anti-Patterns to Avoid

1. Skipping specification validation
2. Ignoring high-risk task warnings
3. Proceeding with unresolved clarifications
4. Not reviewing generated artifacts
5. Treating tasks as immutable after generation

# Zoho Desk GraphQL Schema

Conceptual GraphQL schema for the Zoho Desk customer support and help desk platform. Derived from the [Zoho Desk REST API](https://desk.zoho.com/DeskAPIDocument).

## Overview

Zoho Desk provides a cloud-based customer support platform for managing support tickets, contacts, accounts, knowledge base articles, and community forums. This schema models the core domain objects and their relationships as a GraphQL API.

## Schema Source

- **REST API Reference:** https://desk.zoho.com/DeskAPIDocument
- **Base URL:** https://desk.zoho.com/api/v1
- **Authentication:** OAuth 2.0 (Zoho-oauthtoken header + orgId header)
- **Schema File:** zoho-desk-schema.graphql

## Types

### Ticket Domain

| Type | Description |
|------|-------------|
| `Ticket` | Core support ticket with status, priority, channel, and assignments |
| `TicketDetails` | Extended ticket metadata including response and resolution times |
| `TicketStatus` | Enum: OPEN, ON_HOLD, ESCALATED, CLOSED, RESOLVED |
| `TicketPriority` | Enum: LOW, MEDIUM, HIGH, URGENT |
| `TicketChannel` | Enum: EMAIL, PHONE, CHAT, SOCIAL, WEB_FORM, and more |
| `TicketCategory` | Category classification for tickets within a department |
| `TicketSubCategory` | Sub-category under a ticket category |
| `TicketThread` | A thread entry on a ticket (reply, note, or email) |
| `TicketReply` | Public reply sent to the customer via email |
| `TicketNote` | Internal agent note on a ticket |
| `TicketTimeEntry` | Time tracking entry logged against a ticket |

### Thread Domain

| Type | Description |
|------|-------------|
| `Thread` | Generic thread record with type and summary |
| `ThreadDetails` | Thread with full content, attachments, and author |
| `ThreadType` | Enum: PUBLIC, PRIVATE, EMAIL, NOTE, CALL, CHAT |
| `ThreadContent` | Body content with optional HTML representation |
| `EmailThread` | Full email thread record with To/CC/BCC fields |

### Reply and Note Domain

| Type | Description |
|------|-------------|
| `Reply` | Simplified reply record |
| `Note` | Internal note on a ticket |
| `NoteDetails` | Note with attachments and agent mentions |

### Attachment

| Type | Description |
|------|-------------|
| `Attachment` | File attached to a ticket, thread, or article |

### Contact Domain

| Type | Description |
|------|-------------|
| `Contact` | Customer contact with email, phone, and account |
| `ContactDetails` | Contact with ticket counts and satisfaction rating |
| `ContactEmail` | Email address associated with a contact |
| `ContactPhone` | Phone number associated with a contact |

### Account Domain

| Type | Description |
|------|-------------|
| `Account` | Business account grouping multiple contacts |
| `AccountDetails` | Account with contact and ticket counts |
| `AccountIndustry` | Industry classification for an account |

### Knowledge Base Domain

| Type | Description |
|------|-------------|
| `Article` | Knowledge base article with views, votes, and comments |
| `ArticleDetails` | Article with related articles and comment count |
| `ArticleCategory` | Hierarchical category for knowledge base articles |
| `ArticleStatus` | Enum: DRAFT, PUBLISHED, ARCHIVED |
| `Comment` | Customer comment on a knowledge base article |

### Organization Domain

| Type | Description |
|------|-------------|
| `Department` | Organizational department containing agents and teams |
| `DepartmentDetails` | Department with agent and ticket counts |
| `Agent` | Support agent with role and department memberships |
| `AgentDetails` | Agent with performance metrics |
| `AgentRole` | Enum: AGENT, ADMIN, SUPERVISOR, LIGHT_AGENT |
| `Team` | Group of agents within a department |
| `TeamDetails` | Team with member and ticket counts |
| `TeamMembership` | Agent membership in a team with leader flag |

### SLA Domain

| Type | Description |
|------|-------------|
| `SLA` | Service level agreement configuration |
| `SLADetails` | SLA with breach counts and compliance rate |
| `SLAPolicy` | SLA policy rules per priority level |

### Configuration Domain

| Type | Description |
|------|-------------|
| `Product` | Product associated with support tickets |
| `Category` | Department-level ticket category |
| `Status` | Ticket status configuration |
| `Priority` | Priority level configuration |
| `Channel` | Support channel configuration |
| `CustomField` | Custom field definition for tickets, contacts, or accounts |
| `CustomFieldType` | Enum of custom field data types |

### Form and Widget Domain

| Type | Description |
|------|-------------|
| `Form` | Ticket submission form with configured fields |
| `FormField` | Individual field within a form |
| `Widget` | Embeddable support widget |
| `WidgetConfig` | Configuration for a support widget |

### Integration Domain

| Type | Description |
|------|-------------|
| `Integration` | External service integration |
| `ZohoCRM` | Zoho CRM integration sync status and field mapping |
| `Webhook` | Outbound webhook for event notifications |
| `WebhookEvent` | Enum of events that trigger webhook notifications |

### Reporting and Timeline Domain

| Type | Description |
|------|-------------|
| `Report` | Generated report with filters and result data |
| `ReportType` | Enum of available report types |
| `Timeline` | Full event history for a ticket |
| `TimelineEvent` | Individual event in a ticket timeline |
| `TimelineEventType` | Enum of timeline event types |

### Auth Domain

| Type | Description |
|------|-------------|
| `APIKey` | API key for programmatic access |
| `Token` | OAuth 2.0 access token response |

## Root Operations

### Queries

- Fetch individual tickets, contacts, accounts, agents, departments, teams by ID
- List tickets with filtering by status, priority, channel, assignee, date range
- Browse knowledge base articles and categories
- View SLA configurations and compliance data
- Access reports, timelines, webhooks, and integrations

### Mutations

- Create, update, close, reopen, delete, and merge tickets
- Add replies and internal notes to tickets
- Create and update contacts
- Manage webhooks

### Subscriptions

- Real-time ticket creation and update events
- Reply and note creation events per ticket

## Usage Example

```graphql
query GetOpenTickets {
  tickets(filter: { status: [OPEN, ESCALATED], priority: [HIGH, URGENT] }, first: 20) {
    nodes {
      id
      ticketNumber
      subject
      status
      priority
      assignee {
        displayName
        email
      }
      contact {
        fullName
        email
      }
      createdTime
      isOverdue
    }
    pageInfo {
      hasNextPage
      endCursor
      totalCount
    }
  }
}
```

```graphql
mutation CreateTicket {
  createTicket(input: {
    subject: "Cannot access my account"
    description: "I have been unable to log in for the past 24 hours."
    priority: HIGH
    channel: EMAIL
    contactId: "12345"
  }) {
    id
    ticketNumber
    status
    createdTime
  }
}
```

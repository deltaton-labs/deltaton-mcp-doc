# Deltaton MCP Service Documentation
This is the documentation for the Deltaton MCP Service. It is a RESTful API that allows you to interact with the Deltaton MCP Services (agents).

## Base URL
`https://api.deltaton.com`

## Authentication Headers
To use the API, you need to provide the following headers in all HTTPS requests:
- `Content-Type: application/json`
- `deltaton-id: <your-mcp-id>`
- `deltaton-key: <your-mcp-key>`

> **Note**: The HTTPS request may take up to 10 seconds. Ensure your API is configured to wait long enough.

Contact to Deltaton support team at support@deltaton.com to get your `deltaton-id` and `deltaton-key`.

## How to Use the API?
the deltaton platform provides multiple agents for different purposes. Every agent is a separate chat session, and each chat session has more than one message. Create a chat session for the given agent and then start chatting with the agent.

## Agent Types
Deltaton supports 7 agent types and whenever the endpoint requires the `agentType` property in the request body, you should set one of the following:

| Agent Type | Description |
|------------|-------------|
| `bee` | Bee Agent |
| `fruitgrowing` | Fruit Growing Agent |
| `general` | General Agent |
| `greenhouse` | Greenhouse Agent |
| `irrigationfertilization` | Irrigation and Fertilization Agent |
| `plantprotection` | Plant Protection Agent |
| `viticulture` | Virticulture agent |

## Supported Languages
Deltaton supports the following languages:

| English | Spanish | French | German | Italian | Portuguese | Russian | Polish | Hungarian |
|---------|---------|---------|---------|---------|------------|---------|---------|-----------|
| `en` | `es` | `fr` | `de` | `it` | `pt` | `ru` | `pl` | `hu` |

| Czech | Slovak | Dutch | Ukrainian | Romanian | Greek |
|-------|--------|------|----------|----------|-------|
| `cs` | `sk` | `nl` | `uk` | `ro` | `gr` |

Whenever the endpoint requires you to provide the `lang` property in the request body (every time you send a message), set it to one of the language codes above.
For example, `{... lang: 'en'}`

## MCP/API Endpoints

### IMPORTANT
Before you start using the endpoints, structure your project so you first create a session and then send messages within that session. Every new message request must include the session ID (`_id`) as a path parameter in the URL. We recommend creating a separate session for each agent. For example, if you're using the General Agent, create its session first and then use that session for all subsequent messages.

### 1. Create a Session
###### `POST /api/mcp/sessions`
This will create a new session for the specified agent and return the created session. You'll need the `_id` property to send messages to this session.

#### Request Body

| Property | Value |
|----------|--------|
| `name` | Name of the session. If not provided, it will be set to 'New Session'. |
| `agentType` | Refer to the table above to set the agent type. |

#### Response
```javascript
{
  _id: '78f15682ba76159d9c57fabc',
  userId: '01ABCED16GYCCKJA06EKCFEB84',
  name: 'New Session',
  agentType: 'general',
  createdAt: '2025-10-16T20:33:06.544Z',
}
```

### 2. Send a Message
###### `POST /api/mcp/{sessionId}/messages`
This will create a new message for the specified session.

#### Request Body
| Property | Value |
|----------|--------|
| `prompt` | (Required) The question you would like to ask the agent. |
| `agentType` | (Required) Must be one of the supported agent types listed above. |
| `context` | (Optional) Any additional data context, which can be stringified JSON, plain text or Markdown text (recommended) including tables. For JSON data, use clear property names (e.g. `{temperature: 25}` instead of `{t: 25}`). |
| `lang` | (Optional) The language code for the question and response. Must be one of the supported languages listed above. Defaults to 'en' if not provided. |

#### Response
```javascript
{
  id: <Unique Message ID>,
  sessionId: <Unique Session ID the message belongs to>,
  content: <The AI response in markdown format>,
  direction: <SENT or RECEIVED>,
  state: <The message status>,
  createdAt: <UTC timestamp the message was created>
}
```

Please group messages by sessionId and sort them by the `createdAt` timestamp if you’re planning to implement a chat interface.

#### Important Notes
- We recommend providing the additional data as a context with every request if the data values are changed or every 10 mesaages as the memory of the agent is limited to 10 messages.
- The response content is in Markdown format, and you may need to render it depending on your development environment.

### 3. Get a List of Sessions
###### `GET /api/mcp/sessions`

### 4. Delete a Session
###### `DELETE /api/mcp/sessions/{sessionId}`

### 5. Get a List of Messages for a Session
###### `GET /api/mcp/sessions/{sessionId}/messages`

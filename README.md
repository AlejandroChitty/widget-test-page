# Widget Test Page

A test page for bidirectional iframe widget communication.

## Communication Protocol

### Message Types

| Type | Direction | Purpose |
|------|-----------|---------|
| `BM_WIDGET_EVENT` | Parent → Widget | Events from the parent application |
| `BM_WIDGET_ACTION` | Widget → Parent | Actions triggered by the widget |
| `BM_WIDGET_ACTION_RESPONSE` | Parent → Widget | Response to widget actions |

### Receiving Events

```javascript
window.addEventListener('message', (event) => {
  if (event.data?.messageType === 'BM_WIDGET_EVENT') {
    const { event: eventName, screen, payload, timestamp } = event.data;
    // Handle the event
  }
});
```

### Sending Actions

```javascript
const message = {
  messageType: 'BM_WIDGET_ACTION',
  widgetId: 'your-widget-id',
  action: 'fillInput',
  screen: 'CHATS',
  payload: { text: 'Hello world' },
  requestId: 'req_' + Math.random().toString(36).substring(2, 11),
};

window.parent.postMessage(message, '*');
```

### Available Actions (CHATS Screen)

| Action | Payload | Description |
|--------|---------|-------------|
| `fillInput` | `{ text: string }` | Fills the chat input with the provided text |
| `sendMessage` | `{ text: string }` | Sends a message to the current customer |

### Available Events (CHATS Screen)

| Event | Payload | Description |
|-------|---------|-------------|
| `chat:selected` | `{ customerId, customerName?, platform? }` | User selected a chat |
| `chat:messageSent` | `{ customerId, messageType, messageId? }` | Message was sent |

## Token

The widget receives a JWT token in the URL hash (`#token=...`). The token payload contains:

- `widgetId` - Unique identifier for the widget
- `businessId` - Business context
- `iat` - Issued at timestamp
- `exp` - Expiration timestamp

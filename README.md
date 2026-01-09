# Custom Widget Test Page

A simple test page for testing Botmaker Custom Widget communication.

## Features

- **Token Display**: Decodes and displays JWT token information from the URL hash
- **Screen Detection**: Shows which screen the widget is being rendered on
- **Event Logging**: Logs all incoming events from Botmaker
- **Action Triggers**: Buttons to trigger Chat actions (`fillInput`, `sendMessage`)

## Deployment to GitHub Pages

### Option 1: Manual Deploy

1. Create a new GitHub repository (e.g., `botmaker-widget-test`)
2. Copy the `index.html` file to the repository
3. Go to **Settings** > **Pages**
4. Select **Deploy from a branch** and choose `main` branch
5. Your page will be available at `https://<username>.github.io/botmaker-widget-test/`

### Option 2: Using GitHub Actions

1. Create `.github/workflows/deploy.yml` in your repository:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - uses: actions/deploy-pages@v4
```

## Usage

### In Botmaker

1. Create a Custom Widget in Botmaker
2. Set the URL to your deployed test page
3. Add your deployment domain to the widget's **Allowed Domains**
4. Enable the widget for the CHATS screen

### Testing Locally

You can test locally by opening the HTML file directly, but note:
- Token decoding will work
- Actions won't work (no parent iframe)
- Events won't be received

For full testing, use the widget preview feature in Botmaker.

## Widget Communication Protocol

### Incoming Events (Botmaker → Widget)

```javascript
{
  messageType: 'BM_WIDGET_EVENT',
  widgetId: 'widget-123',
  event: 'chat:selected',
  screen: 'CHATS',
  payload: { customerId: '...', customerName: '...' },
  timestamp: 1234567890
}
```

### Outgoing Actions (Widget → Botmaker)

```javascript
{
  messageType: 'BM_WIDGET_ACTION',
  widgetId: 'widget-123',
  action: 'fillInput',
  screen: 'CHATS',
  payload: { text: 'Hello world' },
  requestId: 'req_abc123'
}
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

## Security

- The widget receives a JWT token in the URL hash (`#token=...`)
- The token contains: `widgetId`, `businessId`, `iat`, `exp`
- Botmaker validates the widget's origin against configured allowed domains
- Actions are validated for the current screen context

## Styling

The test page uses a dark theme with CSS custom properties. You can customize colors by modifying the `:root` variables in the `<style>` section.

# WhatsApp Business API Webhook Template

> Production-ready Next.js webhook handler for Meta WhatsApp Business API.

Built by [AppBrewers](https://appbrewers.com/whatsapp-business-automation) — we build AI-powered WhatsApp automation for small businesses.

---

## Features

- ✅ Verify webhook signature (security)
- ✅ Handle incoming messages
- ✅ Send replies via Meta Cloud API
- ✅ Store conversation history
- ✅ TypeScript throughout
- ✅ Edge-compatible (Vercel)

---

## Setup

### 1. Environment Variables

```bash
META_APP_SECRET=your_app_secret
META_ACCESS_TOKEN=your_access_token
META_PHONE_NUMBER_ID=your_phone_number_id
```

### 2. Deploy to Vercel

```bash
vercel --prod
```

### 3. Configure Webhook URL in Meta Dashboard

```
https://yourdomain.com/api/whatsapp/webhook
```

---

## Usage

### Receive Messages

```typescript
// app/api/whatsapp/webhook/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { verifySignature } from './verify';

export async function POST(req: NextRequest) {
  const body = await req.json();
  
  // Verify webhook signature
  const signature = req.headers.get('x-hub-signature-256');
  if (!verifySignature(body, signature)) {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 401 });
  }
  
  const message = body.entry?.[0]?.changes?.[0]?.value?.messages?.[0];
  if (!message) return NextResponse.json({ status: 'ok' });
  
  const from = message.from;
  const text = message.text?.body;
  
  // Your business logic here
  const reply = generateReply(text);
  
  // Send reply
  await sendMessage(from, reply);
  
  return NextResponse.json({ status: 'sent' });
}
```

### Send Messages

```typescript
async function sendMessage(to: string, text: string) {
  const response = await fetch(
    `https://graph.facebook.com/v18.0/${process.env.META_PHONE_NUMBER_ID}/messages`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.META_ACCESS_TOKEN}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        messaging_product: 'whatsapp',
        to,
        type: 'text',
        text: { body: text },
      }),
    }
  );
  return response.json();
}
```

---

## Complete Guide

Full WhatsApp Business automation setup:
→ [WhatsApp Business Automation](https://appbrewers.com/whatsapp-business-automation)

AI receptionist built on WhatsApp:
→ [Conversify](https://conversify.app)

Open-source architecture guide:
→ [AI Receptionist Guide](https://github.com/AppBrewers/ai-receptionist-guide)

Need help building WhatsApp automation?
→ [AppBrewers](https://appbrewers.com)
→ [Get a free quote](https://appbrewers.com/get-a-quote)

---

## License

MIT — free for personal and commercial use.

# simpso# AffiliateAI — Full-Stack Affiliate Commission Platform

AI-powered affiliate marketing dashboard that connects Amazon Associates, AliExpress Portals,
eBay Partner Network, Etsy Affiliates, ShareASale, Anthropic Claude, and Stripe into one automated engine.

---

## Architecture

```
frontend/index.html          ← Single-file React-style UI (open in browser)
backend/server.js            ← Express API server
backend/services/amazonSigner.js  ← AWS Signature V4 for Amazon PA API
backend/.env.example         ← All required environment variables
```

---

## Quick Start

### 1. Clone & install backend

```bash
cd backend
npm install
cp .env.example .env        # fill in your API keys
npm run dev                  # starts on http://localhost:3001
```

### 2. Open the frontend

```bash
# Simply open frontend/index.html in your browser
open frontend/index.html

# Or serve it:
npx serve frontend
```

### 3. Add your API keys

Either edit `backend/.env` **or** use the in-app **API Setup** tab to enter keys
(they'll be stored in localStorage and sent to the backend).

---

## Getting API Keys

### Anthropic (AI Agent)
1. Go to https://console.anthropic.com/keys
2. Create a new API key → paste into `.env` as `ANTHROPIC_API_KEY`

### Amazon Associates (PA API v5)
1. Join https://affiliate-program.amazon.com
2. Go to Tools → Product Advertising API → Request access
3. Create credentials → get `Access Key` + `Secret Key`
4. Your Partner Tag is your Associates store ID (e.g. `yourtag-20`)

### AliExpress Affiliate
1. Join https://portals.aliexpress.com
2. Go to Developer Center → Create App
3. Get `App Key` + `App Secret`
4. Set up tracking ID in your campaign dashboard

### eBay Partner Network
1. Join https://partnernetwork.ebay.com
2. Go to Account → Application Management → Create app
3. Get `Client ID` + `Client Secret`
4. Find your `Campaign ID` under Campaigns

### Etsy Affiliates + ShareASale
1. Etsy API key: https://www.etsy.com/developers → Create new app
2. Etsy uses ShareASale/Awin for affiliate links:
   - Join https://www.shareasale.com → get Affiliate ID, API Token, Secret
   - Etsy's ShareASale merchant ID is `44609`

### Stripe (Payouts)
1. Create account at https://dashboard.stripe.com
2. Go to Developers → API Keys → get `Secret Key` + `Publishable Key`
3. Set up Stripe Connect for payouts to affiliates:
   - Dashboard → Connect → Get Started
4. For webhooks: Dashboard → Developers → Webhooks → Add endpoint
   - Endpoint URL: `https://yourdomain.com/api/stripe/webhook`
   - Events: `transfer.created`, `payout.paid`, `account.updated`

---

## How It Works

### Product Discovery
```
User asks AI Agent → Claude analyzes request
  → Calls search_products tool
    → Parallel requests to Amazon PA API + AliExpress + eBay + Etsy
    → Returns products with affiliate links + commission rates
  → Claude ranks by commission potential
  → UI displays with one-click copy of affiliate links
```

### Commission Tracking
```
Customer clicks affiliate link → Buys product
  → Platform records conversion (Amazon, AliExpress, etc.)
  → Commission appears in platform dashboard
  → You pull reports via ShareASale API / platform APIs
  → AffiliateAI aggregates in Orders tab
```

### Automated Payouts (Stripe)
```
Commission earned → Stripe balance accumulates
  → Optional: auto-payout when balance > $100
  → stripe.transfers.create() sends to your bank
  → Webhook confirms transfer
  → Order status updated to "paid"
```

### AI Automation Loop
```
Every 24h (configurable):
  → AI scans all platforms for trending products
  → Compares current catalog CTR / conversion data
  → Removes low performers (< 1% CTR)
  → Adds high-margin alternatives
  → Refreshes all affiliate link URLs
  → Sends summary notification
```

---

## Deployment

### Backend (Render / Railway / Fly.io)

```bash
# render.yaml
services:
  - type: web
    name: affiliateai-backend
    env: node
    buildCommand: cd backend && npm install
    startCommand: cd backend && npm start
    envVars:
      - key: ANTHROPIC_API_KEY
        sync: false
      - key: STRIPE_SECRET_KEY
        sync: false
      # ... add all keys from .env.example
```

### Frontend (Vercel / Netlify)
Just deploy `frontend/index.html` — update `API_BASE` constant to your backend URL.

---

## Real Commission Rates (2025)

| Platform    | Category          | Rate     |
|-------------|-------------------|----------|
| Amazon      | Luxury Beauty     | 10%      |
| Amazon      | Amazon Devices    | 4%       |
| Amazon      | Electronics       | 3%       |
| AliExpress  | Electronics       | 8%       |
| AliExpress  | Fashion           | 12%      |
| eBay        | Motors Parts      | 4%       |
| Etsy        | All categories    | 4% flat  |
| ShareASale  | SaaS/Software     | 5–30%    |
| ShareASale  | Retail            | 5–15%    |

---

## License
MIT

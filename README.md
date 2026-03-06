# gig-worker-insurance-platform
AI-powered parametric insurance platform protecting gig delivery workers from income loss caused by real-world disruptions.
# 🛡️ GigShield — AI-Powered Parametric Insurance for India's Gig Economy

> **Guidewire DEVTrails 2026 — University Hackathon Submission**  
> *Seed. Scale. Soar.*

---

## 📌 Table of Contents

1. [Problem Statement](#-problem-statement)
2. [Our Solution](#-our-solution)
3. [WhatsApp-First Experience](#-whatsapp-first-experience)
4. [Persona & Target Users](#-persona--target-users)
5. [Core Features](#-core-features)
6. [System Architecture](#-system-architecture)
7. [Application Workflow](#-application-workflow)
8. [Parametric Trigger Design](#-parametric-trigger-design)
9. [Weekly Premium Model](#-weekly-premium-model)
10. [AI/ML Integration](#-aiml-integration)
11. [Fraud Detection System](#-fraud-detection-system)
12. [Tech Stack](#-tech-stack)
13. [Project Structure](#-project-structure)
14. [Setup & Installation](#-setup--installation)
15. [API Integrations](#-api-integrations)
16. [Deliverables by Phase](#-deliverables-by-phase)
17. [Team](#-team)

---

## 🚨 Problem Statement

India's platform-based delivery partners — working across Zomato, Swiggy, Amazon, Flipkart, Zepto, Blinkit, Dunzo — are the backbone of the digital economy. Yet they remain completely exposed to income loss from events entirely outside their control.

| Disruption | Impact |
|---|---|
| Heavy Rainfall (> 35mm/hr) | Deliveries halted, zero earnings |
| Extreme Heat (> 44°C) | Outdoor work unsafe, lost hours |
| Severe Air Pollution (AQI > 300) | Platforms suspend outdoor operations |
| Unplanned Curfew / Civil Strike | Unable to access pickup/drop zones |
| Flash Floods / Waterlogging | Zone-wide delivery suspension |

**The result:** Gig workers lose 20–30% of monthly income with no safety net, no employer protection, and no insurance.

> ⚠️ **Critical Constraint:** GigShield covers **INCOME LOSS ONLY**. It explicitly excludes health insurance, life insurance, accident claims, and vehicle repair payouts.

---

## 💡 Our Solution

GigShield is an AI-enabled Parametric Insurance Platform that meets gig workers exactly where they already are — on **WhatsApp**.

- Automatically detects disruptions via real-time data feeds (weather, AQI, civic alerts)
- Triggers payouts automatically — **no claim filing required** from the worker
- Uses ML-based dynamic pricing for fair, hyper-local weekly premiums
- Delivers instant payouts to the worker's UPI/bank account — with a WhatsApp confirmation
- Workers interact with GigShield entirely through WhatsApp if they prefer — no app downloads needed

```
Disruption Detected → Trigger Verified → Claim Auto-Initiated → WhatsApp Alert → Payout in < 2 Hours
```

---

## 💬 WhatsApp-First Experience

> **Why WhatsApp?** India has 500+ million active WhatsApp users. For delivery partners earning ₹14,000–₹22,000/month, downloading yet another app creates friction. WhatsApp is already open, already trusted, and already used for everything. GigShield meets workers on this familiar ground.

### WhatsApp as a Full Interaction Channel

GigShield treats WhatsApp not as a notification tool, but as a **primary interface**. Using the **Meta WhatsApp Business Cloud API** with a Django webhook backend, workers can:

| Worker Action | WhatsApp Interaction |
|---|---|
| Register & Onboard | Guided conversation flow collects name, mobile, zone, segment |
| KYC Verification | Upload Aadhaar/PAN photo directly in WhatsApp chat |
| Choose Weekly Plan | Interactive button messages — tap to select Basic / Standard / Premium |
| Check Policy Status | Send "STATUS" → receive active policy summary instantly |
| Check Payout Balance | Send "BALANCE" → receive this week's earnings protected |
| Receive Claim Alerts | Auto-pushed message when disruption triggers a payout |
| Confirm Bank Details | One-time UPI ID submission via WhatsApp reply |
| Get Risk Forecast | Send "RISK" → receive next week's disruption probability for their zone |

### WhatsApp Onboarding Flow (Zero App Required)

```
Worker sends "Hi" to GigShield WhatsApp number
           │
           ▼
Bot replies: "Welcome to GigShield 🛡️
Select language:
[1] English  [2] हिंदी  [3] தமிழ்  [4] ಕನ್ನಡ"
           │
           ▼
Worker selects language → Bot continues in chosen language
           │
           ▼
"What platform do you work on?
[1] Zomato/Swiggy  [2] Amazon/Flipkart  [3] Zepto/Blinkit/Dunzo"
           │
           ▼
Worker picks segment → Bot asks for zone (city + area)
           │
           ▼
"Please share your Aadhaar number for KYC verification"
Worker replies with Aadhaar / uploads photo
           │
           ▼
AI Risk Profiling runs in background
           │
           ▼
Bot sends plan recommendation with interactive buttons:
"Based on your zone (Dharavi, Mumbai) and segment (Food Delivery),
we recommend Standard Shield 🛡️

[Basic ₹29/week — ₹500 cover]
[Standard ₹49/week — ₹1,000 cover ✅ Recommended]
[Premium ₹79/week — ₹2,000 cover]"
           │
           ▼
Worker taps choice → Bot asks for UPI ID
           │
           ▼
✅ Policy Active — Worker receives confirmation in WhatsApp
"Your GigShield is active from Monday! 🎉
You're covered for Rain, Heat, AQI, Floods & Curfews.
Reply HELP anytime for support."
```

### Zero-Touch Payout Notification (Worker's Favourite Moment)

When a disruption triggers an automatic payout, the worker receives:

```
🛡️ GigShield Payout Alert

✅ ₹350 has been credited to your UPI ID (raj***@upi)

📍 Zone: Bangalore South
⚡ Trigger: Heavy Rain (38mm/hr — 7:00–9:00 PM)
📅 Date: 6 March 2026

Your Shield worked for you tonight.
Reply STATUS to see your full claim history.
```

### Two-Way Command System

Workers can text these commands anytime:

| Command | Response |
|---|---|
| `STATUS` | Active policy, expiry date, this week's cover amount |
| `BALANCE` | Total payouts received this month |
| `CLAIMS` | Last 5 claim events with amounts |
| `RENEW` | Renew or upgrade weekly plan via button tap |
| `RISK` | Next week's disruption forecast for worker's zone |
| `HELP` | Human support escalation (Admin notified) |
| `STOP` | Pause coverage (with confirmation prompt) |

### WhatsApp for Admin Alerts Too

The insurer-side Admin also receives WhatsApp alerts for high-priority events:
- Fraud flagged above score 0.7 → Instant WhatsApp to Admin
- Zone-wide mass claim event → Summary pushed to Admin's WhatsApp
- Weekly loss ratio breached → Alert with zone breakdown

### Technical Implementation

```python
# backend/apps/whatsapp/client.py

import requests

class WhatsAppClient:
    BASE_URL = "https://graph.facebook.com/v18.0"

    def __init__(self):
        self.token = settings.WHATSAPP_TOKEN
        self.phone_id = settings.WHATSAPP_PHONE_ID

    def send_payout_alert(self, worker, claim, amount):
        """Push payout confirmation to worker's WhatsApp"""
        payload = {
            "messaging_product": "whatsapp",
            "to": worker.mobile_number,
            "type": "template",
            "template": {
                "name": "gigshield_payout_alert",
                "language": {"code": worker.language_preference},
                "components": [{
                    "type": "body",
                    "parameters": [
                        {"type": "text", "text": f"₹{amount}"},
                        {"type": "text", "text": claim.trigger_type},
                        {"type": "text", "text": claim.zone_name},
                        {"type": "text", "text": str(claim.event_date)}
                    ]
                }]
            }
        }
        requests.post(
            f"{self.BASE_URL}/{self.phone_id}/messages",
            headers={"Authorization": f"Bearer {self.token}"},
            json=payload
        )

    def send_interactive_plan_selection(self, worker, recommended_plan):
        """Send plan selection with tap-to-choose buttons"""
        payload = {
            "messaging_product": "whatsapp",
            "to": worker.mobile_number,
            "type": "interactive",
            "interactive": {
                "type": "button",
                "body": {"text": f"Choose your weekly plan for {worker.zone}:"},
                "action": {
                    "buttons": [
                        {"type": "reply", "reply": {"id": "BASIC", "title": "Basic ₹29/week"}},
                        {"type": "reply", "reply": {"id": "STANDARD", "title": "Standard ₹49/week ✅"}},
                        {"type": "reply", "reply": {"id": "PREMIUM", "title": "Premium ₹79/week"}}
                    ]
                }
            }
        }
        requests.post(
            f"{self.BASE_URL}/{self.phone_id}/messages",
            headers={"Authorization": f"Bearer {self.token}"},
            json=payload
        )
```

```python
# backend/apps/whatsapp/webhook.py — Handles incoming worker messages

@csrf_exempt
def whatsapp_webhook(request):
    """Receives and routes all incoming WhatsApp messages from workers"""
    data = json.loads(request.body)
    message = extract_message(data)

    if not message:
        return HttpResponse("OK")

    worker = Worker.objects.filter(mobile=message["from"]).first()
    text = message.get("text", {}).get("body", "").upper().strip()

    COMMAND_HANDLERS = {
        "STATUS":  handle_status,
        "BALANCE": handle_balance,
        "CLAIMS":  handle_claims,
        "RENEW":   handle_renewal,
        "RISK":    handle_forecast,
        "HELP":    handle_support,
        "STOP":    handle_pause,
    }

    handler = COMMAND_HANDLERS.get(text)
    if handler:
        handler(worker, message)
    elif not worker:
        initiate_onboarding(message["from"])
    else:
        handle_onboarding_step(worker, message)

    return HttpResponse("OK")
```

### WhatsApp Webhook Endpoints

```
POST  /api/whatsapp/webhook/        # Meta verification + message receiver
GET   /api/whatsapp/webhook/        # Meta webhook verification challenge
POST  /api/whatsapp/send-alert/     # Internal: trigger payout/claim notifications
```

---

## 👤 Persona & Target Users

### All Delivery Segments Covered

| Segment | Platforms | Key Risk Window | Unique Risk Factor |
|---|---|---|---|
| Food Delivery | Zomato, Swiggy | Lunch (12–2 PM), Dinner (7–10 PM) | Rain/heat during peak hours wipes out 50%+ earnings |
| E-commerce Delivery | Amazon, Flipkart, Meesho | Full day, sale periods | Curfews & floods block warehouse-to-home last mile |
| Grocery / Q-Commerce | Zepto, Blinkit, Dunzo | All day, 10-min delivery windows | AQI alerts & extreme heat hit continuous delivery cycles |

### User Personas

**🚴 Rajan — Food Delivery Partner (Zomato)**  
Age: 24 | Earns: ~₹18,000/month  
Works dinner peak hours (6 PM–10 PM) — one rainstorm = ₹500 lost  
*Needs:* Simple Hindi WhatsApp interface, no app downloads, instant payout alerts

**📦 Arjun — E-commerce Delivery Partner (Amazon Flex)**  
Age: 30 | Earns: ~₹22,000/month  
Covers 30–40 deliveries/day across city zones  
*Needs:* Zone-level curfew alerts, daily income protection, WhatsApp status updates

**🛒 Priya — Grocery/Q-Commerce Partner (Zepto)**  
Age: 22 | Earns: ~₹14,000/month  
Completes 80–100 hyper-local deliveries/day  
*Needs:* AQI + heat trigger coverage, UPI instant payout, Tamil-language WhatsApp

**🖥️ Admin / Insurance Operations Manager (Secondary User)**  
Monitors fraud alerts, loss ratios, zone-level claim analytics  
Receives high-priority WhatsApp alerts for fraud flags and mass claim events

---

## ✨ Core Features

### Must-Have Features

| Feature | Description |
|---|---|
| WhatsApp Onboarding | Full registration, KYC, and plan selection — entirely via WhatsApp chat, no app needed |
| Worker Web App | React-based web dashboard for workers who prefer a visual interface |
| Segment Selection | Worker chooses Food / E-commerce / Grocery at signup |
| Weekly Policy Creation | ₹29–₹79/week tiered plans, AI-adjusted per zone and segment |
| Parametric Trigger Engine | Auto-monitors 5 disruption types in real-time via Celery Beat |
| Zero-Touch Claims | Disruption detected = claim auto-filed, no worker action needed |
| Instant Payout + WhatsApp Alert | UPI/bank transfer within 2 hours, immediately followed by WhatsApp confirmation |
| Two-Way WhatsApp Commands | Workers query STATUS, BALANCE, CLAIMS, RISK anytime via chat |
| AI Risk Assessment | XGBoost model scoring per zone, segment, and season |
| Fraud Detection | GPS validation, anomaly scoring, duplicate prevention |
| Dual Dashboard | Worker earnings view + Admin analytics + fraud queue |

### Innovation Features

| Feature | Description |
|---|---|
| Vernacular WhatsApp | Bot responds in Hindi, Tamil, Telugu, Kannada based on worker's language choice |
| WhatsApp Plan Upgrade | Workers can tap-to-upgrade their weekly plan mid-week via WhatsApp button messages |
| Risk Forecast via WhatsApp | "RISK" command returns next week's disruption probability for the worker's zone |
| Trust Score Discounts | Consistent, fraud-free workers earn premium discounts reflected in weekly WhatsApp renewal message |
| Admin WhatsApp Alerts | Fraud flags and mass claim summaries pushed directly to insurer's WhatsApp |

---

## 🏗️ System Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         GIGSHIELD PLATFORM                               │
├──────────────────┬─────────────────────────┬─────────────────────────────┤
│   FRONTEND       │   BACKEND (Django)      │    AI / ML LAYER            │
│                  │                         │                             │
│   React.js       │   Django REST           │   Risk Scoring Model        │
│   (Web App)      │   Framework (DRF)       │   (XGBoost via scikit)      │
│                  │                         │                             │
│   Worker UI      │   Auth Module           │   Fraud Detection           │
│   Admin UI       │   Policy Module         │   (Isolation Forest)        │
│                  │   Claims Module         │                             │
│                  │   Payout Module         │   Week-Ahead Forecast       │
│                  │   Trigger Engine        │   (Facebook Prophet)        │
│                  │   WhatsApp Module       │                             │
│                  │                         │   PostgreSQL DB             │
│                  │                         │   Celery + Redis Queue      │
└──────────────────┴─────────────────────────┴─────────────────────────────┘
        │                      │                           │
        ▼                      ▼                           ▼
  ┌──────────┐        ┌──────────────────┐      ┌─────────────────────────┐
  │  Browser │        │  External APIs   │      │  Payment + Messaging    │
  │  (React) │        │  OpenWeatherMap  │      │  Razorpay Test Mode     │
  └──────────┘        │  CPCB AQI API    │      │  UPI Simulator          │
        │             │  IMD/NDMA (Mock) │      │  Meta WhatsApp API      │
        ▼             │  Govt Curfew     │      │  (Business Cloud API)   │
  ┌──────────┐        │  (Mock Feed)     │      └─────────────────────────┘
  │WhatsApp  │        └──────────────────┘
  │(Primary  │
  │Interface)│
  └──────────┘
```

---

## 🔄 Application Workflow

### 1. Worker Onboarding — Two Paths, One Platform

```
PATH A: WhatsApp (Recommended for most workers)
Worker texts "Hi" to GigShield WhatsApp Number
              │
              ▼
Language selection → Segment selection → Zone entry
              │
              ▼
KYC via Aadhaar photo upload in WhatsApp
              │
              ▼
AI risk profiling runs in background
              │
              ▼
Plan recommendation via interactive button message
              │
              ▼
Worker taps plan → UPI ID collected via reply
              │
              ▼
✅ Policy Active — WhatsApp confirmation sent


PATH B: Web App (React frontend for workers who prefer it)
Worker visits GigShield web app
              │
              ▼
Mobile OTP verification → KYC upload → Segment + zone
              │
              ▼
AI risk profiling → Plan selection → UPI linking
              │
              ▼
✅ Policy Active — WhatsApp notification sent either way
```

### 2. Weekly Policy Management

```
Every Monday:
  ├── Premium auto-deducted via UPI
  ├── New policy ID generated for the week
  ├── Worker receives WhatsApp:
  │   "🛡️ GigShield renewed for this week!
  │    Plan: Standard Shield | Cover: ₹1,000
  │    Premium: ₹49 debited from your UPI
  │    Reply RISK to see this week's forecast."
  └── Zone-level risk recalculated for new week
```

### 3. Zero-Touch Claim Flow (Core Innovation)

```
External Disruption Occurs
(e.g., Heavy Rain in Bangalore — 7 PM, 38mm/hr)
              │
              ▼
Celery Beat polls OpenWeatherMap every 15 min
              │
              ▼
Threshold Check: rain.1h > 35mm? → YES
              │
              ▼
Identify all workers in affected zone
              │
              ▼
For each eligible worker:
  ├── Active policy this week? → Check DB
  ├── Worker was online during event window? → Platform API / mock
  └── Fraud score < 0.3? → Call ML fraud endpoint
              │
              ▼
All checks pass → Claim auto-created (status: APPROVED)
              │
              ▼
Payout calculated using earnings formula
              │
              ▼
Razorpay payout API called (test mode)
              │
              ▼
WhatsApp message pushed to worker:
"✅ GigShield: ₹350 credited to your UPI
 Trigger: Heavy Rain — Bangalore South — 7–9 PM, 6 Mar
 Reply CLAIMS to view full history."
```

---

## ⚡ Parametric Trigger Design

All triggers use objective, third-party verifiable data. Workers never file a claim — the system handles everything.

| Trigger | Event | Data Source | Threshold | Segments Affected |
|---|---|---|---|---|
| T-01 | Heavy Rainfall | OpenWeatherMap API | > 35mm/hr for 30 min | Food, Grocery, E-comm |
| T-02 | Extreme Heat | OpenWeatherMap API | > 44°C sustained 2 hrs | Food, Grocery |
| T-03 | Severe Air Pollution | CPCB / WAQI AQI API | AQI > 300 for 3 hrs | All segments |
| T-04 | Flood / Waterlogging | IMD + NDMA (Mock) | Zone flood alert issued | All segments |
| T-05 | Curfew / Civil Disruption | Govt alert mock feed | Official curfew declared | All segments |

### Payout Formula

```
Payout = Base Daily Earning × Coverage % × (Disruption Hours ÷ Active Work Hours)

Minimum Floor: ₹100 per event
Maximum Cap:   Capped by weekly plan limit (₹500 / ₹1,000 / ₹2,000)

Example — Food Delivery, Rain Event:
  Rajan earns ₹700/day
  Rain event 7–9 PM (peak dinner = 40% of daily earnings)
  Payout = ₹700 × 0.40 × (2/8) = ₹70 → floored to ₹100 minimum
```

---

## 💰 Weekly Premium Model

### Pricing Tiers

| Plan | Weekly Premium | Max Weekly Payout | Triggers Covered |
|---|---|---|---|
| Basic Shield | ₹29 / week | ₹500 | Rain + Heat only |
| Standard Shield | ₹49 / week | ₹1,000 | All 5 triggers |
| Premium Shield | ₹79 / week | ₹2,000 | All 5 + priority payout (< 1 hr) |

### AI-Driven Dynamic Premium Formula

```
Final Premium = Base Rate × Zone Risk Score × Claim History Factor × Season Multiplier × Segment Factor

Variables:
  Zone Risk Score      → 0.8 (safe zone) to 1.5 (flood-prone / AQI hotspot)
  Claim History Factor → 0.85 (loyal, clean record) to 1.3 (repeated claims)
  Season Multiplier    → 1.0 (Jan–Mar) | 1.3 (Apr–Jun) | 1.6 (Jul–Sep) | 1.1 (Oct–Dec)
  Segment Factor       → Food: 1.1 | E-commerce: 1.0 | Grocery: 1.2

Example:
  Plan: Standard Shield (₹49 base)
  Zone: Dharavi, Mumbai → Risk: 1.3
  Worker: 6 months, no fraud → History: 0.9
  Month: August → Season: 1.6
  Segment: Food Delivery → 1.1
  Final Premium = ₹49 × 1.3 × 0.9 × 1.6 × 1.1 = ₹100.8/week
```

---

## 🤖 AI/ML Integration

### 1. Risk Scoring Model — XGBoost

**Purpose:** Calculate dynamic weekly premium per worker

**Input Features:**

| Feature | Source |
|---|---|
| Delivery zone (lat/lon cluster) | Worker profile |
| Zone's historical disruption frequency (2 years) | IMD historical data |
| Worker's active hours (peak vs off-peak) | Platform API / declared |
| Platform tenure (months active) | Worker profile |
| Claim history count (last 6 months) | GigShield DB |
| Month of year (seasonal factor) | System date |
| Delivery segment | Worker profile |

**Output:** Risk score 0.0–2.0 → fed into premium multiplier  
**Training:** Synthetic dataset from IMD weather records + simulated worker profiles (~50,000 rows)

### 2. Fraud Detection — Isolation Forest

**Purpose:** Flag suspicious claims before payout is released

| Anomaly Signal | How It's Checked |
|---|---|
| GPS at claim time vs disruption zone | Worker's last GPS ping vs event zone boundary |
| Worker platform activity during event | Mock platform API — was worker accepting orders? |
| Claim frequency vs zone baseline | Statistical z-score vs historical zone average |
| Coordinated mass claims | Cross-worker cluster analysis (same zone + same 15-min window) |
| Device fingerprint | Same device filing claims for multiple worker IDs |

**Decision Logic:**
```
Fraud Score 0.0 – 0.3  → Auto-approve  → Instant payout + WhatsApp confirmation
Fraud Score 0.3 – 0.6  → Hold 24 hrs  → Admin review queue + worker notified via WhatsApp
Fraud Score 0.6+       → Auto-reject   → Account flagged + Admin WhatsApp alert sent
```

### 3. Week-Ahead Forecast — Facebook Prophet

**Purpose:** Predict next week's disruption probability per zone

- Ingests 7-day weather forecast from OpenWeatherMap
- Outputs per-zone risk probability for each trigger type
- Shown on Admin dashboard: *"Mumbai North — Rain Trigger: 74% probability this week"*
- Available to workers via WhatsApp `RISK` command: *"⚠️ High rain risk this week — your Standard Shield is ready"*

---

## 🛡️ Fraud Detection System

### Three-Layer Architecture

```
LAYER 1 — Real-Time (fires at every claim trigger)
  ├── GPS boundary check: worker within disrupted zone ± 2km?
  ├── Platform activity check: was worker online during the event?
  ├── Zone event validation: did this disruption actually affect this area?
  └── Duplicate guard: has this worker already been paid for this event ID?

LAYER 2 — Daily Batch (Celery scheduled task, runs at 2 AM)
  ├── Per-worker anomaly scoring (Isolation Forest model)
  ├── Cross-worker cluster fraud detection
  ├── Claim frequency deviation from historical zone baseline
  └── Device fingerprint duplication check

LAYER 3 — Manual Review (Admin Dashboard + WhatsApp Alerts)
  ├── High-severity flags (score > 0.7) pushed to Admin's WhatsApp instantly
  ├── All Layer 1/2 flags surface in Admin web dashboard queue
  ├── Admin reviews evidence and approves / permanently rejects
  └── Outcome fed back as labelled data to retrain model monthly
```

### Duplicate Claim Prevention

- Each disruption event gets a unique `event_fingerprint` (zone + trigger + time window)
- Database unique constraint: one claim per `(worker_id, event_fingerprint)`
- Payout service is idempotent — retries will never double-transfer

---

## 🧰 Tech Stack

### Frontend

| Technology | Version | Purpose |
|---|---|---|
| React.js | 18.x | Web application framework |
| Tailwind CSS | 3.x | UI component styling |
| React Query (TanStack) | 5.x | Server state + caching |
| React Router | 6.x | Client-side routing |
| i18next | latest | Hindi, Tamil, Telugu, Kannada support |
| Recharts | latest | Analytics dashboard charts |
| Axios | latest | HTTP client for Django REST API |

### Backend

| Technology | Version | Purpose |
|---|---|---|
| Python | 3.11+ | Primary backend language |
| Django | 4.2 LTS | Core web framework |
| Django REST Framework | 3.14+ | REST API layer |
| Celery | 5.x | Async task queue (trigger engine, payouts, WhatsApp dispatch) |
| Redis | 7.x | Celery broker + result backend + caching |
| PostgreSQL | 15.x | Primary relational database |
| SimpleJWT | latest | JWT authentication for React frontend |
| Gunicorn | latest | Production WSGI server |

### AI / ML

| Technology | Purpose |
|---|---|
| scikit-learn + XGBoost | Risk scoring model for premium calculation |
| Isolation Forest (scikit-learn) | Unsupervised fraud anomaly detection |
| Facebook Prophet | Week-ahead weather risk forecasting |
| Pandas + NumPy | Feature engineering and data preprocessing |
| joblib | Model serialization and loading in Django |

### External APIs & Integrations

| Service | Purpose | Mode |
|---|---|---|
| OpenWeatherMap API | Rain, heat, wind trigger monitoring | Free tier (live) |
| WAQI / CPCB AQI API | Air quality index monitoring | Free (live) |
| IMD / NDMA | Flood and disaster alerts | Mock / simulated |
| Razorpay Test Mode | Payout processing simulation | Sandbox |
| **Meta WhatsApp Business Cloud API** | **Worker onboarding, two-way commands, payout alerts, Admin alerts** | **Sandbox / Test** |
| DigiLocker / eKYC | Aadhaar-based identity verification | Simulated |

### DevOps

| Technology | Purpose |
|---|---|
| Docker + Docker Compose | Multi-service containerization |
| GitHub Actions | CI/CD — lint, test, deploy pipeline |
| Render / Railway | Cloud hosting (free tier for demo) |
| Nginx | Reverse proxy (frontend + backend) |

---

## 📁 Project Structure

```
gigshield/
│
├── frontend/                          # React Web App
│   ├── public/
│   └── src/
│       ├── pages/
│       │   ├── Onboarding/            # KYC, registration, segment selection
│       │   ├── Dashboard/             # Worker weekly earnings dashboard
│       │   ├── Policy/                # Weekly plan selection & management
│       │   ├── Claims/                # Claim history + payout status
│       │   └── Admin/                 # Insurer analytics + fraud queue
│       ├── components/
│       ├── hooks/
│       ├── services/                  # Axios API call functions
│       ├── i18n/                      # Language JSON files (hi, ta, te, kn)
│       └── App.jsx
│
├── backend/                           # Django Backend
│   ├── gigshield/
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── celery.py
│   │
│   ├── apps/
│   │   ├── accounts/                  # Worker auth, KYC, profiles
│   │   │   ├── models.py
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── urls.py
│   │   │
│   │   ├── policies/                  # Weekly policy management
│   │   │   ├── models.py
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── premium_calculator.py  # Calls ML risk model
│   │   │
│   │   ├── claims/                    # Claim lifecycle
│   │   │   ├── models.py
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   └── auto_claimer.py        # Auto-claim logic
│   │   │
│   │   ├── payouts/                   # Payout processing
│   │   │   ├── models.py
│   │   │   ├── razorpay_service.py    # Razorpay test mode integration
│   │   │   └── tasks.py               # Celery async payout task
│   │   │
│   │   ├── triggers/                  # Parametric trigger engine
│   │   │   ├── models.py
│   │   │   ├── engine.py
│   │   │   ├── tasks.py               # Celery Beat scheduled polling
│   │   │   └── api_clients/
│   │   │       ├── weather_client.py
│   │   │       ├── aqi_client.py
│   │   │       └── mock_clients.py
│   │   │
│   │   ├── whatsapp/                  # ✨ WhatsApp Integration Module
│   │   │   ├── client.py              # Meta Cloud API wrapper (send messages)
│   │   │   ├── webhook.py             # Incoming message handler + command router
│   │   │   ├── onboarding_flow.py     # Conversational onboarding state machine
│   │   │   ├── command_handlers.py    # STATUS, BALANCE, CLAIMS, RISK, HELP, STOP
│   │   │   ├── templates.py           # Message template strings (multilingual)
│   │   │   └── urls.py                # /api/whatsapp/webhook/ endpoint
│   │   │
│   │   └── fraud/                     # Fraud detection
│   │       ├── fraud_scorer.py
│   │       ├── tasks.py
│   │       └── admin_views.py
│   │
│   ├── ml_models/
│   │   ├── risk_model.pkl
│   │   ├── fraud_model.pkl
│   │   └── forecast_model.pkl
│   │
│   ├── manage.py
│   └── requirements.txt
│
├── ml_training/                       # Offline ML training scripts
│   ├── train_risk_model.py
│   ├── train_fraud_model.py
│   ├── train_forecast_model.py
│   └── data/
│       └── synthetic_worker_dataset.csv
│
├── docs/
│   ├── architecture-diagram.png
│   ├── workflow-diagrams/
│   └── api-spec.yaml
│
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## 🚀 Setup & Installation

### Prerequisites

- Python 3.11+
- Node.js 18+
- PostgreSQL 15+
- Redis 7+
- Docker & Docker Compose (recommended)
- Meta WhatsApp Business API access (sandbox for hackathon)

### Quick Start with Docker

```bash
# 1. Clone the repository
git clone https://github.com/your-team/gigshield.git
cd gigshield

# 2. Set up environment variables
cp .env.example .env
# Fill in your API keys in .env

# 3. Build and start all services
docker-compose up --build

# Access the app:
# Frontend:    http://localhost:3000
# Django API:  http://localhost:8000/api/
# API Docs:    http://localhost:8000/api/schema/swagger-ui/
# Admin Panel: http://localhost:8000/admin/
# WA Webhook:  http://localhost:8000/api/whatsapp/webhook/
```

### Manual Setup (Without Docker)

```bash
# ── Backend (Django) ──────────────────────────────────
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
createdb gigshield_db
python manage.py migrate
python manage.py loaddata fixtures/initial_data.json
python manage.py createsuperuser
python manage.py runserver

# ── Celery Worker ─────────────────────────────────────
celery -A gigshield worker --loglevel=info

# ── Celery Beat Scheduler ──────────────────────────────
celery -A gigshield beat --loglevel=info

# ── Frontend (React) ──────────────────────────────────
cd frontend
npm install
npm start
# App running at http://localhost:3000
```

### Environment Variables

```env
# Django
SECRET_KEY=your_django_secret_key
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database
DB_NAME=gigshield_db
DB_USER=postgres
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=5432

# Redis & Celery
REDIS_URL=redis://localhost:6379/0
CELERY_BROKER_URL=redis://localhost:6379/0

# External APIs
OPENWEATHER_API_KEY=your_openweather_key
WAQI_API_KEY=your_waqi_key

# Razorpay (Test Mode)
RAZORPAY_KEY_ID=rzp_test_xxxx
RAZORPAY_KEY_SECRET=your_test_secret

# WhatsApp Business Cloud API (Meta)
WHATSAPP_TOKEN=your_meta_access_token
WHATSAPP_PHONE_ID=your_phone_number_id
WHATSAPP_VERIFY_TOKEN=your_webhook_verify_token
WHATSAPP_BUSINESS_ACCOUNT_ID=your_waba_id

# Frontend URL (for CORS)
FRONTEND_URL=http://localhost:3000
```

---

## 🔌 API Integrations

### Trigger Engine — Celery Beat Schedule

```python
# backend/gigshield/celery.py

CELERY_BEAT_SCHEDULE = {
    "poll-weather-triggers": {
        "task": "apps.triggers.tasks.poll_weather",
        "schedule": crontab(minute="*/15"),          # Every 15 minutes
    },
    "poll-aqi-triggers": {
        "task": "apps.triggers.tasks.poll_aqi",
        "schedule": crontab(minute="*/30"),          # Every 30 minutes
    },
    "daily-fraud-scan": {
        "task": "apps.fraud.tasks.daily_fraud_scan",
        "schedule": crontab(hour=2, minute=0),       # Daily at 2 AM
    },
    "weekly-policy-renewal": {
        "task": "apps.policies.tasks.renew_weekly_policies",
        "schedule": crontab(day_of_week="monday", hour=0, minute=0),
    },
}
```

### Key Django REST API Endpoints

```
# Auth & Workers
POST   /api/auth/register/              # Worker registration
POST   /api/auth/login/                 # JWT token obtain
GET    /api/workers/me/                 # Worker profile + risk score

# Policies
POST   /api/policies/create/            # Create weekly policy
GET    /api/policies/active/            # Get current active policy
GET    /api/policies/history/           # Policy renewal history

# Claims & Payouts
GET    /api/claims/                     # Worker's claim history
GET    /api/claims/<id>/                # Individual claim detail
GET    /api/payouts/                    # Payout history + status

# Admin
GET    /api/admin/dashboard/            # Admin analytics summary
GET    /api/admin/fraud-queue/          # Claims pending fraud review
PATCH  /api/admin/fraud-queue/<id>/     # Approve or reject flagged claim
GET    /api/admin/trigger-log/          # Trigger event log

# WhatsApp
POST   /api/whatsapp/webhook/           # Incoming messages from workers
GET    /api/whatsapp/webhook/           # Meta webhook verification
POST   /api/whatsapp/send-alert/        # Internal: trigger notifications
```

### Razorpay Test Mode Payout

```python
# backend/apps/payouts/razorpay_service.py

import razorpay

client = razorpay.Client(auth=(settings.RAZORPAY_KEY_ID, settings.RAZORPAY_KEY_SECRET))

def initiate_payout(worker, amount_inr, claim):
    payout = client.payout.create({
        "account_number": "2323230074796898",
        "fund_account_id": worker.razorpay_fund_account_id,
        "amount": int(amount_inr * 100),
        "currency": "INR",
        "mode": "UPI",
        "purpose": "payout",
        "narration": f"GigShield | {claim.trigger_type} | {claim.event_date}"
    })
    return payout
```

---

## 📅 Deliverables by Phase

### ✅ Phase 1: Ideation & Foundation (March 4–20)

- [x] README.md with complete solution design including WhatsApp architecture
- [x] GitHub repository created and link submitted
- [x] 2-minute strategy + prototype walkthrough video uploaded

### 🔧 Phase 2: Automation & Protection (March 21–April 4)

- [ ] Worker registration via both Web App and WhatsApp onboarding flow
- [ ] Weekly policy creation with dynamic AI premium calculation
- [ ] WhatsApp two-way command system (STATUS, BALANCE, CLAIMS, RENEW, RISK, HELP)
- [ ] Claims management UI and Django backend
- [ ] 3–5 parametric triggers wired to OpenWeatherMap + WAQI + mock APIs
- [ ] Zero-touch claim auto-processing via Celery tasks
- [ ] WhatsApp payout alert pushed to worker on every successful claim
- [ ] 2-minute demo video

### 🚀 Phase 3: Scale & Optimise (April 5–17)

- [ ] Advanced fraud detection with GPS spoof + Isolation Forest anomaly scoring
- [ ] Admin WhatsApp alerts for high-severity fraud flags and mass claim events
- [ ] Razorpay sandbox payout simulation with UPI flow
- [ ] Intelligent dual dashboard — Worker view + Admin insurer analytics
- [ ] Prophet-based week-ahead disruption forecast (Admin dashboard + WhatsApp RISK command)
- [ ] 5-minute demo video (disruption → auto-claim → payout → WhatsApp confirmation)
- [ ] Final pitch deck PDF (persona + AI architecture + WhatsApp UX + business model)

---

## 📋 Constraints Compliance Checklist

| Requirement | Status | Implementation |
|---|---|---|
| Income loss only (no health/vehicle) | ✅ Compliant | Trigger engine only fires income-loss events |
| Weekly pricing model | ✅ ₹29 / ₹49 / ₹79/week | Celery resets + charges every Monday |
| Parametric triggers (no manual claims) | ✅ 5 triggers | Celery Beat polls every 15–30 min |
| AI/ML integration | ✅ 3 models | XGBoost + Isolation Forest + Prophet |
| Fraud detection | ✅ Multi-layer | Real-time + daily batch + Admin review |
| Analytics dashboard | ✅ Dual | Worker earnings + Admin insurer view |
| All delivery segments | ✅ Covered | Food, E-commerce, Grocery/Q-Commerce |
| **WhatsApp integration** | ✅ **Full two-way** | **Onboarding + commands + payout alerts + Admin alerts** |

---

## 👥 Team

| Name | Role |
|---|---|
| [Member 1] | Full-Stack (React + Django + WhatsApp Webhook) |
| [Member 2] | ML/AI Engineer (Python, scikit-learn, XGBoost, Prophet) |
| [Member 3] | Backend + DevOps (Django, PostgreSQL, Celery, Redis) |
| [Member 4] | UI/UX + Frontend (React, Tailwind CSS, WhatsApp UX flows) |

**Institution:** [Your College / University Name]  
**GitHub Repository:** [Add your repo link here]  
**Phase 1 Demo Video:** [Add video link here]

---

> *Built with ❤️ for India's gig workers — because no storm should steal their wages, and no insurance should need more than a WhatsApp message.*

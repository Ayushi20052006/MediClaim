# 🏥 MediClaim (ClaimEase)

### *The Zero-UI Multilingual Medical Claim Agent for Bharat*

[![Vite](https://img.shields.io/badge/Vite-8A2BE2?style=for-the-badge&logo=vite&logoColor=white)](https://vite.dev/)
[![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![Gemini](https://img.shields.io/badge/Gemini_2.5_Flash-1A73E8?style=for-the-badge&logo=google-gemini&logoColor=white)](https://ai.google.dev/)
[![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)](https://supabase.com/)
[![Vitest](https://img.shields.io/badge/Vitest-729B1B?style=for-the-badge&logo=vitest&logoColor=white)](https://vitest.dev/)

> **"No App. No English. No Rejections."**
>
> MediClaim is a **Zero-UI** health insurance claim agent designed for Bharat. It strips away the friction of filing medical claims by allowing patients to interact strictly inside **WhatsApp**—sending physical medical bill photos, prescriptions, and regional voice notes. The AI extracts structured details, flags missing parameters that could trigger rejections, audits items against policy limits, and compiles clean, standardized Third-Party Administrator (TPA) claim PDFs.

---

## 🏥 The Problem in Bharat

The traditional health insurance claim filing process in India is fundamentally broken for the average citizen due to:

*   **High Cognitive Load**: Navigating complex portals, deciphering insurance jargon, and filling out pages of English-only forms during times of medical stress.
*   **Document Friction**: Messy, handwritten doctor prescriptions, thermal receipts that fade, and missing stamps or signatures that go unnoticed.
*   **Delayed Rejections**: Claims being rejected weeks after discharge due to minor errors (e.g. faded pharmacy dates or missing doctor stamps), resulting in severe cash-flow issues.
*   **Language Barrier**: Traditional channels alienate the non-English-speaking majorities of India, creating digital exclusion.

---

## ✨ Key Features

*   💬 **WhatsApp UI Simulation**: A pixel-perfect, highly responsive, desktop and mobile-friendly simulation of a WhatsApp chat window to demonstrate the "Zero-UI" patient flow.
*   🗣 **Vernacular Native Agent**: Converses fluidly in regional languages. Supported languages include:
    *   🇬🇧 **English**
    *   🇮🇳 **Hindi (हिंदी) & Hinglish (Hinglish-mix)**
    *   🇮🇳 **Tamil (தமிழ்)**
    *   🇮🇳 **Malayalam (മലയാളം)**
    *   🇮🇳 **Bengali (বাংলা)**
*   📸 **Multimodal OCR & Document Parsing**: Upload multiple files simultaneously (images or PDFs of bills, prescriptions, pharmacy receipts) to extract critical claim elements automatically.
*   ⚖️ **Interactive Policy Auditor**: Toggle a **Demo Policy Rule Engine** (with standard limits like *₹3 Lakh Sum Insured*, *1% Room Rent Cap (₹3,000/day)*, and *non-payable consumables rule*) to instantly run audits on bill items.
*   ✂️ **Deduction Predictor**: Flags specific non-payable consumables (e.g., gloves, syringes, nebulizer kits) and lists them as deductions so patients understand their exact covered amount.
*   🛡️ **Proactive Error & Stamp Detection**: Evaluates the bills for critical missing elements—such as hospital stamps or physician signatures—and prompts the patient to fix them before submitting.
*   📄 **Multilingual TPA Form PDF Generation**: Renders a standard **Health Insurance Claim Form (Part A)** natively translated into the chosen language, attaches copies of the bills, and compiles a comprehensive claim bundle PDF ready to be printed or submitted.

---

## ⚡ Architectural Decisions & Highlights

> [!TIP]
> The prototype implements high-performance conversational patterns that eliminate the need for heavy backend infrastructure during client-side demonstrations.

### 1. Flyweight Context Passing
Instead of maintaining a heavy SQL database session state to track files, the frontend stores the parsed document JSON (`ClaimData`) in the localized state. It then **injects the document data dynamically as a system instruction** into the Gemini chat payload. The AI natively retains memory of the document being discussed without any database sync latency.

### 2. Hinglish & Multilingual Conversational Prompts
The core prompt instructs the AI to be highly empathetic and vernacular. If a user inputs Hinglish (e.g., *"Is bill ka claim kaise milega?"*), the agent automatically replies in the exact same Hinglish script, maintaining comfort and reassurance.

### 3. Direct Gemini SDK Calling (`src/lib/gemini.ts`)
To prevent CORS constraints and deploy edge-function version mismatches during client tests, the chat utilizes the stable Google Generative Language endpoints directly using **`gemini-2.5-flash`**. 
*   **Alternating History Optimizer**: The engine dynamically formats consecutive roles (e.g., merging multiple consecutive user text/file nodes) to comply with Google's strict alternating `user` and `model` API payload schema.

### 4. Client-side PDF Synthesis
Utilizes React `createRoot` inside a detached viewport container, loads Google Noto Sans regional fonts dynamically for language rendering, and prints individual pages (main claim + document attachments) into high-fidelity PDFs via `html2canvas` and `jsPDF`.

---

## 📁 Project Structure

```text
claim-ease-main/
├── public/                 # Static assets, branding, and logos
├── src/
│   ├── components/
│   │   ├── chat/
│   │   │   ├── ChatBubble.tsx        # Standard chat message design
│   │   │   ├── ClaimSummaryCard.tsx  # Interactive parsed invoice summary card
│   │   │   ├── Hero.tsx              # Beautiful multilingual landing screen
│   │   │   ├── TPAClaimForm.tsx      # Standardized TPA Form template (HTML layout)
│   │   │   ├── TypingBubble.tsx      # Realistic chat typing status indicator
│   │   │   └── WhatsAppChat.tsx      # Core chat engine, state, and file handler
│   │   ├── ui/                       # Tailwind + Shadcn UI primitives (Buttons, Progress, etc.)
│   │   └── NavLink.tsx               # Utility navigation components
│   ├── hooks/                        # Local React hooks (toast, mobile detection)
│   ├── integrations/                 # Client configurations (Supabase connection)
│   ├── lib/
│   │   ├── claim-pdf.tsx             # detached viewport React-to-PDF compiler
│   │   ├── gemini.ts                 # Direct Gemini API wrappers (Analysis & Chat)
│   │   ├── i18n.ts                   # Complete localization and translation matrices
│   │   └── utils.ts                  # Tailwind class mergers
│   ├── pages/
│   │   ├── Index.tsx                 # Main application controller
│   │   └── NotFound.tsx              # Fallback 404 routing screen
│   └── test/                         # Core test suite
│       ├── example.test.ts
│       └── setup.ts
├── supabase/                         # Supabase Edge Functions (chat-bot & analyze-bill)
├── vite.config.ts                    # Vite build configs
└── vitest.config.ts                  # Unit testing configurations
```

---

## 🚀 Getting Started

### Prerequisites

Ensure you have [Bun](https://bun.sh/) or [Node.js](https://nodejs.org/) installed on your machine.

### 1. Environment Setup

Create a `.env` file at the root of the project and populate it with your API credentials:

```env
# Gemini API Access (Required for chat bot and vision analysis)
VITE_GEMINI_API_KEY="YOUR_GOOGLE_GEMINI_API_KEY"

# Supabase Auth/Client Details (Required for backend integrations)
VITE_SUPABASE_URL="YOUR_SUPABASE_PROJECT_URL"
VITE_SUPABASE_ANON_KEY="YOUR_SUPABASE_ANON_KEY"
```

### 2. Install Dependencies

Using **Bun**:
```bash
bun install
```

Using **npm**:
```bash
npm install
```

### 3. Run Development Server

Launch the local server:
```bash
npm run dev
```
Open [http://localhost:5173](http://localhost:5173) in your browser to experience the simulated chat agent.

---

## 🧪 Testing

The codebase includes standard unit and integration tests powered by **Vitest**. To execute the test suite:

```bash
npm run test
```

---

## 🛡️ License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
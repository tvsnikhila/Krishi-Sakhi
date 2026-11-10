# 🌾 Krishi Sakhi

**An AI farming assistant for Kerala farmers**, giving personalized crop advice in English and Malayalam, with voice translation and spoken replies so it's usable regardless of literacy level.

Built with **Next.js, TypeScript and Google's Genkit** running Gemini models.

## The problem

Small farmers often can't get timely, farm-specific advice in their own language. Generic guidance ignores their soil, crop, irrigation and local weather, and text-heavy apps exclude farmers who are more comfortable speaking than reading.

## Features

| Feature | What it does |
|---|---|
| 🌐 **Language choice** | Farmers choose English or Malayalam on first visit; the whole interface switches, and the choice is remembered |
| 🧑‍🌾 **Farm profile** | Records location, crop, farm size, soil conditions and irrigation method |
| 🤖 **Personalized advisory** | Gemini generates crop-management, pest-control and best-practice advice from the farm's details, weather and history |
| 🔊 **Voice replies** | Advice can be read aloud using Gemini text-to-speech, in a Malayalam or English voice |
| 🎙️ **Voice translation** | Speak in English and get the Malayalam translation as text |
| 📒 **Activity log** | Log sowing, irrigation, fertilizer, pest control and harvest activities |

## How the AI works

All AI features are **Genkit flows** in `src/ai/flows/`, called from the UI as Next.js server actions. Each flow has a **Zod schema** for its input and output, so the model's response is validated before it reaches the page.

| Flow | Input → output |
|---|---|
| `personalized-crop-advisory.ts` | Farm details + weather + history → `{ cropManagementAdvice, pestControlAdvice, optimalFarmingPractices }` (Gemini 2.5 Flash, structured output) |
| `voice-based-translation.ts` | English audio (base64 data URI) → Malayalam text (Gemini multimodal input) |
| `ai-generated-voice-responses.ts` | Text + language → spoken WAV audio (Gemini 2.5 Flash TTS; raw PCM is wrapped into WAV on the server) |

```
Browser (Next.js + React)
   │  server action
   ▼
Genkit flow ──► Zod-validated prompt ──► Gemini (text / audio / TTS)
   │
   └──► typed result back to the UI
```

## Tech stack

**Frontend:** Next.js 15 (App Router) · React 18 · TypeScript · Tailwind CSS · shadcn/ui (Radix)
**AI:** Genkit · Google AI (Gemini 2.5 Flash, Gemini TTS) · Zod schemas
**Other:** React Hook Form · i18n dictionary for English and Malayalam · Firebase App Hosting config

## Running locally

Requirements: Node.js 18+ and a [Gemini API key](https://aistudio.google.com/apikey).

```bash
git clone https://github.com/tvsnikhila/Krishi-Sakhi.git
cd Krishi-Sakhi
npm install
```

Create a `.env` file in the project root:

```
GEMINI_API_KEY=your-key-here
```

Then start the app:

```bash
npm run dev          # http://localhost:9002
npm run genkit:dev   # optional: Genkit developer UI to test flows on their own
```

## Project structure

```
src/
├── ai/
│   ├── genkit.ts                 Genkit setup (Google AI plugin, default model)
│   └── flows/                    advisory, translation and text-to-speech flows
├── app/
│   ├── login/, signup/           auth screens
│   └── dashboard/                home, profile, advisory, activity-log pages
├── components/
│   ├── dashboard/                forms, advisory display, language switcher, nav
│   └── voice-translator.tsx      record → translate component
├── hooks/use-language.tsx        language context (persisted in localStorage)
└── lib/i18n.ts                   English and Malayalam strings
```

## Status and next steps

This is a hackathon prototype. The login and sign-up screens are UI only, and profiles and activity logs are not yet stored in a database.

- Add Firebase Authentication and store profiles and activity logs in Firestore
- Pull live weather from a weather API instead of entering it by hand
- Feed the logged activities into the advisory as historical data
- Support Malayalam speech input, not only English

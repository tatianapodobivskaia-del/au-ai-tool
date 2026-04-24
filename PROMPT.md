# The Prompt That Created This App

> **This is the prompt you paste into [claude.ai](https://claude.ai) or Claude Code to generate `index.html` from scratch.**
> Your instructor demoed this at the start of the workshop. If you want the full authoring experience — or to create a second, completely different AI tool — copy everything below and paste it into a new Claude chat.

---

## How to use this prompt

1. Open [claude.ai](https://claude.ai) → start a new chat
2. Copy **everything** between the `=====` lines below
3. Paste it into Claude
4. Wait ~60 seconds
5. Claude returns a complete `index.html` file
6. Copy Claude's code, paste it into your own `index.html`, save
7. Double-click `index.html` to preview locally

You can use this same prompt at any time to create **a brand new AI tool from scratch** — just change the vision section to describe a different tool.

---

## ═══════════════════════════════════════════════
## THE PROMPT · COPY EVERYTHING BELOW THIS LINE
## ═══════════════════════════════════════════════

Build a **single-file HTML application** called `index.html` — no build step, no framework, no external files beyond CDN scripts. It's a chat interface that talks to Anthropic's Claude API directly from the browser.

## What the app does

It's a customizable AI tool. A user types a message → gets a response from Claude → continues the conversation. The tool's personality is defined by a system prompt that I can easily change. The goal is: someone forks this, changes one string in the CONFIG object, and their tool transforms into a Socratic tutor, a recipe generator, a cover-letter writer, etc.

## Two modes, automatic switching

- **Demo Mode** — when no API key is set, the app returns canned responses from a small hardcoded pool. Every visitor without a key sees a working product.
- **Live Mode** — when a user adds their Anthropic API key via Settings, responses come from the real Claude API. Key is stored in `localStorage`, never sent anywhere except directly to Anthropic.

A small badge in the UI shows which mode is active ("Demo Mode" in coral, "Live Mode" in teal).

## Technical requirements

- **Model:** `claude-sonnet-4-5`
- **Endpoint:** `https://api.anthropic.com/v1/messages`
- **Required headers** for direct browser calls:
  - `Content-Type: application/json`
  - `x-api-key: <user's key from localStorage>`
  - `anthropic-version: 2023-06-01`
  - `anthropic-dangerous-direct-browser-access: true`
- **Request body:** include `model`, `max_tokens: 1024`, `system: CONFIG.systemPrompt`, and `messages: [...conversation history]`
- **Response handling:** extract text from `data.content` (array of blocks with `type: "text"`)
- **API key storage:** `localStorage.setItem('anthropic_api_key', key)` — NEVER anywhere else
- **NO backend, NO server, NO build tools** — this is a static HTML file that works by opening it in a browser or deploying to GitHub Pages

## Visual design

**Aesthetic:** dark editorial. Premium, focused, slightly serious. Not cartoonish.

**Color palette (use CSS variables):**
- Background: `#0A0E14` (very dark blue-black)
- Surface: `#141921` (cards)
- Surface-2: `#1E2530` (inputs, modal)
- Border: `#2A3340`
- Text primary: `#E8ECEF`
- Text muted: `#8B96A5`
- Coral accent (Atlantis University brand): `#F74E53`
- Teal accent (Atlantis University brand): `#26C0BB`

**Typography (load from Google Fonts):**
- Display: `Instrument Serif` (italics for accent)
- Body: `Manrope`
- Monospace: `JetBrains Mono`

**Background:** subtle radial-gradient ambient glow (coral top-left, teal bottom-right, very low opacity) behind everything.

## Animations — use GSAP 3.12 (loaded from cdnjs)

Include **six distinct GSAP animation moments**, each commented in the code with `🎬 GSAP #N`:

1. **Page load orchestration** — header, chat area, input, and settings button stagger in from below on first load using a GSAP timeline with `back.out(1.4)` easing.
2. **User message pop** — when a user sends a message, their bubble animates in with a small `scale` + `y` bounce.
3. **Typing indicator** — three dots that bounce in sequence using `gsap.to()` with `repeat: -1`, `yoyo: true`, and staggered delays. Shows while waiting for the AI response.
4. **AI response word-by-word reveal** — when the AI replies, split the text into word spans and use GSAP's `stagger` to fade them in sequentially, like a gentle typewriter effect.
5. **Mode badge flip** — when the user saves an API key and switches from Demo → Live (or vice versa), the mode badge does a 360° Y-axis flip with a scale pop at the midpoint. The text and color change at the flip's halfway point.
6. **Settings modal open/close** — the modal scales + fades in from 0.9 using `back.out(1.4)`; backdrop fades independently. Reverse on close.

## Layout structure

```
┌─────────────────────────────────────────────────────┐
│  ← Back link        [Mode Badge]       ⚙ Settings   │
│                                                      │
│  My AI Tool                                          │
│  Built with Claude · Hosted on GitHub Pages          │
│                                                      │
│  ═══════════════════════════════════════════        │
│                                                      │
│  [Welcome state — shown only before first msg]       │
│    Ready when you are.                               │
│    [3 clickable starter-prompt chips]                │
│                                                      │
│  [User message bubble]                               │
│     [AI response bubble with word-by-word reveal]    │
│     [Three bouncing dots while waiting]              │
│                                                      │
│  ═══════════════════════════════════════════        │
│  [ Type your message here......  ][ Send ]           │
│  Made by NAME · Atlantis University                  │
└─────────────────────────────────────────────────────┘
```

## The CONFIG object (customization surface)

Place a clearly-commented `CONFIG` object at the top of the `<script>` block. Every student will customize these four values first:

```js
const CONFIG = {
  systemPrompt: `You are a helpful assistant. Answer questions clearly and concisely. Be friendly but professional. If you don't know something, say so.`,

  suggestedPrompts: [
    "What can you help me with?",
    "Explain something complex simply.",
    "Give me an idea for a project."
  ],

  inputPlaceholder: "Ask me anything...",

  demoResponses: [
    "👋 I'm running in demo mode right now. To enable real AI responses, click Settings (top-right) and add your Anthropic API key.",
    "This is a starter app built for a Git & GitHub workshop at Atlantis University. Add an API key in Settings to make me fully functional!",
    "In demo mode, I give fixed replies. With an API key, I'm powered by Claude and answer anything you ask."
  ],

  model: "claude-sonnet-4-5",
  maxTokens: 1024
};
```

## Customization markers

Add `🎨 CUSTOMIZE:` comments at every spot a student should easily edit. Specifically:
- Browser tab `<title>`
- CSS `:root` variable block (colors / fonts)
- The big heading text
- The "Back" link destination
- The CONFIG object (already marked by being at the top)
- Footer name and GitHub username

## Behavior details

- **Enter** sends the message; **Shift+Enter** creates a newline
- Textarea auto-grows as the user types, capped at ~160px
- Welcome screen (with starter-prompt chips) fades out on first message
- Chat scrolls to bottom on each new message
- Settings modal: click ⚙ to open, Escape to close, click backdrop to close
- API key input uses `type="password"` with `autocomplete="off"`
- Send button is disabled while awaiting an AI response
- Modal has a subtle warning that the key stays in localStorage and a link to `console.anthropic.com`

## Code quality

- Everything inline: HTML + CSS + JavaScript in a single `index.html` file
- No `<form>` tags in the React-component sense — just plain HTML form elements handled via event listeners
- Comment section headers clearly (for example, `/* ── Header ── */`, `// ═══ GSAP #3 ═══`)
- Use `const` by default, `let` only when mutating
- Use `async/await` for the API call
- Handle errors gracefully — if the fetch fails, show the error in the chat as an AI message rather than crashing

## Response format

**Return the complete, full HTML file from `<!DOCTYPE html>` to `</html>`, unabridged. Do NOT truncate. Do NOT use placeholders like "...rest of code here...". The file should be ready to save and open in a browser.**

Target length: approximately 700–1000 lines. Include all styles and scripts inline. Load only the following from CDN: Google Fonts (Instrument Serif, Manrope, JetBrains Mono) and GSAP 3.12.

## ═══════════════════════════════════════════════
## END OF PROMPT · COPY EVERYTHING ABOVE THIS LINE
## ═══════════════════════════════════════════════

---

## What you'll get back

Claude will respond with a complete HTML file — typically 700–1000 lines — ready to save as `index.html` and open in any browser. Demo Mode works immediately; Live Mode works as soon as you add an API key in Settings.

## Customizing the prompt for a different tool

If you want a completely different AI tool (not just a tweak to the existing one), **change the top section** of the prompt. For example, replace the "What the app does" section with:

> *"It's a **Socratic tutor** for university students. The user asks a question and the AI responds only with Socratic counter-questions that lead them toward the answer — never giving the answer directly. The palette should be dark green instead of coral..."*

Then paste the (modified) prompt into Claude. You'll get a completely different `index.html` that still follows the same technical structure (single file, GSAP animations, Demo/Live mode, Anthropic API), but with your custom purpose baked in.

---

*Saved with your app so the prompt travels with the code. Every time you push to GitHub, this file goes with it — the recipe alongside the dish.*

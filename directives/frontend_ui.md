# Directive: Content Automation Front-End UI

## Goal
Build a single-page web application that serves as the control interface
for the Fetemi content automation system. It connects to n8n webhooks
to trigger and receive AI-generated content.

## The 4 UI States (one page, different views)
The app has one page with four sequential states. Only one is visible at a time.

### State 1: Input Form
- Textarea: "Content idea or topic"
- URL input: "Reference article URL (optional)"
- Validation: at least one field must be filled
- Submit button: "Generate Drafts"
- On submit: POST to n8n intake webhook, transition to State 2

### State 2: Loading
- Show animated loading indicator
- Text: "Generating 3 article drafts…"
- This state persists until the webhook response arrives

### State 3: Draft Selection
- Display 3 draft cards side by side (or stacked on mobile)
- Each card shows: article title, 150-word preview (truncated), angle label
  (e.g. "Problem/Cost angle", "How-to angle", "Data/Insights angle")
- One "Select this draft" button per card
- On selection: POST to n8n selection webhook, transition to State 4

### State 4: Platform Preview + Publish
- Three columns: LinkedIn, X (Twitter), Email Newsletter
- Each column shows the formatted platform post (read-only preview)
- Two buttons per column: "Publish Now" and "Save for later"
- On publish: POST to n8n publish webhook
- Show success confirmation per platform after publish

## Webhook Endpoints (placeholders — replace with real n8n URLs later)
- Intake: POST https://PLACEHOLDER/webhook/content-intake
- Selection: POST https://PLACEHOLDER/webhook/content-select
- Publish: POST https://PLACEHOLDER/webhook/content-publish

## Session Management
- Generate a UUID on page load and attach it to every webhook payload
  as `session_id`
- Store in sessionStorage so it persists across the state transitions

## JSON Payloads
Intake POST body:
{ "idea": string, "url": string, "session_id": string }

Selection POST body:
{ "selected_draft": string, "draft_index": number, "session_id": string }

Publish POST body:
{ "linkedin": string, "tweet": string, "email_subject": string,
  "email_body": string, "action": "publish_now"|"save", "session_id": string }

## Design Requirements
- Dark theme, editorial/magazine aesthetic
- Clean typography, generous whitespace
- Smooth state transitions (fade or slide)
- Mobile responsive
- No external UI libraries — plain HTML, CSS, JS only

## Error Handling
- If any webhook call fails, show an inline error message with a retry button
- Never leave the user on a blank loading screen

## Navigation Requirements
- On State 3 (Draft Selection): show a "← Start over" text link top-left
  that resets to State 1 and clears sessionStorage
- On State 4 (Platform Preview): show a "← Back to drafts" text link top-left
  that returns to State 3 without re-fetching (use the cached draft data)
- After all three platforms are published, show a "Create new content" button
  that resets the entire flow to State 1

## Design Revision
The current aesthetic is too generic. Revise as follows:
- Replace Playfair Display + Inter with: DM Serif Display (headings)
  + DM Mono (labels, metadata, angle tags) + DM Sans (body)
  — all available on Google Fonts, creates a sharp editorial-tech hybrid
- Background: #080809 (deeper, less "app-like")
- Add a very subtle noise texture overlay on the background using
  a CSS SVG filter (feTurbulence) — gives it print/paper depth
- Draft cards: add a thin left-border accent in a warm amber (#C8933F)
  that thickens on hover, no background fill change on hover
- Angle tags (BUSINESS VALUE, TECHNICAL DEEP-DIVE, etc.): use DM Mono,
  0.7rem, letter-spacing: 0.12em — make them feel like editorial category slugs
- Platform column headers (LinkedIn, X Post, Newsletter): replace the
  icon + label with a large faint watermark letter behind the column
  (L, X, N) in ~15vw size, 4% opacity — editorial section marker
- The "Generate Drafts" and "Publish Now" buttons: remove the white fill,
  use transparent background + 1px solid rgba(255,255,255,0.3) border,
  with a slow (0.4s) white fill on hover using clip-path wipe, not opacity fade
- Add a thin 1px horizontal rule (#1a1a1f) between the header and content
  area on every state

## Layout & Visual Redesign (Priority Override)

Completely restructure State 1 (Input Form) layout. Reference aesthetic:
asymmetric split-screen, bold display type left, contained form card right.

### State 1 Layout
- Full viewport height, two columns (60/40 split)
- LEFT column: large ALL CAPS display heading, heavy weight
  e.g. "GENERATE CONTENT THAT CONVERTS" — stacked across 3 lines
  Below it: 2-line descriptor in small regular weight
  Font: use 'Bebas Neue' (Google Fonts) for the display heading
  Accent color: #E8A838 (amber-gold) — apply to one word or line
  of the heading to create contrast
- RIGHT column: dark card (#111114 background, 1px solid #222228 border,
  24px border-radius) containing the form fields and submit button
  Card should be vertically centered in the column
  Label style: DM Mono, 0.7rem, letter-spacing 0.15em, uppercase, muted color
  Input fields: dark (#0d0d10), 1px border (#2a2a32), no border-radius (sharp)
  Submit button: full width, solid #E8A838 background, #080809 text,
  Bebas Neue font, 1rem letter-spacing, uppercase — NO border on this button

### State 3 (Draft Cards) Layout
- Keep 3-column grid but make cards taller and more editorial
- Large card number (01, 02, 03) in top-left of each card in Bebas Neue,
  4rem, 8% opacity — acts as a background texture element
- Angle tag: DM Mono, amber (#E8A838) text, no background pill
- Card title: DM Serif Display, 1.4rem
- Left border accent: 2px solid #E8A838 on the active/hovered card only
- "Select this narrative" button: transparent, full-width, 1px solid #E8A838,
  Bebas Neue, letter-spacing 0.1em — fills amber on hover

### State 4 (Platform Preview) Layout
- Keep 3-column layout
- Column header: platform name in Bebas Neue, 1.8rem, all caps
- Behind each column header, large faint watermark letter
  (L / X / N), Bebas Neue, ~8rem, 5% opacity
- "Publish Now" button: solid #E8A838, dark text, Bebas Neue
- "Save for later": ghost button, 1px #E8A838 border

### Global tokens
- Accent: #E8A838
- Background: #080809
- Card surface: #111114
- Border: #222228
- Muted text: #666672
- Body font: DM Sans
- Mono/labels: DM Mono  
- Display headings: Bebas Neue
- Noise texture: keep the feTurbulence overlay from previous version

### Navigation (from previous directive)
- "← Start over" and "← Back to drafts" links: DM Mono, 0.75rem,
  muted color (#666672), no underline, amber on hover
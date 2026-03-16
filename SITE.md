# Vibe Coders Anonymous

> A satirical support community for developers recovering from AI-dependent coding.

## Brand Identity

- **Personality**: Satirical, deadpan humor, parody of 12-step programs
- **Tone**: Dramatic and serious-sounding but absurd content, self-aware tech humor
- **Target Audience**: Developers who use AI coding tools (and can laugh at themselves)

### Visual Design

GitHub-dark inspired aesthetic with dramatic lighting and polish:

- **Background**: Deep charcoal (`#0d1117`)
- **Cards**: Slate (`#161b22`) with borders (`#30363d`)
- **Text**: Light gray (`#c9d1d9`) primary, medium gray (`#8b949e`) muted
- **Accent**: Vibrant orange (`#f97316`) for CTAs
- **Blue**: GitHub blue (`#58a6ff`) for links and highlights
- **Success**: Green (`#7ee787`) for recovery badges
- **Medallion**: Amber/gold gradient with glow effect

### Typography

- **Display**: Bitter (serif) - institutional, trustworthy
- **Body**: Source Sans 3 (sans-serif) - clean, readable
- **Code**: System monospace - terminal authenticity

## Interactive Features

### Sobriety Medallion

- Displays random 0-3 "days without prompting"
- **Click to relapse** - resets counter to 0 with toast notification
- Glowing pulse animation
- Hover effect scales up

### Warning Signs Diagnosis

- 10 checkable symptoms
- **Live diagnosis meter** that fills as you check boxes
- Dynamic messages based on count:
  - 0: "Check any that apply to you..."
  - 1-2: "You might be okay. Maybe." (green)
  - 3-4: "Concerning. Consider attending a meeting." (yellow)
  - 5-6: "This is serious. We're here for you." (orange)
  - 7-8: "Please, sit down. We need to talk." (red)
  - 9-10: "Oh no. OH NO. We're sending help immediately." (bright red)

### Scroll Animations

- Sections fade in and slide up when scrolling into view
- Uses IntersectionObserver for performance
- Hero elements animate in sequence on load

### Easter Eggs

- **Medallion click**: Resets your "days clean" to 0
- **Emergency Relapse Button** in footer: Links to claude.ai
- Floating code snippets in hero with gentle animations

## Page Sections

### 1. Hero (Full-screen)

- Animated sobriety medallion with glow
- Floating code snippets (`Claude.fix(everything)`, `await claude.pray()`)
- Gradient text headline
- Staggered entrance animations
- Scroll indicator

### 2. Terminal Confession

- macOS-style terminal window
- `~/recovery/confession.sh` running `cat my_story.txt`
- Syntax-highlighted output
- Blinking cursor

### 3. Warning Signs (Interactive)

- Diagnosis meter with progress bar
- 10 clickable symptom cards
- Color-coded severity feedback
- "Your shame is encrypted end-to-end" disclaimer

### 4. The Twelve Steps (Phased)

- Grouped into 3 phases:
  - **Acceptance** (Steps 1-4) - Blue badge
  - **Action** (Steps 5-8) - Orange badge
  - **Growth** (Steps 9-12) - Green badge
- Timeline-style layout with connector dots
- Hover effects on each step

### 5. Stories of Recovery

- **Featured testimonial**: Large, dramatic with background glow
- **Supporting testimonials**: 3-column grid below
- Job titles like "Ex-Vibe Architect", "LinkedIn Thought Leader"
- Green "days clean" badges

### 6. Serenity Prayer

- Full-width dramatic section
- Orange gradient glow background
- Noise texture overlay
- Emphasized keywords (serenity, courage, wisdom)

### 7. Recovery Resources

- 3-column interactive tool cards:
  - **CommitRoulette**: Generates funny "vibe-based" commit messages like "I have no idea what this does, but the tests passed"
  - **WeightTracker**: Scans node_modules and reveals your "emotional weight" (e.g., "Equivalent to 12 years of questioning your career choice")
  - **PanicButton**: Emergency detox button — opens Haskell, Rust, or COBOL docs in a new tab and shows a "you don't need the prompt" overlay

### 8. CTA Section

- Orange gradient with dot pattern overlay
- Large headline with line break
- Prominent "Join a Meeting" button
- Link to the Diagnostic Quiz (`/quiz`)
- Link to the 60 Seconds Sober game (`/game`)

### 9. Footer

- "One line at a time." tagline
- **Emergency Relapse Button** linking to Claude
- Satirical disclaimer

## Animations

- **Floating code snippets**: 3 different float speeds
- **Medallion glow**: Slow pulse
- **Scroll reveals**: Fade-in-up on intersection
- **Button hovers**: Scale + shadow
- **Checkbox check**: Scale pop-in
- **Progress bar**: Smooth width transition

## Tech Stack

- **Framework**: SvelteKit 2 with Svelte 5
- **Styling**: Tailwind CSS with arbitrary values
- **State**: Svelte 5 runes (`$state`, `$derived`)
- **Transitions**: Svelte built-in (`fly`, `fade`, `scale`)
- **Scroll detection**: IntersectionObserver
- **Fonts**: Google Fonts (Bitter, Source Sans 3)

## Pages

- **Homepage** (`/`) — The main recovery landing page with all sections
- **Diagnostic Quiz** (`/quiz`) — 10-question multiple choice test, calculates your "vibe coder" score and tier. Each answer has a unique snarky reaction that appears before advancing. Score is hidden until the end. Results include a 2-paragraph diagnosis, an "Rx" prescription, severity spectrum bar, and a "Share Diagnosis" clipboard button.
- **60 Seconds Sober** (`/game`) — Survival game: developer crisis scenarios appear one at a time, click the sober response to earn points. Click "Ask Claude" even once = instant game over. 15 randomized scenarios, streak multipliers, 60-second countdown timer.
- **The Confession Booth** (`/confessions`) — Anonymous confession wall. 14 pre-written funny confessions displayed as rotated "pinned note" cards in a masonry grid. Each card has a category tag (Prompt Abuse, Copy-Paste Crime, Identity Crisis, Dependency, Deploy Recklessness), days-sober badge (color-coded red/yellow/green), and a "Me Too" counter button. Filter by category. Submit your own confession — it gets a randomly generated anonymous username and appears at the top of the wall. Absolution toast notification on submit.

## Components

- **Navbar** (`src/lib/components/Navbar.svelte`) - Fixed top navigation bar with links to all 4 pages. Highlights the active page in orange. Collapses into a hamburger menu on mobile. Logo links back to the homepage.
- **CommitRoulette** (`src/lib/components/CommitRoulette.svelte`) - Generates random commit messages like "Replaced logic with vibes". Shows message in terminal-style display.
- **WeightTracker** (`src/lib/components/WeightTracker.svelte`) - Simulates scanning node_modules, shows "emotional weight" in GB with humorous equivalents. Red shake animation if over 1GB.
- **PanicButton** (`src/lib/components/PanicButton.svelte`) - Emergency button that opens documentation (Haskell, Rust, COBOL) and shows overlay with motivational message.
- **RubberDuck** (`src/lib/components/RubberDuck.svelte`) - Interactive rubber duck with debugging tips.

## Key Files

- `src/app.css` - Base styles and design tokens
- `src/routes/+layout.svelte` - Layout with fonts and Navbar
- `src/routes/+page.svelte` - Complete interactive homepage
- `src/routes/quiz/+page.svelte` - Diagnostic quiz page
- `src/routes/game/+page.svelte` - 60 Seconds Sober game page
- `src/routes/confessions/+page.svelte` - The Confession Booth page

## Customization

### Change medallion max days

```ts
daysSober = Math.floor(Math.random() * 4); // Change 4 to your max + 1
```

### Edit diagnosis messages

Modify the `getDiagnosisMessage()` function

### Add warning signs

Add to the `warningSigns` array

### Change colors

Search and replace hex codes (e.g., `#f97316` for orange accent)

## Disclaimer

This is a satirical website. AI coding tools are genuinely useful—the joke is about over-reliance without understanding, not about using AI itself.

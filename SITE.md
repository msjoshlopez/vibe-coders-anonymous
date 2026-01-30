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

- 3-column icon cards
- Color-coded hover effects (blue, green, orange)
- Scale animation on hover

### 8. CTA Section

- Orange gradient with dot pattern overlay
- Large headline with line break
- Prominent "Join a Meeting" button

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

## Key Files

- `src/app.css` - Base styles and design tokens
- `src/routes/+layout.svelte` - Layout with fonts
- `src/routes/+page.svelte` - Complete interactive homepage

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

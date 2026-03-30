# FOILIQ — COMPLETE REBUILD SPEC FOR CLAUDE CODE
*Hand this entire document to Claude Code. Do not summarize it.*

---

## OVERVIEW

Build FoilIQ — a premium AI-powered fencing analytics and coaching web application. This is a complete rebuild of the existing site at `aifenceguy.github.io/foiliq/`. The rebuilt app must be deployed to GitHub Pages as a single HTML file with embedded CSS and JavaScript, with all data stored in Supabase.

**Core positioning:** FoilIQ provides what coaches can't — continuous, personalized support between training sessions, at tournaments, and in every moment no coach is present. It is NOT a replacement for a coach. It extends the coach's impact across the 95% of a fencer's competitive life when no coach is present.

**Target users:** Competitive foil fencers in Y10, Y12, Y14 divisions and their families.

**Named fencers:** Raedyn (Y14) and Kaylan (Y12), competing on the SYC circuit at SoCAL Fencing Center.

---

## TECHNICAL ARCHITECTURE

- **Deployment:** GitHub Pages — single HTML file (`index.html`) with all CSS and JS embedded
- **Database:** Supabase (existing project — credentials will be provided by the user at runtime)
- **AI:** Anthropic Claude API (claude-sonnet-4-20250514) called directly from the browser
- **No backend server** — all calls are client-side (Supabase JS client + fetch to Anthropic API)
- **No frameworks** — vanilla HTML/CSS/JS only. Use `var` and traditional `function` declarations throughout (no `const`/`let`, no arrow functions at the top level) to avoid fatal redeclaration bugs in single-file browser apps
- **localStorage** — used only for API key and user session; all fencer data goes to Supabase

---

## DESIGN SYSTEM

### Aesthetic Direction
**Premium dark sports analytics** — think ESPN meets Hudl meets a Bloomberg terminal. Dense, data-rich, serious. Every element signals performance and precision. This is a tool for athletes who want to win, not a hobby app.

### Colors
```css
--bg-primary: #0a0f1e;        /* deep navy */
--bg-secondary: #0f1629;      /* slightly lighter navy */
--bg-card: #131d35;           /* card backgrounds */
--bg-elevated: #1a2540;       /* elevated elements */
--accent-gold: #f0b429;       /* primary gold accent */
--accent-gold-dim: #c49320;   /* dimmed gold */
--accent-blue: #3d7ef5;       /* electric blue secondary */
--text-primary: #f0f4ff;      /* near white */
--text-secondary: #8899bb;    /* muted blue-grey */
--text-muted: #4a5568;        /* very muted */
--success: #22c55e;
--danger: #ef4444;
--warning: #f0b429;
--border: rgba(255,255,255,0.06);
```

### Typography
- **Display/Headers:** `Bebas Neue` (Google Fonts) — bold, athletic, commanding
- **Body/UI:** `DM Sans` (Google Fonts) — clean, readable, modern
- **Data/Numbers:** `JetBrains Mono` (Google Fonts) — monospaced for KPI values

### Layout
- Dense, dashboard-heavy
- Card-based grid layout
- Sidebar navigation on desktop, bottom tab bar on mobile
- Responsive — equal priority mobile and desktop
- Dark overlays, subtle gradients, gold borders on active states

### Logo
- Sword/foil icon (SVG, draw it inline) + "FoilIQ" wordmark in Bebas Neue
- Gold color for the icon, white for the wordmark
- "SMART FENCING ANALYTICS" tagline in small caps below

### Motion
- Subtle entrance animations on cards (fade + slide up)
- Gold pulse on active nav items
- Smooth transitions between sections (no page reloads — single page app)

---

## APP STRUCTURE

### Landing Page (shown on first load)
Two large doors — full screen, dramatic layout:

**Left door — ⚔ FENCER**
- Subtitle: "Game plans. Bout analysis. Decision training."
- CTA button: "Enter Fencer Dashboard"
- Clicking this enters the Fencer Sector

**Right door — 🏅 PARENT**
- Subtitle: "Tournament intelligence. Coming Soon."
- Brief value prop: "Stop chasing the wrong competitions. We'll tell you exactly which events move the needle for your child's level and region."
- Email capture field: "Get notified when Parent Sector launches"
- Grayed out / coming soon treatment

---

## FENCER SECTOR

### Navigation
Sidebar on desktop, bottom bar on mobile. Items:

1. 🏠 **Dashboard** — KPI overview + today's focus
2. 👤 **My Profile** — personal fencer profile
3. 🎯 **Pre-Bout** — game plan builder
4. ⏱ **Break** — mid-bout advisor
5. 📋 **Post-Bout** — debrief logger
6. 🏋 **Practice** — practice planner
7. 🧠 **Decision Training** — scenario drills
8. 🎯 **Opponents** — opponent profiles
9. 📊 **History** — match log
10. ⚙ **Settings** — API keys, fencer selection

Fencer selector at the top of sidebar: toggle between Raedyn and Kaylan.

---

## SCREENS — DETAILED SPEC

### 1. DASHBOARD
The home screen. Dense data layout.

**Top row — KPI cards (7 cards in a grid):**
- Win Rate — percentage + trend arrow
- Control Rate — percentage of bouts where fencer was controlling
- Action Accuracy — % correct action for situation
- Debrief Consistency — % of bouts with post-bout debrief logged
- Decision Drill Level — current level badge (Beginner / Intermediate / Advanced)
- Touch Differential — average score diff per bout (+ or -)
- Tournament Trend — sparkline chart of last 6 tournament results

Each KPI card: large number, label, trend indicator (up/down vs last month).

**Middle section — Today's Focus:**
- "THIS WEEK'S TARGETS" — pulled from fencer_profiles.weekly_targets
- 1-3 bullet focus areas
- "TODAY'S PRACTICE GOAL" — single sentence from latest training_plans.daily_session_goal

**Bottom section — Recent Activity:**
- Last 5 match logs (date, opponent, result, score)
- Last AI insight generated

---

### 2. MY PROFILE
Personal fencer profile. The foundation that feeds all AI features.

**Fields:**
- Name, division, club, handedness (read-only, set at setup)
- Long-term goals — text area (e.g. "Top 8 at SYC, qualify for Summer Nationals")
- Current strengths — multi-select chips + free text
- Current weaknesses — multi-select chips + free text
- Weekly targets — 3 editable text fields (Target 1, 2, 3)
- Season — text field (e.g. "2025 Spring")

**AI-generated profile summary:** A 3-sentence AI-written coaching summary based on the profile data. "Based on your profile, here's your current competitive identity..." Regenerate button.

Save button writes to `fencer_profiles` table.

---

### 3. PRE-BOUT
Game plan builder before a bout starts.

**Step 1 — Select or create opponent**
- Dropdown of existing opponents from DB
- Or quick-add: name + opponent type

**Step 2 — Confirm opponent type**
- Comes Forward Fast / Keeps Backing Up / Waits to Hit You / Counter Attacks You / Not Doing Much

**Step 3 — Quick SWOT reminder**
- Pull from opponent profile if it exists
- 4 quadrants displayed compactly

**Step 4 — AI generates game plan**
- Large AI output area
- Prompt context: fencer profile, opponent SWOT, opponent type, fencer's current weaknesses
- Game plan = 3 sections: Opening tactic, Mid-bout adjustment trigger, Mental cue
- Copy to clipboard button

---

### 4. BREAK ADVISOR
Score-aware mid-bout adjustment. Designed for use in the 1-minute break.

**Inputs (large touch targets — this is used under pressure):**
- Current score: Us ___ / Them ___
- Period: 1st / 2nd / 3rd
- What's happening: Working well / They're dominating / Even fight / I'm nervous
- Opponent type (pre-filled if selected earlier)

**Output:**
- 3 bullet points MAX — short, direct, actionable
- No paragraphs. This is a 60-second break.
- Bold the single most important instruction

---

### 5. POST-BOUT DEBRIEF
Captures what happened while memory is fresh.

**Fields:**
- Link to match (select from today's logged matches or log new one inline)
- What worked (free text)
- What failed (free text)  
- Key pattern noticed (free text)
- Emotional state: Focused / Nervous / Confident / Frustrated / Checked Out
- One thing to fix before next bout (free text)

**AI debrief analysis:**
After saving, AI generates:
- Pattern detected (cross-references with last 5 debriefs from DB)
- One tactical adjustment recommendation
- One practice drill suggestion

Saves to `debriefs` table. Triggers KPI recalculation.

---

### 6. PRACTICE PLANNER
Solves "empty mind at practice" problem.

**Two views:**

**Weekly Plan view:**
- "THIS WEEK'S PRACTICE FOCUS" — AI-generated based on recent debrief patterns
- 3 focus areas with brief explanation of why each matters now
- How to approach each when the coach runs: drills / situation play / free fencing
- Regenerate button (calls AI with latest debrief data)

**Today's Session card:**
- Single bold headline: "TODAY'S MISSION"
- One tactical goal for today's practice in one sentence
- Example: "In every free fencing bout today, focus on removing the blade before attacking. No attacks without preparation."
- Share button (copy to clipboard to show coach)

Saves to `training_plans` table.

---

### 7. DECISION TRAINING MODULE
Scenario-based tactical drills. Game-like, fast, phone-friendly.

**Home screen:**
- Current level badge (Beginner / Intermediate / Advanced)
- Progress bar within current level (X/10 sessions passed)
- Best streak
- "START SESSION" button — large, gold

**Session screen (5 cards per session):**

Each card shows:
```
PERIOD 2 — SCORE: DOWN 2-3
OPPONENT TYPE: Comes Forward Fast
LAST ACTION: You attacked, got countered

What do you do next?

[A] Attack again immediately
[B] Make them go first — parry-riposte
[C] Back up and reset
```

On answer:
- Correct → green flash + explanation (2-3 sentences max)
- Wrong → red flash + "You chose X. The right answer is Y because..."
- Next card auto-advances after 2 seconds

**End of session:**
- Score: X/5
- 4-5 correct → "LEVEL PASSED" gold animation + unlock message
- Under 4 → "Try again — here's what to focus on"

**Level progression:**
- Beginner: Single variable scenarios (just opponent type)
- Intermediate: Two variables (opponent type + score)  
- Advanced: Full context (opponent type + score + period + prior action)

**Scenario generation:**
- Scenarios are AI-generated and pulled from `drill_scenarios` table
- If fewer than 5 scenarios available for current level, call Claude API to generate 10 more
- Scenarios personalized to this fencer's logged weaknesses from their profile

---

### 8. OPPONENTS
Opponent profile manager.

**List view:**
- Cards showing name, club, division, last fenced date, win/loss record vs this opponent
- Search/filter by name, division
- "+ Add Opponent" button

**Profile view:**
- All fields: name, club, country, age group, handedness, height/reach, fencing style
- Preferred actions (multi-select chips)
- FencingTracker URL import button — fetch and auto-fill name/club/division
- SWOT grid (4 quadrants):
  - My Strengths vs Them
  - My Weaknesses to Hide
  - Their Weaknesses to Exploit
  - Their Strengths — Avoid
- Each quadrant: pre-set chips + free text
- Additional notes
- Match history vs this opponent (pulled from match_logs)

---

### 9. MATCH HISTORY
Full match log.

**Filters:** Fencer (Raedyn/Kaylan/All), Type (Practice/Tournament/All), Result (Win/Loss/All), Date range

**Log entry fields:**
- Opponent name
- Date
- Match type (Practice / Tournament)
- Opponent type
- Control metric (I was controlling / Opponent controlling / Not sure)
- Action used (Test / Make Them Go First / Go Attack / Prepare)
- What happened (multi-select: Good execution / Backed up too much / Attacked too early / Without setup / Hesitated / Got countered / Too far distance)
- Result (Win/Loss)
- Score (Us / Them)
- Notes
- Coach notes

**Stats bar at top:** Total bouts, Win rate, Avg touch differential

---

### 10. SETTINGS
- Fencer management (add/edit Raedyn and Kaylan profiles)
- AI Provider toggle: Claude (Anthropic) / GPT-4o (OpenAI)
- API key inputs (stored in localStorage, never sent to FoilIQ servers)
- Supabase URL + anon key inputs
- "Test connection" button for both AI and Supabase

---

## SUPABASE DATABASE SCHEMA

Create all tables via the Supabase JS client on first load if they don't exist (or provide SQL migration). Schema:

```sql
-- Fencer profiles
CREATE TABLE fencers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  division TEXT CHECK (division IN ('Y10','Y12','Y14')),
  club TEXT,
  handedness TEXT CHECK (handedness IN ('right','left')),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE fencer_profiles (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  long_term_goals TEXT,
  strengths TEXT,
  weaknesses TEXT,
  weekly_targets JSONB,
  season TEXT,
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Match data
CREATE TABLE match_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  opponent_id UUID REFERENCES opponents(id),
  opponent_name TEXT,
  date DATE NOT NULL,
  match_type TEXT CHECK (match_type IN ('practice','tournament')),
  opponent_type TEXT,
  control_metric TEXT,
  action_used TEXT,
  what_happened JSONB,
  result TEXT CHECK (result IN ('win','loss')),
  score_us INTEGER,
  score_them INTEGER,
  notes TEXT,
  coach_notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE debriefs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  match_log_id UUID REFERENCES match_logs(id),
  what_worked TEXT,
  what_failed TEXT,
  key_pattern TEXT,
  emotion_state TEXT,
  next_focus TEXT,
  ai_analysis TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Opponents
CREATE TABLE opponents (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  name TEXT NOT NULL,
  club TEXT,
  country TEXT,
  age_group TEXT,
  handedness TEXT,
  height_reach TEXT,
  fencing_style TEXT,
  preferred_actions JSONB,
  swot_my_strengths JSONB,
  swot_my_weaknesses JSONB,
  swot_their_weaknesses JSONB,
  swot_their_strengths JSONB,
  fencingtracker_url TEXT,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Training
CREATE TABLE training_plans (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  week_of DATE,
  focus_areas JSONB,
  daily_session_goal TEXT,
  generated_by_ai BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Decision training
CREATE TABLE drill_scenarios (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  difficulty TEXT CHECK (difficulty IN ('beginner','intermediate','advanced')),
  period TEXT,
  score_context TEXT,
  opponent_type TEXT,
  last_action TEXT,
  option_a TEXT,
  option_b TEXT,
  option_c TEXT,
  correct_option TEXT CHECK (correct_option IN ('a','b','c')),
  explanation TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE drill_sessions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  difficulty TEXT,
  scenarios_attempted INTEGER,
  correct_count INTEGER,
  passed BOOLEAN,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- KPIs
CREATE TABLE kpi_snapshots (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  snapshot_date DATE,
  win_rate DECIMAL,
  control_rate DECIMAL,
  action_accuracy DECIMAL,
  debrief_consistency DECIMAL,
  drill_level TEXT,
  touch_differential DECIMAL,
  tournament_trend JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE fencer_insights (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  insight_type TEXT,
  insight_text TEXT,
  source_data JSONB,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Agent / notifications
CREATE TABLE agent_log (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  module TEXT,
  trigger_type TEXT,
  action_taken TEXT,
  result TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE notifications_sent (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  fencer_id UUID REFERENCES fencers(id),
  notification_type TEXT,
  channel TEXT DEFAULT 'email',
  subject TEXT,
  body TEXT,
  sent_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## AI PROMPT SYSTEM

All AI calls use Claude claude-sonnet-4-20250514. Each feature has a system prompt:

### Global system context (prepend to all prompts):
```
You are FoilIQ, an AI coaching assistant for competitive foil fencing. 
You provide what coaches can't — support between sessions, at tournaments, 
and in moments no coach is present. You are NOT a replacement for a coach.
Your fencer is [NAME], competing in [DIVISION] at SoCAL Fencing Center 
on the SYC circuit. Their profile: strengths = [STRENGTHS], 
weaknesses = [WEAKNESSES], weekly targets = [TARGETS].
Be direct, tactical, and brief. No fluff. Fencers need actionable guidance.
Use foil-specific terminology. Reference the Sun Tzu SWOT framework.
```

### Pre-Bout system prompt addition:
```
Generate a game plan in 3 sections:
1. OPENING TACTIC (2 sentences max)
2. MID-BOUT TRIGGER — when to switch tactics (1 sentence)
3. MENTAL CUE — one phrase to repeat under pressure
Base this on the opponent SWOT and the fencer's current weaknesses.
```

### Break Advisor system prompt addition:
```
The fencer is in a 1-minute break. Be extremely brief.
Output exactly 3 bullet points. Bold the most critical one.
No paragraphs. Direct commands only.
Score context: [SCORE]. Period: [PERIOD]. Situation: [SITUATION].
```

### Post-Bout system prompt addition:
```
Analyze this debrief against the last 5 debriefs for this fencer.
Output 3 sections:
1. PATTERN DETECTED (1 sentence — what's recurring)
2. TACTICAL FIX (1 sentence — what to change in the next bout)
3. PRACTICE DRILL (1 sentence — what to drill at next practice)
```

### Practice Planner system prompt addition:
```
Based on the last 5 debriefs, generate a weekly practice focus.
Output:
- 3 focus areas (each with a title and 1-sentence explanation)
- For each focus area, one sentence on how to apply it during: drills, situation play, free fencing
- TODAY'S MISSION: one single sentence the fencer memorizes before practice
Be specific to foil. Reference real drills and actions.
```

### Decision Training scenario generation:
```
Generate 10 decision training scenarios for a [DIFFICULTY] level foil fencer.
This fencer's weaknesses are: [WEAKNESSES].
For each scenario output JSON:
{
  "period": "1st|2nd|3rd",
  "score_context": "winning|losing|tied|close",
  "opponent_type": "comes_forward|backs_up|waits|counter|passive",
  "last_action": "brief description",
  "option_a": "action description",
  "option_b": "action description", 
  "option_c": "action description",
  "correct_option": "a|b|c",
  "explanation": "2-3 sentence explanation of why"
}
Difficulty guidelines:
- Beginner: opponent type is the only variable
- Intermediate: opponent type + score context
- Advanced: all variables including period and last action
Output ONLY the JSON array. No preamble.
```

---

## FOIL DECISION GUIDE (reference data — hardcoded)

```
Comes Forward Fast → Make Them Go First
  Let them commit → parry → riposte. Never attack into their pressure.

Keeps Backing Up → Go Attack  
  Chase, pressure, commit to score. Don't let them reset.

Waits to Hit You → Fake, then Go
  Draw the counter-attack → parry → attack. They are baiting you.

Counter Attacks You → Prepare first — remove the angle
  Never attack open. Setup → remove their blade → then commit.

Not Doing Much → Take Control
  Test → dominate center → force reaction. You set the tempo.

SWOT principle: Use YOUR strengths to hit THEIR weaknesses. 
Hide YOUR weaknesses. Don't let them use their strengths against you.

Every forward move must be: Test → Prepare → Make Them Go First → Go Attack
```

---

## FIRST RUN EXPERIENCE

On first load (no Supabase credentials in localStorage):
1. Show settings modal immediately
2. Prompt for Supabase URL, anon key, and AI API key
3. On save, test connection to both
4. If connection succeeds, check if `fencers` table has Raedyn and Kaylan
5. If not, seed them automatically:
   - Raedyn: Y14, SoCAL Fencing Center, right-handed
   - Kaylan: Y12, SoCAL Fencing Center, right-handed
6. Redirect to landing page

---

## PERFORMANCE REQUIREMENTS

- App must load and be interactive in under 3 seconds
- All Supabase reads must show loading skeletons (not blank screens)
- All AI calls must show a loading state with a relevant message ("Generating game plan...", "Analyzing bout patterns...", etc.)
- Error states must be handled gracefully — if AI call fails, show the error and a retry button
- All forms must validate before submission

---

## AUTHENTICATION

Use **Supabase Auth** — already built into the Supabase JS client. No extra service needed.

### Login Methods
- Email + password
- Google OAuth (via Supabase Google provider)

### Registration
- Open — anyone can sign up
- On first sign up, create a `user_profiles` record linked to the Supabase auth user

### Auth Flow
1. App loads → check Supabase session
2. No session → show Login/Register page (full screen, before landing page)
3. Session exists → show landing page (Fencer / Parent doors)
4. All Supabase data queries must include `user_id` filter — users can only see their own data

### Auth Pages

**Login Page:**
- FoilIQ logo + tagline centered
- Email + password fields
- "Sign in with Google" button
- "Don't have an account? Register" link
- Dark blue design matching app theme

**Register Page:**
- Name, email, password, confirm password
- "Sign up with Google" button
- "Already have an account? Login" link
- On success → redirect to profile setup (fencer name, division, club)

**Profile Setup (first login only):**
- "Set up your fencer profile"
- Fields: fencer name, division (Y10/Y12/Y14), club, handedness
- Option to add a second fencer (for families with multiple kids like Raedyn + Kaylan)
- On complete → seed fencer records in DB, redirect to landing page

### Database Changes for Auth

```sql
-- Add user_id to all fencer-linked tables
ALTER TABLE fencers ADD COLUMN user_id UUID REFERENCES auth.users(id);

-- User profiles table
CREATE TABLE user_profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  display_name TEXT,
  email TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Supabase Row Level Security (RLS)
Enable RLS on all tables. Each user can only read/write their own data:
```sql
-- Example policy (apply to all tables with fencer_id)
CREATE POLICY "Users can only access their own fencers"
ON fencers FOR ALL
USING (user_id = auth.uid());
```

### Seed Data for Ricky
After Ricky creates his account, the app automatically seeds Raedyn (Y14) and Kaylan (Y12) as his fencers if no fencers exist for his user_id.

---

## WHAT NOT TO BUILD

- No authentication/login system (out of scope for now)
- No Parent Sector functionality (placeholder only)
- No backend server
- No payment/subscription system
- No live scoring
- No FoilIQ Pro features (Cadet/Junior, sport science, AI vision)

---

## DELIVERABLE

A single `index.html` file with all CSS and JS embedded, ready to commit to the `aifenceguy/foiliq` GitHub Pages repository. The file must work when opened directly in a browser with no build step required.

---

*End of spec. Build everything in this document. When in doubt, refer back to the design system and product positioning: premium dark sports analytics for competitive Y10-Y14 foil fencers.*

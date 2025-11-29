# 📚 Study Buddy - Complete Project Analysis

## 1. Project Overview

### Purpose
**Study Buddy** is an AI-powered study and productivity platform designed to help students learn more effectively. Think of it as a smart study assistant that:
- Takes your notes and creates summaries automatically
- Generates quizzes to test your knowledge
- Creates flashcards for memorization
- Tracks your learning progress

### Main Features
1. **Smart Notes** - Create, organize, and manage study notes
2. **AI Summaries** - Automatically generate concise summaries from your notes
3. **Interactive Quizzes** - AI-generated multiple-choice questions from your notes
4. **Flashcards** - Spaced repetition flashcards for effective memorization
5. **Progress Tracking** - Analytics, streaks, and achievement badges
6. **User Profiles** - Personalized learning experience

### Tech Stack & Why Each Was Chosen

| Technology | Purpose | Why Chosen |
|------------|---------|------------|
| **React 18 + TypeScript** | Frontend framework | Modern, type-safe, component-based UI development |
| **Vite** | Build tool | Fast development server and optimized production builds |
| **Tailwind CSS** | Styling | Utility-first CSS for rapid, consistent UI development |
| **Supabase** | Backend & Database | Provides PostgreSQL database, authentication, and serverless functions in one platform |
| **TanStack Query** | Data fetching | Handles caching, loading states, and data synchronization automatically |
| **React Router** | Navigation | Client-side routing for single-page application navigation |
| **Radix UI + shadcn/ui** | UI components | Accessible, customizable component library |
| **Google Gemini 2.5 Flash** | AI Model | Fast, cost-effective AI for generating summaries, quizzes, and flashcards |
| **Deno** | Edge Functions runtime | Secure, modern runtime for serverless functions |

---

## 2. Frontend Explanation

### File Structure

```
src/
├── App.tsx                    # Main app component - sets up routing and providers
├── main.tsx                   # Entry point - renders App to DOM
├── index.css                  # Global styles and design system
├── vite-env.d.ts             # TypeScript definitions for Vite
│
├── pages/                     # Page components (routes)
│   ├── Auth.tsx              # Login/signup page
│   ├── Dashboard.tsx         # Main dashboard with stats
│   ├── Notes.tsx             # List of all notes
│   ├── NoteDetail.tsx        # View/edit single note + AI features
│   ├── Quizzes.tsx           # List of all quizzes
│   ├── QuizPlay.tsx          # Play a quiz
│   ├── Flashcards.tsx        # List of flashcard sets
│   ├── FlashcardStudy.tsx    # Study flashcards
│   ├── Profile.tsx           # User profile and settings
│   └── NotFound.tsx          # 404 error page
│
├── components/
│   ├── dashboard/            # Dashboard-specific components
│   │   ├── Sidebar.tsx       # Navigation sidebar
│   │   ├── DashboardLayout.tsx # Layout wrapper
│   │   ├── WelcomeBanner.tsx # Welcome message
│   │   ├── StatsCards.tsx    # Statistics cards
│   │   ├── RecentNotes.tsx   # Recent notes widget
│   │   ├── PerformanceChart.tsx # Performance graph
│   │   ├── StudyStreakTracker.tsx # Streak counter
│   │   └── AchievementBadges.tsx # Achievement display
│   │
│   └── ui/                   # Reusable UI components (50+ components)
│       ├── button.tsx
│       ├── card.tsx
│       ├── dialog.tsx
│       ├── glass-card.tsx    # Glassmorphism card
│       └── ... (many more)
│
├── hooks/                     # Custom React hooks
│   ├── use-toast.ts          # Toast notification hook
│   └── use-mobile.tsx        # Mobile detection hook
│
├── lib/
│   └── utils.ts              # Utility functions (cn for className merging)
│
└── integrations/
    └── supabase/
        ├── client.ts         # Supabase client setup
        └── types.ts          # Database type definitions
```

### Component Purpose & Interactions

#### **App.tsx** - The Root Component
```typescript
// Sets up the entire app structure:
1. QueryClientProvider - Manages API data caching
2. ThemeProvider - Handles dark/light mode
3. BrowserRouter - Handles page navigation
4. Routes - Defines all page routes
```

**Flow**: User opens app → App.tsx loads → Checks authentication → Routes to appropriate page

#### **Pages Explained**

1. **Auth.tsx** - Login/Signup
   - User enters email/password
   - Calls `supabase.auth.signInWithPassword()` or `signUp()`
   - On success → navigates to `/dashboard`

2. **Dashboard.tsx** - Home Screen
   - Shows welcome message
   - Displays stats (notes count, quizzes, flashcards)
   - Shows recent notes
   - Displays performance charts
   - Shows study streak and achievements

3. **Notes.tsx** - Notes List
   - Fetches all user notes using `useQuery`
   - Filter options: All, With Summary, Recent
   - "Create Note" button opens dialog
   - Clicking a note navigates to `/notes/:id`

4. **NoteDetail.tsx** - Single Note View
   - Shows note content
   - "Generate Summary" button → calls AI function
   - "Generate Quiz" button → creates quiz from note
   - "Generate Flashcards" button → creates flashcards

5. **Quizzes.tsx** - Quiz List
   - Lists all quizzes
   - Shows completion status
   - Click to play a quiz

6. **QuizPlay.tsx** - Quiz Interface
   - Shows one question at a time
   - User selects answer
   - Calculates score at end
   - Saves attempt to database

7. **Flashcards.tsx** - Flashcard Sets
   - Lists flashcard sets grouped by source note
   - Shows progress for each set
   - Click to study a set

8. **FlashcardStudy.tsx** - Study Interface
   - Shows flashcard with flip animation
   - User rates difficulty (Easy/Medium/Hard)
   - Uses spaced repetition algorithm
   - Updates next review date

### UI Flow Diagram

```
User Opens App
    ↓
Auth Check (App.tsx)
    ↓
┌─────────────────┐
│  Not Logged In  │ → Auth.tsx (Login/Signup)
└─────────────────┘
    ↓
┌─────────────────┐
│   Logged In     │ → Dashboard.tsx
└─────────────────┘
    ↓
Sidebar Navigation
    ├─→ Notes.tsx → NoteDetail.tsx
    │                    ├─→ Generate Summary (AI)
    │                    ├─→ Generate Quiz (AI)
    │                    └─→ Generate Flashcards (AI)
    │
    ├─→ Quizzes.tsx → QuizPlay.tsx
    │
    ├─→ Flashcards.tsx → FlashcardStudy.tsx
    │
    └─→ Profile.tsx
```

### State Management

**TanStack Query (React Query)** handles all server state:
- **useQuery**: Fetches and caches data
  ```typescript
  const { data: notes } = useQuery({
    queryKey: ["notes"],
    queryFn: async () => {
      // Fetch notes from Supabase
    }
  });
  ```
- **useMutation**: Modifies data (create, update, delete)
  ```typescript
  const createNote = useMutation({
    mutationFn: async (newNote) => {
      // Insert into database
    },
    onSuccess: () => {
      // Refresh notes list
    }
  });
  ```

**React useState**: Handles local UI state (form inputs, modals, etc.)

### Routing

**React Router v6** handles navigation:
- `/` → Redirects to `/auth`
- `/auth` → Login/Signup page
- `/dashboard` → Main dashboard
- `/notes` → Notes list
- `/notes/:id` → Single note detail
- `/quizzes` → Quiz list
- `/quizzes/:id` → Play quiz
- `/flashcards` → Flashcard sets
- `/flashcards/study/:id` → Study flashcards
- `/profile` → User profile
- `*` → 404 page

### API Calls Flow

```
User Action (e.g., "Generate Summary")
    ↓
Frontend: NoteDetail.tsx
    ↓
supabase.functions.invoke("summarize-note", { noteId, content })
    ↓
Backend: Edge Function (Deno)
    ↓
Calls AI API (Google Gemini)
    ↓
AI Returns Summary
    ↓
Backend Saves to Database
    ↓
Returns Summary to Frontend
    ↓
Frontend Updates UI (shows typing animation)
```

---

## 3. Backend Explanation

### Backend Structure

```
supabase/
├── functions/                 # Serverless Edge Functions (Deno)
│   ├── summarize-note/
│   │   └── index.ts          # Generates AI summary
│   ├── generate-quiz/
│   │   └── index.ts          # Generates quiz questions
│   └── generate-flashcards/
│       └── index.ts          # Generates flashcards
│
├── migrations/                # Database schema
│   └── 20251119172909_remix_migration_from_pg_dump.sql
│
└── config.toml              # Supabase configuration
```

### Edge Functions Explained

#### 1. **summarize-note/index.ts**

**Purpose**: Takes a note's content and generates a concise summary using AI

**Input**:
```typescript
{
  noteId: "uuid",
  content: "Full note text..."
}
```

**Process**:
1. Receives note content from frontend
2. Calls AI API (Google Gemini) with prompt: "Summarize into 5-7 bullet points"
3. AI returns summary text
4. Updates `notes` table with summary
5. Returns summary to frontend

**Output**:
```typescript
{
  summary: "• Point 1\n• Point 2\n..."
}
```

**Code Flow**:
```typescript
serve(async (req) => {
  // 1. Get noteId and content from request
  const { noteId, content } = await req.json();
  
  // 2. Call AI API
  const response = await fetch('https://ai.gateway.lovable.dev/v1/chat/completions', {
    method: 'POST',
    body: JSON.stringify({
      model: 'google/gemini-2.5-flash',
      messages: [
        { role: 'system', content: 'You are a concise academic summarizer...' },
        { role: 'user', content: `Summarize: ${content}` }
      ]
    })
  });
  
  // 3. Extract summary from AI response
  const summary = response.choices[0].message.content;
  
  // 4. Save to database
  await supabase.from('notes').update({ summary }).eq('id', noteId);
  
  // 5. Return to frontend
  return new Response(JSON.stringify({ summary }));
});
```

#### 2. **generate-quiz/index.ts**

**Purpose**: Creates multiple-choice quiz questions from note content

**Input**:
```typescript
{
  noteId: "uuid",
  content: "Full note text...",
  title: "Note title"
}
```

**Process**:
1. Analyzes content to determine optimal question count (20-30 questions)
2. Creates detailed prompt for AI
3. Calls AI API to generate questions
4. Validates question structure
5. Creates quiz record in database
6. Returns quiz ID

**Key Function - `calculateOptimalQuestionCount()`**:
```typescript
// Analyzes content to determine how many questions to generate
function calculateOptimalQuestionCount(content: string): number {
  const wordCount = content.split(/\s+/).length;
  const conceptIndicators = content.match(/\b(define|concept|principle)\b/gi)?.length || 0;
  
  // Base: 1 question per 50 words
  let questionCount = Math.max(20, Math.floor(wordCount / 50));
  
  // Bonus for rich content
  if (conceptIndicators > 3) questionCount += Math.floor(conceptIndicators / 2);
  
  // Cap between 20-30
  return Math.min(30, Math.max(20, questionCount));
}
```

**Output**:
```typescript
{
  quizId: "uuid",
  questionCount: 25
}
```

#### 3. **generate-flashcards/index.ts**

**Purpose**: Creates Q&A flashcards from note content

**Input**:
```typescript
{
  noteId: "uuid",
  content: "Full note text..."
}
```

**Process**:
1. Calls AI API to generate 8 flashcards
2. AI returns JSON array: `[{front: "Q", back: "A"}, ...]`
3. Gets authenticated user ID
4. Inserts flashcards into database
5. Returns count

**Output**:
```typescript
{
  count: 8
}
```

### API Structure

All Edge Functions follow this pattern:

```typescript
serve(async (req) => {
  // 1. Handle CORS preflight
  if (req.method === 'OPTIONS') {
    return new Response(null, { headers: corsHeaders });
  }
  
  // 2. Get request data
  const { noteId, content } = await req.json();
  
  // 3. Call AI API
  const aiResponse = await fetch('https://ai.gateway.lovable.dev/...');
  
  // 4. Process AI response
  const result = await aiResponse.json();
  
  // 5. Save to database
  const supabase = createClient(SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY);
  await supabase.from('table').insert/update(...);
  
  // 6. Return response
  return new Response(JSON.stringify({ result }), {
    headers: { ...corsHeaders, 'Content-Type': 'application/json' }
  });
});
```

### Authentication & Authorization

**Supabase Auth** handles all authentication:
- User signs up/logs in → Supabase creates session
- Session token stored in browser localStorage
- Every API call includes token in `Authorization` header
- Edge Functions verify token:
  ```typescript
  const authHeader = req.headers.get('Authorization');
  const token = authHeader.replace('Bearer ', '');
  const { data: { user } } = await supabase.auth.getUser(token);
  ```

**Row Level Security (RLS)** in database ensures users can only access their own data.

---

## 4. Database Explanation

### Database Tables

#### 1. **notes** - Stores user notes

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key (auto-generated) |
| `user_id` | UUID | Foreign key to auth.users |
| `title` | TEXT | Note title |
| `content` | TEXT | Full note content |
| `summary` | TEXT | AI-generated summary (nullable) |
| `created_at` | TIMESTAMP | When note was created |
| `updated_at` | TIMESTAMP | Last update time |

**Sample Queries**:
```sql
-- Get all notes for a user
SELECT * FROM notes WHERE user_id = 'user-uuid' ORDER BY created_at DESC;

-- Update note summary
UPDATE notes SET summary = 'New summary' WHERE id = 'note-uuid';

-- Create new note
INSERT INTO notes (user_id, title, content) 
VALUES ('user-uuid', 'My Note', 'Content here');
```

#### 2. **quizzes** - Stores generated quizzes

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key |
| `user_id` | UUID | Owner of quiz |
| `note_id` | UUID | Source note (nullable) |
| `title` | TEXT | Quiz title |
| `questions` | JSONB | Array of question objects |
| `created_at` | TIMESTAMP | Creation time |

**Questions JSON Structure**:
```json
[
  {
    "question": "What is React?",
    "options": ["A library", "A framework", "A language", "A database"],
    "answer_index": 0,
    "explanation_short": "React is a JavaScript library..."
  }
]
```

#### 3. **flashcards** - Stores flashcards with spaced repetition data

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key |
| `user_id` | UUID | Owner |
| `note_id` | UUID | Source note (nullable) |
| `front` | TEXT | Question side |
| `back` | TEXT | Answer side |
| `next_review` | TIMESTAMP | When to review next |
| `interval_days` | INTEGER | Days until next review |
| `ease_factor` | FLOAT | Difficulty multiplier (default 2.5) |
| `repetitions` | INTEGER | Times reviewed correctly |
| `created_at` | TIMESTAMP | Creation time |

**Spaced Repetition Algorithm**:
- Easy answer → Increase interval, increase ease factor
- Medium answer → Moderate interval increase
- Hard answer → Small interval increase, decrease ease factor

#### 4. **quiz_attempts** - Tracks quiz completions

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key |
| `user_id` | UUID | User who took quiz |
| `quiz_id` | UUID | Foreign key to quizzes |
| `score` | INTEGER | Number correct |
| `total_questions` | INTEGER | Total questions |
| `answers` | JSONB | Array of selected answer indices |
| `completed_at` | TIMESTAMP | When completed |

#### 5. **profiles** - User profile data

| Column | Type | Description |
|--------|------|-------------|
| `id` | UUID | Primary key |
| `user_id` | UUID | Foreign key to auth.users |
| `display_name` | TEXT | User's display name |
| `points` | INTEGER | Gamification points |
| `streak_days` | INTEGER | Current study streak |
| `created_at` | TIMESTAMP | Account creation |
| `updated_at` | TIMESTAMP | Last update |

### Relationships

```
auth.users (Supabase Auth)
    ↓ (1:1)
profiles
    ↓ (1:many)
notes ──→ (1:many) ──→ quizzes
    │                    ↓
    │              quiz_attempts
    │
    └──→ (1:many) ──→ flashcards
```

**Key Relationships**:
- One user has many notes
- One note can generate one quiz
- One quiz can have many attempts
- One note can generate many flashcards

### CRUD Operations Flow

**Create Note**:
```
Frontend: Notes.tsx
    ↓
supabase.from('notes').insert({ user_id, title, content })
    ↓
Database: INSERT INTO notes ...
    ↓
Returns: { id, title, content, created_at }
    ↓
Frontend: Navigate to /notes/:id
```

**Read Notes**:
```
Frontend: Notes.tsx
    ↓
useQuery(['notes'], () => supabase.from('notes').select('*'))
    ↓
Database: SELECT * FROM notes WHERE user_id = ?
    ↓
Returns: Array of notes
    ↓
Frontend: Displays in list
```

**Update Note Summary**:
```
Frontend: NoteDetail.tsx → "Generate Summary"
    ↓
Edge Function: summarize-note
    ↓
AI API: Generates summary
    ↓
Database: UPDATE notes SET summary = ? WHERE id = ?
    ↓
Frontend: Shows summary with typing animation
```

**Delete Note**:
```
Frontend: NoteDetail.tsx → Delete button
    ↓
supabase.from('notes').delete().eq('id', noteId)
    ↓
Database: DELETE FROM notes WHERE id = ?
    ↓
Frontend: Navigate to /notes
```

---

## 5. AI Module Explanation

### AI Model Used

**Google Gemini 2.5 Flash** - A fast, efficient language model optimized for quick responses.

**Why This Model?**
- Fast response times (important for good UX)
- Cost-effective for high-volume usage
- Good at following structured prompts
- Handles academic/scientific content well

### AI Integration Architecture

```
User Action
    ↓
Frontend Component
    ↓
Supabase Edge Function (Deno)
    ↓
AI Gateway API (https://ai.gateway.lovable.dev)
    ↓
Google Gemini 2.5 Flash Model
    ↓
Processed Response
    ↓
Database Update
    ↓
Frontend Display
```

### How AI Processes Data

#### 1. **Summary Generation**

**Input**: Raw note content (could be 1000+ words)

**Process**:
1. Content sent to AI with system prompt: "You are a concise academic summarizer"
2. User prompt: "Summarize into 5-7 bullet points"
3. AI analyzes content, identifies key points
4. AI generates structured summary

**Output**: Concise bullet points

**Example**:
```
Input: "React is a JavaScript library for building user interfaces. It uses a virtual DOM..."
Output: "• React is a JavaScript library for UI development
         • Uses virtual DOM for efficient updates
         • Component-based architecture
         • Unidirectional data flow
         • JSX syntax for templating"
```

#### 2. **Quiz Generation**

**Input**: Note content + calculated question count (20-30)

**Process**:
1. AI receives detailed prompt with instructions:
   - Generate EXACTLY N questions
   - Multiple choice format
   - Include explanations
   - Vary difficulty
2. AI analyzes content for key concepts
3. AI creates questions covering different topics
4. AI generates plausible wrong answers (distractors)
5. AI returns JSON array

**Output**: Structured JSON with questions

**Example**:
```json
[
  {
    "question": "What is the main purpose of React?",
    "options": [
      "Building user interfaces",
      "Server-side rendering",
      "Database management",
      "API development"
    ],
    "answer_index": 0,
    "explanation_short": "React is specifically designed for building interactive UIs"
  }
]
```

#### 3. **Flashcard Generation**

**Input**: Note content

**Process**:
1. AI receives prompt: "Generate 8 flashcards. Return JSON: [{front, back}]"
2. AI identifies key concepts and facts
3. AI creates question-answer pairs
4. AI formats as JSON

**Output**: Array of flashcard objects

**Example**:
```json
[
  {
    "front": "What is the virtual DOM?",
    "back": "A JavaScript representation of the DOM that React uses to optimize updates"
  }
]
```

### AI Workflow Diagram

```
┌─────────────────┐
│  User Clicks    │
│ "Generate..."   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Frontend       │
│  Sends Request  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Edge Function  │
│  1. Validates   │
│  2. Prepares    │
│     Prompt      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  AI Gateway     │
│  Routes to      │
│  Gemini Model   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Gemini 2.5     │
│  Processes      │
│  Content        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Returns        │
│  Structured     │
│  Response       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Edge Function  │
│  1. Validates   │
│  2. Saves to DB │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Frontend       │
│  Updates UI     │
└─────────────────┘
```

### Prompt Engineering

**System Prompts** (Tell AI its role):
- Summary: "You are a concise academic summarizer. Produce clear summaries capturing main topics and key facts."
- Quiz: "You are an expert quiz creator. Generate high-quality multiple-choice questions..."
- Flashcards: "Create concise Q/A flashcards. Return valid JSON only."

**User Prompts** (What to do):
- Summary: "Summarize the following note into 5-7 concise bullet points:\n\n{content}"
- Quiz: "Generate EXACTLY {count} questions from: {content}"
- Flashcards: "Generate 8 flashcards. Return JSON: [{front, back}]\n\n{content}"

### Libraries & Frameworks

- **Deno Standard Library**: HTTP server for Edge Functions
- **Supabase JS**: Database client
- **Fetch API**: HTTP requests to AI gateway
- **JSON Parsing**: Extract structured data from AI responses

---

## 6. Code Walkthrough

### Key Functions & Components

#### 1. **Authentication Flow** (Auth.tsx)

```typescript
const handleAuth = async (e: React.FormEvent) => {
  e.preventDefault();
  
  if (isSignUp) {
    // Create new account
    const { data, error } = await supabase.auth.signUp({
      email,
      password,
      options: { data: { display_name: displayName } }
    });
  } else {
    // Sign in existing user
    const { error } = await supabase.auth.signInWithPassword({ email, password });
  }
  
  // Navigate to dashboard on success
  navigate("/dashboard");
};
```

**What happens**:
1. User submits form
2. Supabase creates/verifies account
3. Session token stored automatically
4. User redirected to dashboard

#### 2. **Data Fetching with React Query** (Notes.tsx)

```typescript
const { data: notes, refetch } = useQuery({
  queryKey: ["notes"],
  queryFn: async () => {
    const { data: { user } } = await supabase.auth.getUser();
    const { data, error } = await supabase
      .from("notes")
      .select("*")
      .eq("user_id", user.id)
      .order("created_at", { ascending: false });
    return data;
  }
});
```

**Benefits**:
- Automatic caching (doesn't refetch unnecessarily)
- Loading states handled automatically
- Error handling built-in
- `refetch()` to manually refresh

#### 3. **AI Function Call** (NoteDetail.tsx)

```typescript
const handleGenerateSummary = async () => {
  setIsGeneratingSummary(true);
  
  // Call Edge Function
  const { data, error } = await supabase.functions.invoke("summarize-note", {
    body: { noteId: note.id, content: note.content }
  });
  
  // Refresh note data to get summary
  refetch();
  
  // Show typing animation
  setShowTypingEffect(true);
};
```

**Flow**:
1. User clicks button
2. Loading state shown
3. Edge function called
4. AI processes content
5. Summary saved to database
6. UI updates with animation

#### 4. **Spaced Repetition Algorithm** (FlashcardStudy.tsx)

```typescript
const updateCardDifficulty = (difficulty: 'easy' | 'medium' | 'hard') => {
  const card = currentCard;
  
  if (difficulty === 'easy') {
    card.interval_days = Math.floor(card.interval_days * card.ease_factor * 1.3);
    card.ease_factor = Math.min(2.5, card.ease_factor + 0.15);
  } else if (difficulty === 'medium') {
    card.interval_days = Math.floor(card.interval_days * card.ease_factor);
  } else { // hard
    card.interval_days = Math.max(1, Math.floor(card.interval_days * 0.5));
    card.ease_factor = Math.max(1.3, card.ease_factor - 0.15);
  }
  
  card.repetitions += 1;
  card.next_review = new Date(Date.now() + card.interval_days * 24 * 60 * 60 * 1000);
  
  // Save to database
  updateCard(card);
};
```

**How it works**:
- Easy → Card is easy, review less often (increase interval)
- Medium → Normal progression
- Hard → Card is difficult, review more often (decrease interval)

### Utility Functions

#### **cn()** - Class Name Merger (lib/utils.ts)

```typescript
import { clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

**Purpose**: Combines Tailwind classes intelligently, handling conflicts.

**Example**:
```typescript
cn("px-4", "px-6") // → "px-6" (last one wins)
cn("bg-red-500", condition && "bg-blue-500") // → Conditional classes
```

---

## 7. Project Flow Summary

### Complete User Journey: Creating a Note and Generating AI Content

```
STEP 1: User Opens App
┌─────────────────┐
│  Browser loads  │
│  App.tsx         │
└────────┬─────────┘
         │
         ▼
STEP 2: Authentication Check
┌─────────────────┐
│  Check if user  │
│  is logged in   │
└────────┬─────────┘
         │
    ┌────┴────┐
    │        │
    ▼        ▼
┌──────┐  ┌──────────┐
│ Yes  │  │   No     │
└──┬───┘  └────┬─────┘
   │           │
   │           ▼
   │      Auth.tsx
   │      (Login)
   │           │
   │           ▼
   │      Dashboard
   │           │
   └───────────┘
         │
         ▼
STEP 3: Navigate to Notes
┌─────────────────┐
│  User clicks    │
│  "Notes" in     │
│  Sidebar        │
└────────┬─────────┘
         │
         ▼
STEP 4: Notes Page Loads
┌─────────────────┐
│  Notes.tsx      │
│  1. useQuery    │
│     fetches     │
│     notes       │
│  2. Displays    │
│     list        │
└────────┬─────────┘
         │
         ▼
STEP 5: Create New Note
┌─────────────────┐
│  User clicks    │
│  "Create Note"  │
│  Dialog opens   │
│  User enters:   │
│  - Title        │
│  - Content      │
└────────┬─────────┘
         │
         ▼
STEP 6: Save Note
┌─────────────────┐
│  Frontend:      │
│  supabase       │
│  .from('notes') │
│  .insert(...)   │
└────────┬─────────┘
         │
         ▼
┌─────────────────┐
│  Database:      │
│  INSERT INTO    │
│  notes (...)    │
└────────┬─────────┘
         │
         ▼
┌─────────────────┐
│  Returns:       │
│  { id, title,    │
│    content }     │
└────────┬─────────┘
         │
         ▼
STEP 7: Navigate to Note Detail
┌─────────────────┐
│  navigate(      │
│  `/notes/${id}`) │
└────────┬─────────┘
         │
         ▼
STEP 8: Note Detail Page
┌─────────────────┐
│  NoteDetail.tsx │
│  Shows:         │
│  - Title        │
│  - Content      │
│  - Buttons:     │
│    • Summary    │
│    • Quiz       │
│    • Flashcards │
└────────┬─────────┘
         │
         ▼
STEP 9: User Clicks "Generate Summary"
┌─────────────────┐
│  handleGenerate │
│  Summary()      │
│  1. Sets loading│
│  2. Calls Edge  │
│     Function    │
└────────┬─────────┘
         │
         ▼
STEP 10: Edge Function Processes
┌─────────────────┐
│  summarize-note │
│  /index.ts      │
│  1. Gets content│
│  2. Calls AI API│
└────────┬─────────┘
         │
         ▼
STEP 11: AI Processing
┌─────────────────┐
│  AI Gateway     │
│  → Gemini 2.5   │
│  → Analyzes     │
│  → Generates    │
│  → Returns      │
└────────┬─────────┘
         │
         ▼
STEP 12: Save Summary
┌─────────────────┐
│  Edge Function: │
│  UPDATE notes   │
│  SET summary = ?│
└────────┬─────────┘
         │
         ▼
STEP 13: Return to Frontend
┌─────────────────┐
│  Frontend:      │
│  1. Receives    │
│     summary     │
│  2. Shows typing│
│     animation   │
│  3. Displays    │
│     summary     │
└─────────────────┘
```

### Data Flow Diagram

```
┌──────────────┐
│   Browser    │
│  (Frontend)  │
└──────┬───────┘
       │
       │ HTTP Requests
       │ (REST API)
       ▼
┌──────────────┐
│   Supabase   │
│   Platform   │
└──────┬───────┘
       │
   ┌───┴───┐
   │       │
   ▼       ▼
┌──────┐ ┌──────────────┐
│Post- │ │ Edge Functions│
│greSQL│ │   (Deno)     │
│      │ └──────┬───────┘
│      │        │
│      │        │ HTTP Request
│      │        ▼
│      │  ┌──────────────┐
│      │  │  AI Gateway  │
│      │  │  (Gemini)    │
│      │  └──────────────┘
│      │
│      │  AI Response
│      │        │
│      │        ▼
│      │  ┌──────────────┐
│      │  │ Edge Function │
│      │  │ Processes &   │
│      │  │ Saves to DB  │
│      │  └──────┬───────┘
│      │         │
│      └─────────┘
│            │
│            │ Save Result
│            ▼
│      ┌──────────────┐
│      │  PostgreSQL   │
│      │  (Database)   │
│      └──────────────┘
```

---

## 8. Learning & Practice Exercises

### Beginner Exercises

1. **Add a "Favorite" Feature to Notes**
   - Add `is_favorite` column to notes table
   - Add star icon button in NoteDetail.tsx
   - Toggle favorite status on click
   - Filter notes by favorite in Notes.tsx

2. **Add Note Categories/Tags**
   - Create `tags` table
   - Add tag selection when creating note
   - Display tags on note cards
   - Filter notes by tag

3. **Improve Quiz Feedback**
   - Show explanation after each question
   - Add "Review Mistakes" section at end
   - Highlight correct/incorrect answers

4. **Add Note Search**
   - Add search input in Notes.tsx
   - Filter notes by title/content
   - Highlight search terms

### Intermediate Exercises

1. **Export Notes as PDF**
   - Add "Export" button
   - Use library like `jsPDF` or `react-pdf`
   - Generate formatted PDF with note content

2. **Add Note Sharing**
   - Generate shareable link
   - Create public view page
   - Add privacy settings

3. **Improve AI Prompts**
   - Experiment with different prompt styles
   - Add prompt templates
   - A/B test prompt effectiveness

4. **Add Study Reminders**
   - Schedule review reminders
   - Use browser notifications API
   - Track review history

### Advanced Exercises

1. **Implement Collaborative Notes**
   - Add sharing permissions
   - Real-time collaboration with Supabase Realtime
   - Conflict resolution

2. **Add Voice Notes**
   - Record audio using Web Audio API
   - Transcribe with speech-to-text API
   - Save as note content

3. **Create Study Plans**
   - AI-generated study schedules
   - Calendar integration
   - Progress tracking

4. **Build Mobile App**
   - Use React Native
   - Share codebase with web
   - Native features (push notifications)

### Common Errors & Debugging

#### Error 1: "API key not configured"
**Cause**: Missing `LOVABLE_API_KEY` in Edge Function environment
**Fix**: Set environment variable in Supabase dashboard

#### Error 2: "Not authenticated"
**Cause**: User session expired or not logged in
**Fix**: Check `supabase.auth.getUser()` before API calls

#### Error 3: "Failed to parse JSON"
**Cause**: AI returned malformed JSON
**Fix**: Add better error handling in Edge Functions:
```typescript
try {
  const flashcards = JSON.parse(aiResponse);
} catch (e) {
  // Try to fix JSON or return error
}
```

#### Error 4: "Row Level Security policy violation"
**Cause**: Database RLS blocking access
**Fix**: Check RLS policies in Supabase dashboard

#### Error 5: "CORS error"
**Cause**: Missing CORS headers in Edge Function
**Fix**: Ensure `corsHeaders` are included in response

---

## 9. Summary Table / Mind Map

### File Structure & Purpose

| File/Folder | Purpose | Key Functions |
|-------------|---------|---------------|
| **Frontend** |
| `App.tsx` | Root component | Sets up providers, routing |
| `pages/Auth.tsx` | Login/Signup | Authentication flow |
| `pages/Dashboard.tsx` | Home page | Overview, stats, widgets |
| `pages/Notes.tsx` | Notes list | CRUD operations |
| `pages/NoteDetail.tsx` | Note view | AI generation triggers |
| `pages/Quizzes.tsx` | Quiz list | Display quizzes |
| `pages/QuizPlay.tsx` | Quiz interface | Question display, scoring |
| `pages/Flashcards.tsx` | Flashcard sets | Set management |
| `pages/FlashcardStudy.tsx` | Study interface | Spaced repetition |
| `components/dashboard/Sidebar.tsx` | Navigation | Route navigation |
| `components/dashboard/DashboardLayout.tsx` | Layout wrapper | Page structure |
| **Backend** |
| `supabase/functions/summarize-note/` | AI summary | Calls Gemini, saves summary |
| `supabase/functions/generate-quiz/` | Quiz generation | Creates questions, saves quiz |
| `supabase/functions/generate-flashcards/` | Flashcard generation | Creates cards, saves to DB |
| **Database** |
| `notes` table | Store notes | CRUD operations |
| `quizzes` table | Store quizzes | Question storage |
| `flashcards` table | Store cards | Spaced repetition data |
| `quiz_attempts` table | Track attempts | Score history |
| `profiles` table | User data | Profile info |

### Component Interaction Map

```
App.tsx
├── ThemeProvider (Dark/Light mode)
├── QueryClientProvider (Data caching)
└── BrowserRouter
    ├── /auth → Auth.tsx
    └── /dashboard → DashboardLayout
        ├── Sidebar (Navigation)
        └── Page Content
            ├── Dashboard.tsx
            ├── Notes.tsx
            │   └── NoteDetail.tsx
            │       ├── Generate Summary → Edge Function → AI
            │       ├── Generate Quiz → Edge Function → AI
            │       └── Generate Flashcards → Edge Function → AI
            ├── Quizzes.tsx
            │   └── QuizPlay.tsx
            ├── Flashcards.tsx
            │   └── FlashcardStudy.tsx
            └── Profile.tsx
```

### Data Flow Summary

```
User Input
    ↓
React Component (useState/useQuery)
    ↓
Supabase Client (supabase.from().select/insert/update)
    ↓
PostgreSQL Database
    OR
Supabase Edge Function
    ↓
AI API (Gemini 2.5 Flash)
    ↓
Process & Save to Database
    ↓
Return to Frontend
    ↓
Update UI (React Query cache)
```

### AI Integration Points

| Feature | Trigger | Edge Function | AI Model | Output |
|---------|---------|---------------|----------|--------|
| Summary | Button click | `summarize-note` | Gemini 2.5 | Text summary |
| Quiz | Button click | `generate-quiz` | Gemini 2.5 | JSON questions |
| Flashcards | Button click | `generate-flashcards` | Gemini 2.5 | JSON cards |

### Key Technologies by Layer

```
┌─────────────────────────────────────┐
│         PRESENTATION LAYER           │
│  React + TypeScript + Tailwind CSS   │
└─────────────────────────────────────┘
              ↓ ↑
┌─────────────────────────────────────┐
│         STATE MANAGEMENT             │
│      TanStack Query (React Query)   │
└─────────────────────────────────────┘
              ↓ ↑
┌─────────────────────────────────────┐
│         API LAYER                    │
│      Supabase Client + Edge Functions│
└─────────────────────────────────────┘
              ↓ ↑
┌─────────────────────────────────────┐
│         DATA LAYER                   │
│         PostgreSQL (Supabase)       │
└─────────────────────────────────────┘
              ↓
┌─────────────────────────────────────┐
│         AI LAYER                     │
│    Google Gemini 2.5 Flash (via     │
│    AI Gateway API)                  │
└─────────────────────────────────────┘
```

---

## Conclusion

**Study Buddy** is a full-stack application that combines:
- **Modern frontend** (React + TypeScript) for great user experience
- **Serverless backend** (Supabase Edge Functions) for scalability
- **PostgreSQL database** for reliable data storage
- **AI integration** (Google Gemini) for intelligent content generation

The architecture is designed to be:
- **Scalable**: Serverless functions handle traffic spikes
- **Maintainable**: Clear separation of concerns
- **Type-safe**: TypeScript throughout
- **User-friendly**: Modern UI with smooth animations

This project demonstrates how to build a production-ready application that leverages AI to enhance user productivity and learning outcomes.


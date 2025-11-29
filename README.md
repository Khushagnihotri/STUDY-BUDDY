# Study Buddy

An AI-powered study and productivity platform that helps students learn smarter and faster through intelligent note-taking, automated summaries, interactive quizzes, and spaced repetition flashcards.

## Features

- **Smart Notes**: Create and organize your study notes with rich text support
- **AI Summaries**: Automatically generate concise summaries of your notes using AI
- **Interactive Quizzes**: Generate comprehensive quizzes from your notes to test your knowledge
- **Flashcards**: Create and study flashcards with spaced repetition for effective memorization
- **Progress Tracking**: Monitor your learning progress with detailed analytics and statistics
- **Modern UI**: Beautiful, responsive interface with dark mode support

## Tech Stack

- **Frontend**: React 18 + TypeScript
- **Styling**: Tailwind CSS with custom design system
- **UI Components**: Radix UI + shadcn/ui
- **State Management**: TanStack Query (React Query)
- **Routing**: React Router v6
- **Backend**: Supabase (PostgreSQL + Edge Functions)
- **AI Integration**: OpenAI-compatible API
- **Build Tool**: Vite

## Getting Started

### Prerequisites

- Node.js 18+ or Bun
- npm, yarn, or bun
- Supabase account and project

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd study-buddy
```

2. Install dependencies:
```bash
npm install
# or
bun install
```

3. Set up environment variables:
Create a `.env.local` file in the root directory:
```env
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

4. Set up Supabase Edge Functions:
Configure your Supabase Edge Functions with the required environment variables:
- `LOVABLE_API_KEY`: Your AI API key for generating summaries, quizzes, and flashcards

5. Run the development server:
```bash
npm run dev
# or
bun dev
```

The app will be available at `http://localhost:8080`

## Project Structure

```
study-buddy/
├── src/
│   ├── components/        # React components
│   │   ├── dashboard/    # Dashboard-specific components
│   │   └── ui/           # Reusable UI components
│   ├── pages/            # Page components
│   ├── hooks/            # Custom React hooks
│   ├── lib/              # Utility functions
│   ├── integrations/     # Third-party integrations
│   └── assets/           # Static assets
├── supabase/
│   ├── functions/        # Edge Functions
│   └── migrations/       # Database migrations
└── public/               # Public assets
```

## Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run lint` - Run ESLint

## Database Schema

The project uses Supabase PostgreSQL with the following main tables:
- `notes` - User notes with content and AI-generated summaries
- `quizzes` - Generated quizzes with questions and answers
- `flashcards` - Flashcard sets for spaced repetition
- `quiz_attempts` - User quiz attempt history

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is private and proprietary.

## Support

For issues and questions, please open an issue in the repository.


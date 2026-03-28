# Voixa

We were inspired by the need to support diverse student communities, especially Spanish-speaking and bilingual learners who face language barriers in traditional education tools. Most platforms are designed for English-first learning, making it harder for many students to fully understand and engage with their coursework. We wanted to create a system where students can learn and interact in the language they are most comfortable with.

At the same time, learning today is still largely passive. Students scroll through PDFs, slides, and LMS platforms without real interaction, often struggling with complex topics. We realized the issue is not the lack of content, but the lack of accessible and engaging ways to interact with it.

VOIXA brings these ideas together by turning course materials into something students can talk to, watch, and actively learn from, while ensuring language is never a barrier.

**Live demo:** https://voixa.vercel.app/dashboard

## Prerequisites

- Python 3.10+
- Node.js 18+
- An [OpenAI](https://platform.openai.com/) API key (for chat, flashcards, MCQ, and quizzes)
- A [Google Gemini](https://aistudio.google.com/) API key (for all video generation)
- An [ElevenLabs](https://elevenlabs.io/) API key (for voice input and audio responses)

## Setup

### 1. Clone the repo

```bash
git clone <repo-url>
cd Voixa
```

### 2. Configure environment variables

```bash
cp backend/.env.example backend/.env
```

Open `backend/.env` and fill in your API keys:

| Variable | Description | Default |
|---|---|---|
| `OPENAI_API_KEY` | OpenAI API key (chat, flashcards, MCQ, quizzes) | required |
| `OPENAI_MODEL` | OpenAI model for chat and generation | `gpt-4o-mini` |
| `GEMINI_API_KEY` | Google Gemini API key (full video generation) | required |
| `GEMINI_MODEL` | Gemini model for video generation | `gemini-2.5-flash` |
| `VIDEO_SCRIPT_MODEL` | Gemini model used for video pipeline | `gemini-2.5-flash` |
| `GEMINI_API_URL` | Gemini API base URL | `https://generativelanguage.googleapis.com` |
| `ELEVENLABS_API_KEY` | ElevenLabs API key for voice input and audio responses | required |
| `ELEVENLABS_VOICE_ID` | ElevenLabs voice ID | required |

### 3. Set up the backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate        # On Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 4. Set up the frontend

```bash
cd frontend
npm install
```

## Running the App

Open two terminals:

**Terminal 1 — Backend**
```bash
cd backend
source .venv/bin/activate        # On Windows: .venv\Scripts\activate
uvicorn app:app --reload
```
The API will be available at `http://localhost:8000`.

**Terminal 2 — Frontend**
```bash
cd frontend
npm run dev
```
The app will be available at `http://localhost:5173`.

## Features

- **StudyBuddy Chat** — Streaming AI tutor grounded in your lecture materials, powered by OpenAI
- **Voice Chat** — ElevenLabs voice responses and audio input for a fully hands-free study experience
- **Flashcards** — Auto-generated Q&A pairs from any lecture file via OpenAI
- **MCQ Quizzes** — Multiple choice questions with explanations and progress tracking via OpenAI
- **Learning Maps** — Lectures broken down into structured study chunks via OpenAI
- **AI Video Summaries** — Full video generation powered entirely by Gemini: script, visuals, and narration composed into a complete video *(local only — see note below)*
- **Assignment Helper** — AI tutor for assignments with guardrails (guides without giving direct answers)
- **Task Tracker** — AI-generated assignment task breakdown with completion tracking
- **Multi-language Support** — English and Spanish UI and content translation
- **Conversation History** — Saved and resumable chat sessions per course

## Project Structure

```
Voixa/
├── backend/
│   ├── app.py                  # FastAPI server (all routes)
│   ├── requirements.txt        # Python dependencies
│   ├── .env                    # Your local env vars (not committed)
│   ├── .env.example            # Template for env vars
│   ├── courses/
│   │   ├── courses.json        # Course metadata
│   │   └── <course-folder>/
│   │       ├── lectures/       # Lecture PDFs/PPTXs
│   │       └── assignments/    # Assignment files
│   ├── conversations/          # Saved chat sessions (auto-created)
│   ├── progress/               # Quiz score history (auto-created)
│   ├── trackers/               # Assignment task progress (auto-created)
│   └── translations/           # Cached translated content (auto-created)
└── frontend/
    ├── src/
    │   ├── App.jsx             # Route definitions
    │   ├── api.js              # Axios API client
    │   ├── pages/              # Page components (22 pages)
    │   ├── components/         # Shared UI (Sidebar, CourseCard, etc.)
    │   └── locales/            # i18n strings (en.json, es.json)
    └── vite.config.js          # Proxies /api to localhost:8000
```

## Adding Courses

Edit `backend/courses/courses.json` to add a course entry:

```json
{
  "id": "my-course",
  "name": "Course Name",
  "code": "CS-101",
  "term": "Spring 2026",
  "color": "#4f46e5",
  "folder": "courses/my-course"
}
```

Then create the folder structure under `backend/`:

```
backend/courses/my-course/
├── lectures/       # Drop PDFs or PPTXs here
└── assignments/    # Drop assignment files here
```

Files added to these folders are automatically available in the app.

## Deployment

The project includes a `render.yaml` for one-click deployment to [Render](https://render.com/). The backend runs as a Python web service with:

- Build: `pip install -r requirements.txt`
- Start: `uvicorn app:app --host 0.0.0.0 --port $PORT`

Set all environment variables from `backend/.env` in your Render service's environment settings.

> **Note — Video generation is only available when running locally.** Generating a video requires downloading images, rendering audio, and compositing frames with MoviePy, which is memory-intensive. The deployed backend runs on Render's free tier, which does not have enough memory to handle this workload and will crash under the load. Run the full stack locally if you need video generation.

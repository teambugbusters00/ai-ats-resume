# Career-Pathfinder - Project Architecture Plan

## 📋 Project Overview

**Career-Pathfinder** is a comprehensive ATS (Applicant Tracking System) Resume Scanner platform designed to help job seekers optimize their resumes, find relevant jobs, practice interviews, and automate email communications. The project is a monorepo using pnpm workspace containing a React frontend and Express backend.

---

## 🏗️ System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                    CAREER-PATHFINDER                                               │
│                                 (pnpm Monorepo Workspace)                                          │
├─────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────────────────────┐  │
│  │                                    EXTERNAL SERVICES                                          │  │
│  │  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐    │  │
│  │  │   HuggingFace    │  │   Firebase       │  │   SendGrid/SMTP  │  │   PostgreSQL     │    │  │
│  │  │   (AI Models)    │  │   (Auth)         │  │   (Email)        │  │   (Database)     │    │  │
│  │  │   - Phi-3        │  │   - Users        │  │   - Automation   │  │   - Drizzle ORM  │    │  │
│  │  │   - TinyLlama   │  │   - Sessions     │  │   - Tracking     │  │   - Jobs Data    │    │  │
│  │  └────────┬─────────┘  └────────┬─────────┘  └────────┬─────────┘  └────────┬─────────┘    │  │
│  │           │                       │                       │                       │              │  │
│  │           │                       │                       │                       │              │  │
│  │           ▼                       ▼                       ▼                       ▼              │  │
│  └───────────────────────────────────────────────────────────────────────────────────────────────┘  │
│                                                 │                                                    │
│                                                 │ API Calls                                           │
│                                                 ▼                                                    │
│  ┌───────────────────────────────────────────────────────────────────────────────────────────────┐  │
│  │                                     BACKEND (Port 3001)                                        │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────┐   │  │
│  │  │                              Express API Server (TypeScript)                           │   │  │
│  │  │                                                                                         │   │  │
│  │  │  ┌─────────────────────────────────────────────────────────────────────────────────┐  │  │  │
│  │  │  │                              API ROUTES                                            │  │  │  │
│  │  │  ├─────────────────────────────────────────────────────────────────────────────────┤  │  │  │
│  │  │  │                                                                                   │  │  │  │
│  │  │  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐             │  │  │  │
│  │  │  │  │    /auth     │ │   /resume    │ │   /jobs      │ │   /score     │             │  │  │  │
│  │  │  │  │  - login     │ │  - analyze   │ │  - search    │ │  - pdf       │             │  │  │  │
│  │  │  │  │  - register  │ │              │ │  - explore   │ │  - text      │             │  │  │  │
│  │  │  │  │  - logout    │ │              │ │  - sources   │ │  - ai        │             │  │  │  │
│  │  │  │  │  - profile   │ │              │ │              │ │              │             │  │  │  │
│  │  │  │  └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘             │  │  │  │
│  │  │  │                                                                                   │  │  │  │
│  │  │  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐             │  │  │  │
│  │  │  │  │  /email     │ │ /interview   │ │/huggingface  │ │   /health   │             │  │  │  │
│  │  │  │  │  - connect  │ │  - create    │ │  - run logs  │ │  - health   │             │  │  │  │
│  │  │  │  │  - send     │ │  - answer    │ │  - build logs│ │             │             │  │  │  │
│  │  │  │  │  - tracking │ │  - voice     │ │              │ │             │             │  │  │  │
│  │  │  │  │  - template │ │              │ │              │ │             │             │  │  │  │
│  │  │  │  └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘             │  │  │  │
│  │  │  │                                                                                   │  │  │  │
│  │  │  └─────────────────────────────────────────────────────────────────────────────────┘  │  │  │
│  │  │                                                                                         │   │  │
│  │  │  ┌─────────────────────────────────────────────────────────────────────────────────┐  │  │  │
│  │  │  │                              MIDDLEWARES                                         │  │  │  │
│  │  │  │  - CORS           - Cookie Parser    - Pino HTTP (Logging)                      │  │  │  │
│  │  │  └─────────────────────────────────────────────────────────────────────────────────┘  │  │  │
│  │  │                                                                                         │   │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────┘   │  │
│  │                                                 │                                                    │
│                                                 │ HTTP/JSON                                           │
│                                                 ▼                                                    │
│  ┌───────────────────────────────────────────────────────────────────────────────────────────────┐  │
│  │                                    FRONTEND (Port 5173)                                        │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │                              React + Vite Application                                    │  │  │
│  │  │                                                                                         │  │  │
│  │  │  ┌─────────────────────────────────────────────────────────────────────────────────┐    │  │  │
│  │  │  │                               PAGES                                                │    │  │  │
│  │  │  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐    │    │  │  │
│  │  │  │  │    Login      │  │    Signup     │  │   Dashboard   │  │  Not Found    │    │    │  │  │
│  │  │  │  │  (Demo Mode)  │  │ (localStore)  │  │  (Protected) │  │               │    │    │  │  │
│  │  │  │  └───────────────┘  └───────────────┘  └───────────────┘  └───────────────┘    │    │  │  │
│  │  │  │                                   │                                                 │    │  │  │
│  │  │  │                                   ▼                                                 │    │  │  │
│  │  │  │  ┌─────────────────────────────────────────────────────────────────────────────┐  │    │  │  │
│  │  │  │  │                         DASHBOARD PAGES                                       │  │    │  │  │
│  │  │  │  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐│  │    │  │  │
│  │  │  │  │  │    Resume     │  │     Jobs      │  │   Interview   │  │     Email     ││  │    │  │  │
│  │  │  │  │  │  Scanner      │  │    (Intel)    │  │   Practice    │  │  (Comms Grid) ││  │    │  │  │
│  │  │  │  │  │               │  │               │  │   (Voice)     │  │               ││  │    │  │  │
│  │  │  │  │  │ - ATS Scoring │  │ - Search      │  │ - Questions  │  │ - Connect    ││  │    │  │  │
│  │  │  │  │  │ - PDF Parse   │  │ - Explore     │  │ - AI Feedback│  │ - Send       ││  │    │  │  │
│  │  │  │  │  │ - Suggestions │  │ - Filters     │  │ - Transcript │  │ - Templates  ││  │    │  │  │
│  │  │  │  │  └───────────────┘  └───────────────┘  └───────────────┘  └───────────────┘│  │    │  │  │
│  │  │  │  │                                     │                                           │    │  │  │
│  │  │  │  │                                     ▼                                           │    │  │  │
│  │  │  │  │  ┌───────────────┐  ┌───────────────┐                                        │    │  │  │
│  │  │  │  │  │   Profile    │  │ Startup Map   │                                        │    │  │  │
│  │  │  │  │  │  (User Info) │  │  (Map View)   │                                        │    │  │  │
│  │  │  │  │  └───────────────┘  └───────────────┘                                        │    │  │  │
│  │  │  │  └─────────────────────────────────────────────────────────────────────────────┘  │    │  │  │
│  │  │  │                                                                                         │    │  │  │
│  │  │  │  ┌─────────────────────────────────────────────────────────────────────────────────┐  │    │  │  │
│  │  │  │  │                         STATE MANAGEMENT                                        │  │    │  │  │
│  │  │  │  │  - useAuth (Demo Auth Context)        - React Query (TanStack Query)          │  │    │  │  │
│  │  │  │  │  - localStorage for demo users        - API Client (@workspace/api-client)     │  │    │  │  │
│  │  │  │  └─────────────────────────────────────────────────────────────────────────────────┘  │    │  │  │
│  │  │  │                                                                                         │    │  │  │
│  │  │  │  ┌─────────────────────────────────────────────────────────────────────────────────┐  │    │  │  │
│  │  │  │  │                            UI COMPONENTS                                         │  │    │  │  │
│  │  │  │  │  - Radix UI Components (Dialog, Dropdown, Toast, etc.)                          │  │    │  │  │
│  │  │  │  │  - Shadcn/UI Components (Button, Input, Card, Table, etc.)                     │  │    │  │  │
│  │  │  │  │  - Custom Components (Sidebar, StartupCard, StartupMap, etc.)                    │  │    │  │  │
│  │  │  │  └─────────────────────────────────────────────────────────────────────────────────┘  │    │  │  │
│  │  │  │                                                                                         │    │  │  │
│  │  │  └─────────────────────────────────────────────────────────────────────────────────────────┘    │  │
│  │  │                                                                                         │   │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────┘   │  │
│  │                                                                                                     │
│  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
Career-Pathfinder/
├── package.json                      # Root workspace config
├── pnpm-workspace.yaml               # Workspace definitions & catalog
├── tsconfig.base.json                # Base TypeScript config
│
├── artifacts/                        # Main applications
│   ├── api-server/                  # Backend (Express + TypeScript)
│   │   ├── src/
│   │   │   ├── index.ts             # Entry point
│   │   │   ├── app.ts               # Express app
│   │   │   ├── routes/              # API routes
│   │   │   │   ├── auth.ts          # Login/register endpoints
│   │   │   │   ├── jobs.ts          # Job search endpoints
│   │   │   │   ├── resume.ts        # Resume parsing endpoints
│   │   │   │   ├── email.ts         # Email automation endpoints
│   │   │   │   ├── score.ts         # Resume scoring endpoints
│   │   │   │   ├── score-ai.ts      # AI-powered scoring
│   │   │   │   ├── interview.ts     # Interview practice endpoints
│   │   │   │   ├── huggingface.ts   # HuggingFace integration
│   │   │   │   ├── startups.ts      # Startup data endpoints
│   │   │   │   └── health.ts        # Health check
│   │   │   ├── services/            # External services
│   │   │   │   ├── firebase.ts      # Firebase admin SDK
│   │   │   │   └── serpapi.ts       # Job search API
│   │   │   └── lib/                 # Utilities
│   │   │       └── logger.ts        # Pino logger
│   │   ├── package.json
│   │   └── .env                     # Environment variables
│   │
│   ├── ats-scanner/                 # Frontend (React + Vite)
│   │   ├── src/
│   │   │   ├── App.tsx             # Main app component
│   │   │   ├── main.tsx            # Entry point
│   │   │   ├── index.css           # Global styles
│   │   │   ├── pages/
│   │   │   │   ├── login.tsx       # Login page (demo mode)
│   │   │   │   ├── signup.tsx      # Signup page (localStorage)
│   │   │   │   ├── not-found.tsx  # 404 page
│   │   │   │   └── dashboard/
│   │   │   │       ├── resume.tsx  # Resume scanner
│   │   │   │       ├── jobs.tsx    # Job search
│   │   │   │       ├── interview.tsx # Voice interview
│   │   │   │       ├── email.tsx   # Email automation
│   │   │   │       ├── profile.tsx # User profile
│   │   │   │       └── startup-map.tsx # Startup map
│   │   │   ├── components/
│   │   │   │   ├── layout.tsx      # App layout
│   │   │   │   ├── cyber-ui.tsx    # Custom UI components
│   │   │   │   ├── sidebar-filters.tsx
│   │   │   │   ├── startup-card.tsx
│   │   │   │   ├── startup-map.tsx
│   │   │   │   └── ui/             # Shadcn UI components
│   │   │   ├── hooks/
│   │   │   │   ├── use-auth.tsx    # Demo auth (localStorage)
│   │   │   │   ├── use-mobile.tsx  # Mobile detection
│   │   │   │   └── use-toast.ts    # Toast notifications
│   │   │   └── lib/
│   │   │       ├── startup-types.ts
│   │   │       └── utils.ts
│   │   └── package.json
│   │
│   └── mockup-sandbox/             # Mockup preview tool
│
├── lib/                             # Shared libraries
│   ├── db/                         # Database (Drizzle ORM + PostgreSQL)
│   │   └── src/
│   │       ├── index.ts           # DB connection & query builder
│   │       └── schema/            # Table schemas
│   │
│   ├── api-zod/                   # Shared API schemas (Zod)
│   │
│   └── api-spec/                  # OpenAPI specification
│       └── openapi.yaml           # API documentation
│
└── scripts/                        # Build scripts
```

---

## 🔄 Data Flow Diagrams

### 1. User Authentication Flow (Demo Mode)
```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Login   │───▶│ useAuth  │───▶│localStore│───▶│Dashboard │
│  Page    │    │  Hook    │    │ (demo_)  │    │ (Protected)│
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │                              │
     │              ▼                              │
     │     ┌──────────────┐                       │
     │     │ Demo Users  │                       │
     │     │ (hardcoded) │                       │
     │     └──────────────┘                       │
     │                                           │
     └───────────────────────────────────────────┘
                   Redirect
```

### 2. Resume Analysis Flow
```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Resume   │───▶│  Backend │───▶│ HuggingFace│──▶│  Score   │
│ Upload   │    │  API     │    │  (AI)      │   │ Results  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │               │               │
     │              ▼               ▼               │
     │     ┌────────────────────────────────────┐  │
     │     │       Resume Routes                │  │
     │     │  /resume/analyze, /score/*        │  │
     │     └────────────────────────────────────┘  │
     │                                             │
     └─────────────────────────────────────────────┘
                   API Response
```

### 3. Job Search Flow
```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│   Job    │───▶│  Backend │───▶│ SerpAPI  │───▶│   Job    │
│  Search  │    │  Jobs    │    │ (Search) │    │ Results  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │               │               │
     │              ▼               ▼               │
     │     ┌────────────────────────────────────┐  │
     │     │       Jobs Routes                  │  │
     │     │  /jobs/search, /jobs/explore       │  │
     │     └────────────────────────────────────┘  │
     │                                             │
     └─────────────────────────────────────────────┘
                   Display Jobs Grid
```

### 4. Interview Practice Flow (Voice)
```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Create   │───▶│ Generate │───▶│  Submit  │───▶│  AI      │
│ Session  │    │ Questions│    │  Voice   │    │ Feedback │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │               │               │
     │              ▼               ▼               │
     │     ┌────────────────────────────────────┐  │
     │     │       Interview Routes             │  │
     │     │  /interview/create, /voice        │  │
     │     └────────────────────────────────────┘  │
     │                                             │
     └─────────────────────────────────────────────┘
                   Show Results
```

### 5. Email Automation Flow
```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Configure│───▶│  Connect │───▶│  Send    │───▶│ Tracking │
│   SMTP   │    │  Email   │    │  Bulk    │    │ Results  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │               │               │
     │              ▼               ▼               │
     │     ┌────────────────────────────────────┐  │
     │     │       Email Routes                  │  │
     │     │  /email/connect, /send, /tracking  │  │
     │     └────────────────────────────────────┘  │
     │                                             │
     └─────────────────────────────────────────────┘
                   Dashboard Stats
```

### 6. Startup Discovery Map Flow
```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Page    │───▶│ Fetch   │───▶│ Display  │───▶│ Select   │
│  Load    │    │ Startups│    │ on Map   │    │ Startup  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
     │              │               │               │
     │              ▼               ▼               │
     │     ┌────────────────────────────────────┐  │
     │     │       API: /startups               │  │
     │     │  Filters: search, tag, city        │  │
     │     └────────────────────────────────────┘  │
     │                                             │
     │         ┌──────────────────────────────────┘
     │         │
     │         ▼
     │  ┌──────────────┐    ┌──────────────┐
     │  │   Map View  │    │   List View  │
     │  │ - Markers   │    │ - Grid Cards │
     │  │ - Popups    │    │ - Animation  │
     │  │ - Controls │    │              │
     │  └──────────────┘    └──────────────┘
     │
     │  ┌──────────────┐    ┌──────────────┐
     │  │ Sidebar      │    │ Bottom Panel │
     │  │ Filters      │    │ Startup List │
     │  └──────────────┘    └──────────────┘
     │
     └─────────────────────────────────────────────┘
                   Show Startup Details
```

---

## 🎯 Key Features

### 1. Resume Scanner (ATS Scoring)
- **PDF Parsing**: Upload and parse PDF resumes
- **Text Analysis**: Analyze resume against job descriptions
- **Keyword Matching**: Identify matched and missing keywords
- **AI Scoring**: Powered by Phi-3 or TinyLlama models
- **Suggestions**: Get improvement recommendations

### 2. Job Intel (Job Search)
- **Search**: Find jobs by title, location, type
- **Explore**: Advanced filtering with multiple criteria
- **Sources**: View available job sources
- **Filters**: Job type, experience, salary, remote

### 3. Interview Practice
- **Question Generation**: AI-generated questions based on job title
- **Text Answers**: Submit text for evaluation
- **Voice Answers**: Record voice responses
- **AI Feedback**: Get pronunciation and content feedback

### 4. Comms Grid (Email Automation)
- **SMTP Connection**: Connect email account
- **Template Generation**: AI-powered email templates
- **Bulk Send**: Send emails to multiple recipients
- **Tracking**: Track sent emails and open rates

### 5. User Profile
- **Profile Management**: Update name, bio, job title, location
- **Authentication**: Firebase-backed authentication

---

## 📄 Pages Overview

### Authentication Pages

#### Login Page (`/login`)
The login page provides demo authentication for testing without a backend.

**Features:**
- Quick demo access with 3 pre-configured accounts (demo@example.com, test@example.com, admin@example.com)
- Email/password form for manual login
- Checks against hardcoded demo users
- Generates a `demo_token_{timestamp}` for session
- Uses localStorage to persist session
- Cyber-themed UI with animated backgrounds
- Toast notifications for login status

**How it works:**
1. User clicks a demo account button or enters credentials
2. System validates against demo_users stored in localStorage
3. On success, stores user object and token in localStorage
4. Redirects to dashboard/resume
5. Session persists until user explicitly logs out

**Demo Accounts:**
- Demo User (demo@example.com)
- Test User (test@example.com)
- Admin (admin@example.com)

---

#### Signup Page (`/signup`)
The signup page allows creating new demo accounts stored in localStorage.

**Features:**
- Registration form with name, email, password fields
- Password confirmation field
- Email uniqueness validation
- Stores new users in localStorage under `demo_users` key
- Automatic login after successful registration
- Cyber-themed UI with background graphics

**How it works:**
1. User fills in registration form
2. System validates password match and required fields
3. Checks if email already exists in localStorage
4. Creates new user object with unique ID
5. Adds to existing demo_users array
6. Generates demo token and auto-logs in user
7. Redirects to dashboard

---

### Dashboard Pages

#### Resume Scanner (`/dashboard/resume`)
The resume scanner analyzes resumes against job descriptions using ATS scoring.

**Features:**
- Two input modes: Upload (PDF/DOCX/TXT) or Paste text
- PDF text extraction using PDF.js library
- Job description input for match scoring
- Dual scoring system: ATS Score + AI Score
- Keyword matching analysis
- Visual score display with animated progress rings
- Suggestions for improvement

**How it works:**
1. User uploads a file or pastes resume text
2. System extracts text using PDF.js for PDFs
3. User optionally provides job description
4. Clicks "Initiate Scan" button
5. API call to `/api/resume/analyze`
6. On API failure, falls back to demo mode with local analysis
7. Displays results:
   - Overall score (AI or ATS depending on JD availability)
   - Matched keywords (green badges)
   - Missing keywords (red badges)
   - Optimization suggestions

**Technical Details:**
- Uses PDF.js from CDN for PDF parsing
- Falls back to keyword matching algorithm
- Shows both AI and ATS scores when JD provided
- Animated score rings using Framer Motion
- Supports: PDF, TXT files (DOCX shows copy/paste message)

---

#### Job Intel (`/dashboard/jobs`)
The job search page allows finding jobs from various sources.

**Features:**
- Job title and location search
- Two search methods: API (default) or Deep Search
- Grid display of job cards
- Job details: title, company, location, salary, description
- Posted date display
- External link to original job posting
- Demo mode with hardcoded frontend jobs

**How it works:**
1. User enters job title and location
2. Selects search method (API or Deep Search)
3. Clicks "Execute" button
4. API call to `/api/jobs/search`
5. On API failure, uses demo mode with mock jobs
6. Displays results in responsive grid
7. Each card shows job details with external link

**Demo Mode:**
- Returns 5 hardcoded frontend jobs when searching for frontend-related roles
- Returns generic jobs for other search terms
- Includes realistic salaries and descriptions

---

#### Voice Interview (`/dashboard/interview`)
The interview practice page allows practicing with AI-generated questions.

**Features:**
- Session creation with job title and resume
- AI-generated interview questions
- Text input for answers
- Voice recording simulation
- Question navigation (previous/next)
- AI feedback on answers
- Overall performance score
- Results summary with per-question feedback

**How it works:**
1. User enters target job title and pastes resume
2. Clicks "Start Interview Session"
3. API call to `/api/interview/create`
4. Returns session ID and questions array
5. User answers each question:
   - Type answer in text area
   - Or click "Use Voice" for simulated voice input
6. Submit answer to `/api/interview/answer`
7. Gets score and feedback for each answer
8. After all questions, shows results page
9. Displays overall score and per-question breakdown

**Question Generation:**
- 5 questions per session
- Mix of Behavioral and Technical categories
- Varying difficulty levels (Easy, Medium, Hard)
- Generated based on job title

**Voice Feature:**
- Currently simulates voice recording (2-second delay)
- Returns mock transcript
- In production, would record and send audio to backend

---

#### Comms Grid (`/dashboard/email`)
The email automation page handles bulk email campaigns.

**Features:**
- SMTP server configuration
- Email recipient management
- AI-powered email template generation
- Bulk email sending with tracking
- Email campaign analytics
- Multi-step wizard interface

**How it works:**

**Step 1 - Server Configuration:**
1. User enters SMTP credentials (email, app password, host, port)
2. Clicks "Initialize Uplink"
3. API call to `/api/email/connect`
4. On success, marks connection as online
5. Proceeds to next step

**Step 2 - Target Vectors:**
1. User adds recipients (name, email, company, role)
2. Recipients added to list
3. Can remove individual recipients
4. Click "Proceed to Payload" when done

**Step 3 - Payload Construction:**
1. User enters email subject and body
2. Can use AI generator ("Auto-Generate" button)
3. API call to `/api/email/generate-template`
4. Uses variables: `{{name}}`, `{{company}}`, `{{role}}`
5. Click "Finalize Payload"

**Step 4 - Broadcast Control:**
1. Shows total targets and delay setting
2. Click "Execute Broadcast"
3. API call to `/api/email/send`
4. Sends to all recipients with tracking enabled

**Step 5 - Telemetry:**
1. Shows campaign analytics:
   - Total transmissions
   - Engagement rate (open rate %)
   - Failed count
2. Displays tracking table with:
   - Recipient name
   - Email address
   - Status (sent/opened/failed)
   - Timestamp

---

#### Profile (`/dashboard/profile`)
The profile page manages user information.

**Features:**
- Display user avatar (first letter of name)
- View and edit profile information
- Fields: Name, Job Title, Location, Bio
- Read-only display of email and join date
- Edit mode toggle
- API sync for profile updates

**How it works:**
1. User views profile with current information
2. Clicks "Edit Dossier" to enter edit mode
3. Modifies fields (name, job title, location, bio)
4. Clicks "Save Parameters"
5. API call to `/api/auth/profile`
6. On success, shows toast and exits edit mode
7. Profile information updates immediately

**Read-only Information:**
- Email (from authentication)
- Join date (from account creation)
- User role

---

#### Startup Map (`/dashboard/startup-map`)
The startup discovery map shows startups worldwide.

**Features:**
- Interactive world map with Leaflet
- Startup markers on map
- List and map view toggle
- Filter sidebar (search, tags, city, industry)
- Startup cards with details
- Bottom panel with startup list
- Responsive grid layout
- Custom marker icons
- Map controls (zoom in/out/reset)
- Legend display

**How it works:**

**Page Layout:**
1. Header with title and view toggle buttons
2. Sidebar with filters (desktop only)
3. Main area with map or list view
4. Bottom panel with startup list

**Map View:**
- Uses React-Leaflet with OpenStreetMap CARTO dark tiles
- Custom marker icons (cyan for normal, purple for selected)
- Click marker to select startup
- Selected startup shows info panel with details
- Radius circle shows around selected startup
- Map controls: Zoom In, Zoom Out, Reset View
- Legend in bottom-left corner

**List View:**
- Responsive grid of startup cards
- AnimatePresence for smooth transitions
- Shows all startups in card format

**Filtering:**
- Search input filters by name/domain
- Industry tags filter by category (AI, SaaS, Fintech, etc.)
- City selection filters by location
- Clear filters button
- Shows total count of startups found

**Available Industries:**
- AI, SaaS, Fintech, Healthcare, EdTech, E-commerce, Cybersecurity, Cloud, Data Analytics, Robotics, CleanTech, Retail

**Available Cities:**
- Bangalore, San Francisco, New York, Boston, London, Singapore, Berlin, Tokyo, Sydney, Toronto

**Startup Data Model:**
```typescript
interface Startup {
  id: string;
  name: string;
  domain: string;
  city: string;
  country: string;
  lat: number;
  lng: number;
  tags: string[];
  description?: string;
  website?: string;
  founded?: number;
  stage?: string;
  employees?: string;
  source: string;
  rating?: number;
  reviews?: number;
  phone?: string;
  address?: string;
}
```

**Data Fetching:**
1. Page makes API call to `/api/startups` with filter params
2. Returns startup list with location data
3. Markers plotted on map at lat/lng coordinates
4. Clicking marker selects startup and centers map

**Components Used:**
- `StartupMap` - Interactive Leaflet map
- `SidebarFilters` - Filter panel
- `StartupCard` - Detailed card view
- `StartupCardCompact` - Compact card for bottom panel

---

## 🔌 API Endpoints Summary

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/auth/register` | POST | Register new user |
| `/api/auth/login` | POST | Login user |
| `/api/auth/logout` | POST | Logout user |
| `/api/auth/me` | GET | Get current user |
| `/api/auth/profile` | PUT | Update profile |
| `/api/resume/analyze` | POST | Analyze resume |
| `/api/score/pdf` | POST | Score PDF resume |
| `/api/score/text` | POST | Score text resume |
| `/api/score/ai` | POST | AI-powered scoring |
| `/api/jobs/search` | POST | Search jobs |
| `/api/jobs/explore` | POST | Explore jobs |
| `/api/jobs/sources` | GET | Get job sources |
| `/api/interview/create` | POST | Create interview |
| `/api/interview/answer` | POST | Submit answer |
| `/api/interview/voice` | POST | Submit voice answer |
| `/api/email/connect` | POST | Connect email |
| `/api/email/send` | POST | Send emails |
| `/api/email/tracking` | GET | Get tracking |
| `/api/email/generate-template` | POST | AI template |
| `/api/huggingface/logs/run` | GET | Get run logs |
| `/api/huggingface/logs/build` | GET | Get build logs |
| `/api/healthz` | GET | Health check |

---

## 🛠️ Technology Stack

### Frontend
- **React 19** - UI Framework
- **Vite** - Build tool
- **TanStack Query** - Data fetching
- **wouter** - Routing
- **Radix UI** - UI primitives
- **Tailwind CSS** - Styling
- **Recharts** - Charts
- **Leaflet** - Maps
- **Lucide React** - Icons

### Backend
- **Express 5** - Web framework
- **TypeScript** - Language
- **Firebase Admin** - Authentication
- **Drizzle ORM** - Database
- **Pino** - Logging
- **Axios** - HTTP client

### External Services
- **HuggingFace** - AI models (Phi-3, TinyLlama)
- **Firebase** - Authentication
- **PostgreSQL** - Database
- **SendGrid/SMTP** - Email

---

## 🚀 Running the Application

```bash
# Install dependencies
cd Career-Pathfinder/Career-Pathfinder
pnpm install

# Run Backend (Terminal 1)
cd artifacts/api-server
set DATABASE_URL=postgres://localhost:5432/test
set PORT=3001
pnpm run dev

# Run Frontend (Terminal 2)
cd artifacts/ats-scanner
set PORT=5173
pnpm run dev
```

---

## 📝 Environment Variables

### Backend (.env)
```env
PORT=3001
NODE_ENV=development
DATABASE_URL=postgres://...
SESSION_SECRET=your-secret
HF_TOKEN=hf_...
```

### Frontend
```env
PORT=5173
VITE_API_URL=http://localhost:3001
VITE_BASE_URL=/
```

---

## 🔄 API Fallback Strategy

The application implements a robust fallback strategy:

1. **Primary**: Try HuggingFace Space API
2. **Secondary**: Try HuggingFace Inference API
3. **Fallback**: Return mock data based on request parameters

This ensures the application remains functional even when external APIs are unavailable.

---

## 👥 Target Users

- **Job Seekers**: Looking to optimize resumes and find jobs
- **Career Changers**: Exploring new career paths
- **Fresh Graduates**: Starting their career journey
- **Professionals**: Seeking advancement opportunities

---

## 📊 Age Groups Supported

The platform is designed to support **all age groups**:

1. **Students (16-24)**: 
   - Resume building basics
   - Internship search
   - Entry-level positions

2. **Early Career (25-35)**:
   - Career advancement
   - Skill upgrading
   - Mid-level positions

3. **Mid-Career (35-50)**:
   - Leadership roles
   - Career pivots
   - Industry transition

4. **Senior Professionals (50+)**:
   - Executive positions
   - Consulting opportunities
   - Mentorship roles

---

*Last Updated: 2026-03-28*

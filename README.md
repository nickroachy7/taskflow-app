# TaskFlow - Modern Task Management App

> 🚀 A beautiful, real-time task management application built entirely by AI agents

## 🎯 Project Vision

TaskFlow is a modern, collaborative task management application designed for small teams and individuals. It combines the simplicity of a todo list with powerful features like real-time collaboration, smart categorization, and productivity analytics.

## ✨ Core Features

### 1. Task Management
- **Create, Edit, Delete Tasks** - Full CRUD operations with rich text descriptions
- **Priority Levels** - High, Medium, Low with visual indicators
- **Due Dates** - Calendar picker with reminder notifications
- **Labels/Tags** - Colorful labels for categorization
- **Subtasks** - Break down complex tasks into smaller steps
- **Task Templates** - Save and reuse common task structures

### 2. Organization
- **Projects** - Group related tasks into projects
- **Boards** - Kanban-style boards (To Do, In Progress, Done)
- **Lists** - Simple list view with sorting and filtering
- **Search** - Full-text search across all tasks
- **Filters** - Filter by status, priority, label, assignee, due date

### 3. Collaboration (Future Phase)
- **Team Workspaces** - Invite team members
- **Task Assignment** - Assign tasks to team members
- **Comments** - Discuss tasks with threaded comments
- **Activity Feed** - See what's happening across the team
- **@Mentions** - Notify specific team members

### 4. Productivity
- **Dashboard** - Overview of tasks, deadlines, and progress
- **Calendar View** - See tasks on a calendar
- **Analytics** - Track completion rates and productivity trends
- **Focus Mode** - Distraction-free task view
- **Keyboard Shortcuts** - Power user navigation

### 5. User Experience
- **Dark/Light Theme** - System preference or manual toggle
- **Responsive Design** - Works on desktop, tablet, mobile
- **Drag & Drop** - Reorder tasks and move between boards
- **Offline Support** - Work offline, sync when connected
- **Undo/Redo** - Never lose work accidentally

## 🏗️ Technical Architecture

### Frontend
- **Framework**: React 18 with TypeScript
- **State Management**: Zustand
- **Styling**: Tailwind CSS + Radix UI primitives
- **Routing**: React Router v6
- **Forms**: React Hook Form + Zod validation
- **Data Fetching**: TanStack Query
- **Animations**: Framer Motion
- **Icons**: Lucide React

### Backend
- **Runtime**: Node.js with TypeScript
- **Framework**: Express.js
- **Database**: PostgreSQL via Supabase
- **Auth**: Supabase Auth (email, OAuth)
- **Real-time**: Supabase Realtime subscriptions
- **API**: RESTful with OpenAPI spec
- **Validation**: Zod schemas

### Infrastructure
- **Hosting**: Vercel (frontend) + Supabase (backend)
- **CI/CD**: GitHub Actions
- **Testing**: Vitest + Testing Library + Playwright
- **Linting**: ESLint + Prettier
- **Type Checking**: TypeScript strict mode

## 📁 Project Structure

```
taskflow-app/
├── src/
│   ├── components/        # Reusable UI components
│   │   ├── ui/           # Base components (Button, Input, etc.)
│   │   ├── tasks/        # Task-related components
│   │   ├── projects/     # Project components
│   │   └── layout/       # Layout components
│   ├── pages/            # Route pages
│   ├── hooks/            # Custom React hooks
│   ├── stores/           # Zustand stores
│   ├── lib/              # Utilities and helpers
│   ├── types/            # TypeScript types
│   ├── api/              # API client functions
│   └── styles/           # Global styles
├── supabase/
│   ├── migrations/       # Database migrations
│   └── functions/        # Edge functions
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
└── docs/                 # Documentation
```

## 🗄️ Database Schema

### Core Tables

```sql
-- Users (managed by Supabase Auth)
users (
  id uuid PRIMARY KEY,
  email text UNIQUE,
  full_name text,
  avatar_url text,
  created_at timestamptz
)

-- Projects
projects (
  id uuid PRIMARY KEY,
  name text NOT NULL,
  description text,
  color text,
  owner_id uuid REFERENCES users,
  created_at timestamptz,
  updated_at timestamptz
)

-- Tasks
tasks (
  id uuid PRIMARY KEY,
  title text NOT NULL,
  description text,
  status text DEFAULT 'todo', -- todo, in_progress, done
  priority text DEFAULT 'medium', -- low, medium, high
  due_date timestamptz,
  project_id uuid REFERENCES projects,
  parent_task_id uuid REFERENCES tasks, -- for subtasks
  created_by uuid REFERENCES users,
  assigned_to uuid REFERENCES users,
  position integer, -- for ordering
  created_at timestamptz,
  updated_at timestamptz,
  completed_at timestamptz
)

-- Labels
labels (
  id uuid PRIMARY KEY,
  name text NOT NULL,
  color text NOT NULL,
  project_id uuid REFERENCES projects
)

-- Task Labels (junction table)
task_labels (
  task_id uuid REFERENCES tasks,
  label_id uuid REFERENCES labels,
  PRIMARY KEY (task_id, label_id)
)
```

## 🚦 Development Phases

### Phase 1: Foundation (MVP)
- [ ] Project setup (Vite, TypeScript, Tailwind)
- [ ] Basic UI components (Button, Input, Card, Modal)
- [ ] Task CRUD operations
- [ ] Simple list view
- [ ] Local storage persistence
- [ ] Dark/Light theme

### Phase 2: Core Features
- [ ] Supabase integration
- [ ] User authentication
- [ ] Projects and organization
- [ ] Kanban board view
- [ ] Due dates and reminders
- [ ] Labels and filtering

### Phase 3: Polish
- [ ] Drag and drop
- [ ] Keyboard shortcuts
- [ ] Search functionality
- [ ] Dashboard with stats
- [ ] Animations and transitions
- [ ] Mobile responsiveness

### Phase 4: Advanced (Future)
- [ ] Real-time collaboration
- [ ] Comments and activity
- [ ] Calendar view
- [ ] Analytics
- [ ] Offline support
- [ ] API documentation

## 🎨 Design System

### Colors
- **Primary**: Blue (#3B82F6)
- **Success**: Green (#10B981)
- **Warning**: Amber (#F59E0B)
- **Error**: Red (#EF4444)
- **Neutral**: Slate grays

### Typography
- **Font**: Inter (system fallback)
- **Headings**: Semibold
- **Body**: Regular

### Spacing
- 4px grid system
- Consistent padding/margins

## 🏃 Getting Started

```bash
# Clone the repository
git clone https://github.com/nickroachy7/taskflow-app.git
cd taskflow-app

# Install dependencies
npm install

# Start development server
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

## 📄 License

MIT License - feel free to use this for your own projects!

---

Built with ❤️ by the AI Agent Workforce

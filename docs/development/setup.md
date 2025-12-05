# Development Setup Guide

## Prerequisites

- **Node.js** 18+ (LTS recommended)
- **npm** or **yarn** package manager
- **PostgreSQL** 14+ (local installation or Docker)
- **Git** for version control
- **VS Code** or preferred IDE

## Project Structure

```
taskflow-app/
├── frontend/                 # React application
│   ├── src/
│   │   ├── components/       # Reusable UI components
│   │   ├── pages/           # Route components
│   │   ├── hooks/           # Custom React hooks
│   │   ├── services/        # API communication
│   │   ├── types/           # TypeScript definitions
│   │   └── utils/           # Helper functions
│   ├── public/              # Static assets
│   └── package.json
├── backend/                  # Node.js API
│   ├── src/
│   │   ├── routes/          # Express routes
│   │   ├── controllers/     # Business logic
│   │   ├── services/        # Data access layer
│   │   ├── middleware/      # Custom middleware
│   │   ├── models/          # Database models
│   │   └── utils/           # Helper functions
│   ├── prisma/              # Database schema & migrations
│   └── package.json
├── docs/                    # Project documentation
└── docker-compose.yml       # Development environment
```

## Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/nickroachy7/taskflow-app.git
cd taskflow-app
```

### 2. Setup Backend

```bash
cd backend
npm install

# Copy environment template
cp .env.example .env

# Configure your database URL in .env
DATABASE_URL="postgresql://username:password@localhost:5432/taskflow"
JWT_SECRET="your-super-secret-jwt-key"
PORT=5000
```

### 3. Setup Database

```bash
# Using Docker (recommended)
docker run --name taskflow-db -e POSTGRES_PASSWORD=password -e POSTGRES_DB=taskflow -p 5432:5432 -d postgres:14

# Or install PostgreSQL locally and create database
createdb taskflow
```

### 4. Run Database Migrations

```bash
cd backend
npx prisma migrate dev
npx prisma generate
```

### 5. Setup Frontend

```bash
cd ../frontend
npm install

# Copy environment template
cp .env.example .env.local

# Configure API URL in .env.local
VITE_API_URL=http://localhost:5000/api
```

## Development Workflow

### Starting Development Servers

#### Option 1: Using npm scripts (recommended)

```bash
# From project root
npm run dev
```

This starts both frontend (port 3000) and backend (port 5000) concurrently.

#### Option 2: Manual startup

```bash
# Terminal 1 - Backend
cd backend
npm run dev

# Terminal 2 - Frontend
cd frontend
npm run dev
```

### Available Scripts

#### Root Level Scripts
- `npm run dev` - Start both frontend and backend
- `npm run build` - Build both applications
- `npm run test` - Run all tests
- `npm run lint` - Lint all code

#### Frontend Scripts
- `npm run dev` - Start Vite dev server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run test` - Run Vitest tests
- `npm run lint` - ESLint checking

#### Backend Scripts
- `npm run dev` - Start with nodemon
- `npm run build` - Compile TypeScript
- `npm run start` - Start production server
- `npm run test` - Run Jest tests
- `npm run db:migrate` - Run Prisma migrations
- `npm run db:seed` - Seed database with sample data

## Docker Development (Alternative)

### Using Docker Compose

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

Services:
- Frontend: http://localhost:3000
- Backend: http://localhost:5000
- Database: localhost:5432

## IDE Configuration

### VS Code Extensions (Recommended)

```json
{
  "recommendations": [
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "ms-vscode.vscode-typescript-next",
    "prisma.prisma",
    "ms-vscode-remote.remote-containers"
  ]
}
```

### VS Code Settings

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

## Git Workflow

### Branch Naming Convention
- `feature/task-description` - New features
- `fix/bug-description` - Bug fixes
- `docs/update-description` - Documentation updates
- `refactor/component-name` - Code refactoring

### Commit Message Format
```
type(scope): description

Examples:
feat(auth): add user registration endpoint
fix(tasks): resolve task deletion bug
docs(api): update endpoint documentation
```

## Testing Strategy

### Frontend Testing
- **Unit Tests**: React Testing Library + Vitest
- **Component Tests**: Storybook for component documentation
- **E2E Tests**: Playwright (Phase 2)

### Backend Testing
- **Unit Tests**: Jest for business logic
- **Integration Tests**: Supertest for API endpoints
- **Database Tests**: In-memory SQLite for fast testing

## Troubleshooting

### Common Issues

1. **Database Connection Error**
   - Check PostgreSQL is running
   - Verify DATABASE_URL in .env file
   - Ensure database exists

2. **Port Already in Use**
   - Change PORT in backend .env
   - Update VITE_API_URL in frontend .env.local

3. **TypeScript Errors**
   - Run `npm run build` to check for compilation errors
   - Ensure all types are properly imported

4. **Prisma Issues**
   - Run `npx prisma generate` after schema changes
   - Reset database: `npx prisma migrate reset`
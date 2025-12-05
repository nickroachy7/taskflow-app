# TaskFlow - Development Setup Guide

## Prerequisites

### Required Software
- **Node.js** (v18.0.0 or higher)
- **npm** or **yarn** package manager
- **Docker** and **Docker Compose**
- **PostgreSQL** (v14 or higher)
- **Git**

### Recommended Tools
- **VS Code** with extensions:
  - TypeScript
  - Prettier
  - ESLint
  - Prisma
  - Tailwind CSS IntelliSense

## Initial Setup

### 1. Clone Repository
```bash
git clone https://github.com/nickroachy7/taskflow-app.git
cd taskflow-app
```

### 2. Environment Configuration

#### Backend Environment (.env)
```env
# Database
DATABASE_URL="postgresql://taskflow:password@localhost:5432/taskflow_dev"
DATABASE_URL_TEST="postgresql://taskflow:password@localhost:5432/taskflow_test"

# JWT
JWT_SECRET="your-super-secret-jwt-key-change-in-production"
JWT_REFRESH_SECRET="your-refresh-secret-key"
JWT_EXPIRES_IN="15m"
JWT_REFRESH_EXPIRES_IN="7d"

# Server
PORT=3001
NODE_ENV="development"

# CORS
FRONTEND_URL="http://localhost:3000"

# File Upload
MAX_FILE_SIZE="10MB"
UPLOAD_DIR="uploads"
```

#### Frontend Environment (.env.local)
```env
VITE_API_URL="http://localhost:3001/api"
VITE_APP_NAME="TaskFlow"
VITE_ENVIRONMENT="development"
```

### 3. Database Setup with Docker

#### Start PostgreSQL Container
```bash
docker run --name taskflow-postgres \
  -e POSTGRES_DB=taskflow_dev \
  -e POSTGRES_USER=taskflow \
  -e POSTGRES_PASSWORD=password \
  -p 5432:5432 \
  -d postgres:14
```

#### Create Test Database
```bash
docker exec -it taskflow-postgres psql -U taskflow -c "CREATE DATABASE taskflow_test;"
```

### 4. Backend Setup
```bash
cd backend
npm install
npx prisma generate
npx prisma db push
npm run dev
```

### 5. Frontend Setup
```bash
cd frontend
npm install
npm run dev
```

## Development Workflow

### Git Branch Strategy
- `main` - Production ready code
- `develop` - Integration branch
- `feature/*` - Feature branches
- `fix/*` - Bug fix branches

### Code Standards

#### TypeScript Configuration
- Strict mode enabled
- Path mapping configured
- Consistent import/export patterns

#### ESLint Rules
- Airbnb TypeScript configuration
- React hooks rules
- Import order enforcement
- No unused variables/imports

#### Prettier Configuration
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

### Testing Commands

#### Backend Testing
```bash
cd backend
npm run test          # Run all tests
npm run test:watch    # Watch mode
npm run test:coverage # Coverage report
```

#### Frontend Testing
```bash
cd frontend
npm run test          # Run all tests
npm run test:watch    # Watch mode
npm run test:e2e      # End-to-end tests
```

### Database Management

#### Prisma Commands
```bash
npx prisma generate    # Generate Prisma client
npx prisma db push     # Push schema changes
npx prisma db seed     # Seed database
npx prisma studio      # Database GUI
```

#### Database Migrations
```bash
npx prisma migrate dev --name migration_name
npx prisma migrate deploy
```

## Docker Development

### Full Stack Development
```bash
docker-compose up -d
```

### Docker Compose Services
- `postgres` - Database server
- `backend` - API server
- `frontend` - React development server

## Troubleshooting

### Common Issues

#### Database Connection Errors
- Ensure PostgreSQL is running
- Verify DATABASE_URL environment variable
- Check database credentials

#### Port Conflicts
- Frontend: Change port in `vite.config.ts`
- Backend: Change PORT in `.env`
- Database: Change port mapping in Docker

#### Package Installation Issues
- Clear node_modules and package-lock.json
- Use `npm ci` for clean install
- Check Node.js version compatibility

### Debug Configuration

#### VS Code Launch Config
```json
{
  "type": "node",
  "request": "launch",
  "name": "Debug Backend",
  "program": "${workspaceFolder}/backend/src/index.ts",
  "envFile": "${workspaceFolder}/backend/.env",
  "runtimeArgs": ["-r", "ts-node/register"]
}
```

## Performance Optimization

### Development Mode
- Use React Fast Refresh
- Enable Vite HMR
- Database query logging
- API response time monitoring

### Production Build
```bash
# Frontend
npm run build
npm run preview

# Backend
npm run build
npm start
```
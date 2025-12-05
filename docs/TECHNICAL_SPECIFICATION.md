# TaskFlow - Technical Specification

## Database Schema

### Users Table
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  avatar_url VARCHAR(500),
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Tasks Table
```sql
CREATE TABLE tasks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(255) NOT NULL,
  description TEXT,
  status VARCHAR(50) DEFAULT 'pending',
  priority VARCHAR(50) DEFAULT 'medium',
  due_date TIMESTAMP,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  category_id UUID REFERENCES categories(id),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Categories Table
```sql
CREATE TABLE categories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(100) NOT NULL,
  color VARCHAR(7) DEFAULT '#3B82F6',
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## API Endpoints

### Authentication
- `POST /api/auth/register` - User registration
- `POST /api/auth/login` - User login
- `POST /api/auth/logout` - User logout
- `GET /api/auth/me` - Get current user
- `POST /api/auth/refresh` - Refresh JWT token

### Tasks
- `GET /api/tasks` - Get user tasks (with filters)
- `POST /api/tasks` - Create new task
- `GET /api/tasks/:id` - Get specific task
- `PUT /api/tasks/:id` - Update task
- `DELETE /api/tasks/:id` - Delete task

### Categories
- `GET /api/categories` - Get user categories
- `POST /api/categories` - Create category
- `PUT /api/categories/:id` - Update category
- `DELETE /api/categories/:id` - Delete category

### Users
- `GET /api/users/profile` - Get user profile
- `PUT /api/users/profile` - Update user profile
- `POST /api/users/upload-avatar` - Upload avatar

## Frontend Component Structure

### Pages
- `LoginPage` - User authentication
- `DashboardPage` - Main task overview
- `TasksPage` - Task list with filters
- `ProfilePage` - User profile management
- `NotFoundPage` - 404 error page

### Components
- `TaskCard` - Individual task display
- `TaskForm` - Task creation/editing
- `TaskFilter` - Filter and search controls
- `CategoryBadge` - Category display
- `Layout` - Main app layout
- `Header` - Navigation header
- `Sidebar` - Navigation sidebar

### Hooks
- `useAuth` - Authentication state
- `useTasks` - Task management
- `useCategories` - Category management
- `useApi` - API call wrapper

## Security Requirements

### Authentication
- JWT tokens with 15-minute expiration
- Refresh tokens with 7-day expiration
- Password hashing with bcrypt (12 rounds)
- Rate limiting on auth endpoints

### Authorization
- User can only access their own tasks
- JWT token validation on protected routes
- Input validation and sanitization

### Data Protection
- HTTPS only in production
- CORS configuration
- SQL injection prevention with Prisma
- XSS protection with Content Security Policy

## Performance Requirements

### Frontend
- Initial load time < 2 seconds
- Page transitions < 500ms
- Optimistic updates for better UX
- Image lazy loading

### Backend
- API response time < 200ms
- Database query optimization
- Proper indexing on frequently queried fields
- Connection pooling

### Caching Strategy
- React Query for API caching (5-minute stale time)
- Database connection pooling
- Static asset caching with proper headers

## Testing Strategy

### Frontend Testing
- Unit tests with Jest and React Testing Library
- Component testing for all UI components
- Integration tests for user workflows
- E2E tests with Playwright

### Backend Testing
- Unit tests for controllers and middleware
- Integration tests for API endpoints
- Database testing with test database
- Performance testing for API endpoints

## Deployment Architecture

### Development
- Local development with Docker Compose
- Hot reload for frontend and backend
- Test database for development

### Staging
- Docker containers on cloud platform
- Separate staging database
- CI/CD pipeline testing

### Production
- Load balancer for high availability
- Database replicas for read scaling
- CDN for static assets
- Monitoring and logging
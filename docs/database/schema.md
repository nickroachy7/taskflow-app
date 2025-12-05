# Database Schema Design

## Overview

TaskFlow uses PostgreSQL as the primary database with Prisma ORM for type-safe database operations. The schema is designed to support core task management features with extensibility for future enhancements.

## Entity Relationship Diagram

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    Users    │    │  Projects   │    │    Tasks    │    │ Categories  │
│             │    │             │    │             │    │             │
│ id (PK)     │───►│ id (PK)     │───►│ id (PK)     │    │ id (PK)     │
│ email       │    │ name        │    │ title       │◄──►│ name        │
│ password    │    │ description │    │ description │    │ color       │
│ firstName   │    │ ownerId (FK)│    │ projectId FK│    │ userId (FK) │
│ lastName    │    │ createdAt   │    │ assigneeId  │    │ createdAt   │
│ role        │    │ updatedAt   │    │ categoryId  │    │ updatedAt   │
│ createdAt   │    │             │    │ priority    │    │             │
│ updatedAt   │    │             │    │ status      │    │             │
│             │    │             │    │ dueDate     │    │             │
│             │    │             │    │ createdAt   │    │             │
│             │    │             │    │ updatedAt   │    │             │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

## Prisma Schema

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  password  String
  firstName String
  lastName  String
  role      UserRole @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Relations
  ownedProjects Project[]   @relation("ProjectOwner")
  assignedTasks Task[]      @relation("TaskAssignee")
  categories    Category[]
  
  @@map("users")
}

model Project {
  id          String   @id @default(cuid())
  name        String
  description String?
  ownerId     String
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  // Relations
  owner User   @relation("ProjectOwner", fields: [ownerId], references: [id], onDelete: Cascade)
  tasks Task[]

  @@map("projects")
}

model Task {
  id          String     @id @default(cuid())
  title       String
  description String?
  projectId   String
  assigneeId  String?
  categoryId  String?
  priority    Priority   @default(MEDIUM)
  status      TaskStatus @default(TODO)
  dueDate     DateTime?
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt

  // Relations
  project  Project   @relation(fields: [projectId], references: [id], onDelete: Cascade)
  assignee User?     @relation("TaskAssignee", fields: [assigneeId], references: [id], onDelete: SetNull)
  category Category? @relation(fields: [categoryId], references: [id], onDelete: SetNull)

  @@map("tasks")
}

model Category {
  id        String   @id @default(cuid())
  name      String
  color     String   @default("#6366f1")
  userId    String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Relations
  user  User   @relation(fields: [userId], references: [id], onDelete: Cascade)
  tasks Task[]

  @@unique([name, userId]) // User can't have duplicate category names
  @@map("categories")
}

enum UserRole {
  ADMIN
  USER
}

enum TaskStatus {
  TODO
  IN_PROGRESS
  IN_REVIEW
  DONE
  CANCELLED
}

enum Priority {
  LOW
  MEDIUM
  HIGH
  URGENT
}
```

## Table Specifications

### Users Table
| Column    | Type     | Constraints                    | Description                |
|-----------|----------|--------------------------------|----------------------------|
| id        | String   | PRIMARY KEY, CUID             | Unique user identifier     |
| email     | String   | UNIQUE, NOT NULL              | User's email address       |
| password  | String   | NOT NULL                      | Hashed password            |
| firstName | String   | NOT NULL                      | User's first name          |
| lastName  | String   | NOT NULL                      | User's last name           |
| role      | Enum     | DEFAULT 'USER'                | User role (ADMIN/USER)     |
| createdAt | DateTime | DEFAULT now()                 | Account creation timestamp |
| updatedAt | DateTime | AUTO UPDATE                   | Last update timestamp      |

### Projects Table
| Column      | Type     | Constraints           | Description                  |
|-------------|----------|-----------------------|------------------------------|
| id          | String   | PRIMARY KEY, CUID     | Unique project identifier    |
| name        | String   | NOT NULL              | Project name                 |
| description | String   | NULLABLE              | Project description          |
| ownerId     | String   | FOREIGN KEY, NOT NULL | Reference to Users.id        |
| createdAt   | DateTime | DEFAULT now()         | Project creation timestamp   |
| updatedAt   | DateTime | AUTO UPDATE           | Last update timestamp        |

### Tasks Table
| Column      | Type     | Constraints           | Description                  |
|-------------|----------|-----------------------|------------------------------|
| id          | String   | PRIMARY KEY, CUID     | Unique task identifier       |
| title       | String   | NOT NULL              | Task title                   |
| description | String   | NULLABLE              | Task description             |
| projectId   | String   | FOREIGN KEY, NOT NULL | Reference to Projects.id     |
| assigneeId  | String   | FOREIGN KEY, NULLABLE | Reference to Users.id        |
| categoryId  | String   | FOREIGN KEY, NULLABLE | Reference to Categories.id   |
| priority    | Enum     | DEFAULT 'MEDIUM'      | Task priority level          |
| status      | Enum     | DEFAULT 'TODO'        | Current task status          |
| dueDate     | DateTime | NULLABLE              | Task due date                |
| createdAt   | DateTime | DEFAULT now()         | Task creation timestamp      |
| updatedAt   | DateTime | AUTO UPDATE           | Last update timestamp        |

### Categories Table
| Column    | Type     | Constraints                    | Description                |
|-----------|----------|--------------------------------|----------------------------|
| id        | String   | PRIMARY KEY, CUID             | Unique category identifier |
| name      | String   | NOT NULL                      | Category name              |
| color     | String   | DEFAULT '#6366f1'             | Category color (hex)       |
| userId    | String   | FOREIGN KEY, NOT NULL         | Reference to Users.id      |
| createdAt | DateTime | DEFAULT now()                 | Creation timestamp         |
| updatedAt | DateTime | AUTO UPDATE                   | Last update timestamp      |

## Indexes

```sql
-- Users table indexes
CREATE UNIQUE INDEX users_email_idx ON users(email);

-- Projects table indexes  
CREATE INDEX projects_owner_id_idx ON projects(owner_id);
CREATE INDEX projects_created_at_idx ON projects(created_at);

-- Tasks table indexes
CREATE INDEX tasks_project_id_idx ON tasks(project_id);
CREATE INDEX tasks_assignee_id_idx ON tasks(assignee_id);
CREATE INDEX tasks_category_id_idx ON tasks(category_id);
CREATE INDEX tasks_status_idx ON tasks(status);
CREATE INDEX tasks_priority_idx ON tasks(priority);
CREATE INDEX tasks_due_date_idx ON tasks(due_date);
CREATE INDEX tasks_created_at_idx ON tasks(created_at);

-- Categories table indexes
CREATE INDEX categories_user_id_idx ON categories(user_id);
CREATE UNIQUE INDEX categories_name_user_id_idx ON categories(name, user_id);
```

## Data Validation Rules

### User Validation
- Email must be valid format and unique
- Password minimum 8 characters with complexity requirements
- First/Last name required, 1-50 characters
- Role must be valid enum value

### Project Validation
- Name required, 1-100 characters
- Description optional, max 1000 characters
- Owner must be valid user

### Task Validation
- Title required, 1-200 characters
- Description optional, max 2000 characters
- Project must exist and user must have access
- Priority/Status must be valid enum values
- Due date must be future date (if provided)

### Category Validation
- Name required, 1-50 characters
- Color must be valid hex color code
- User can't have duplicate category names

## Sample Data

```sql
-- Sample Users
INSERT INTO users (id, email, password, first_name, last_name, role) VALUES
('user_1', 'john.doe@example.com', '$hashed_password', 'John', 'Doe', 'ADMIN'),
('user_2', 'jane.smith@example.com', '$hashed_password', 'Jane', 'Smith', 'USER');

-- Sample Projects
INSERT INTO projects (id, name, description, owner_id) VALUES
('proj_1', 'TaskFlow Development', 'Building the TaskFlow application', 'user_1'),
('proj_2', 'Marketing Campaign', 'Q1 marketing initiatives', 'user_2');

-- Sample Categories
INSERT INTO categories (id, name, color, user_id) VALUES
('cat_1', 'Development', '#10b981', 'user_1'),
('cat_2', 'Design', '#f59e0b', 'user_1'),
('cat_3', 'Marketing', '#ef4444', 'user_2');

-- Sample Tasks
INSERT INTO tasks (id, title, description, project_id, assignee_id, category_id, priority, status) VALUES
('task_1', 'Setup Database Schema', 'Create initial Prisma schema', 'proj_1', 'user_1', 'cat_1', 'HIGH', 'DONE'),
('task_2', 'Design Homepage', 'Create homepage mockup', 'proj_1', 'user_2', 'cat_2', 'MEDIUM', 'IN_PROGRESS');
```

## Migration Strategy

### Initial Migration
```bash
npx prisma migrate dev --name init
```

### Adding New Fields (Example)
```prisma
model Task {
  // ... existing fields
  estimatedHours Int?
  actualHours    Int?
}
```

```bash
npx prisma migrate dev --name add_task_hours
```

### Best Practices
1. Always backup database before major migrations
2. Test migrations on development environment first
3. Use descriptive migration names
4. Keep migrations small and focused
5. Document breaking changes
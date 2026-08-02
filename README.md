# Polyglot Task API

One functional API — a task/project management system — built three times across different backend stacks, with the exact same entities, endpoints, and database schema. Goal: demonstrate that architectural principles are understood independently of language or framework, not just syntax in a single language.

## Why this project

As a junior backend developer with solid foundations in Java/Spring, C#/.NET, and Node.js, this project deliberately shows that the same business logic and API architecture can be applied consistently across different ecosystems — including the specifics of each framework (dependency injection, middleware/filters, ORM approach).

## Stacks

| # | Stack | Language | Framework | ORM |
|---|-------|----------|-----------|-----|
| 1 | [`spring-boot/`](./spring-boot) | Java | Spring Boot | Spring Data JPA (Hibernate) |
| 2 | [`dotnet/`](./dotnet) | C# | ASP.NET Core | Entity Framework Core |
| 3 | [`nodejs/`](./nodejs) | JavaScript/TypeScript | Express (or NestJS) | Prisma |

Each sub-folder contains its own README with stack-specific setup instructions.

## Domain: Task & Project Management

A system where users create projects, manage tasks within those projects, and roles/permissions determine who can do what.

### Entities

**User**
| Field | Type | Notes |
|-------|------|-------|
| id | UUID | primary key |
| email | string | unique |
| passwordHash | string | bcrypt/hashed |
| name | string | |
| createdAt | datetime | |

**Role**
| Field | Type | Notes |
|-------|------|-------|
| id | UUID | primary key |
| name | enum/string | `ADMIN`, `PROJECT_MANAGER`, `MEMBER` |

**Project**
| Field | Type | Notes |
|-------|------|-------|
| id | UUID | primary key |
| name | string | |
| description | string | optional |
| ownerId | UUID | FK → User |
| createdAt | datetime | |

**ProjectMember** (join table User ↔ Project, with role within that project)
| Field | Type | Notes |
|-------|------|-------|
| id | UUID | primary key |
| projectId | UUID | FK → Project |
| userId | UUID | FK → User |
| roleId | UUID | FK → Role |

**Task**
| Field | Type | Notes |
|-------|------|-------|
| id | UUID | primary key |
| projectId | UUID | FK → Project |
| assigneeId | UUID | FK → User, nullable |
| title | string | |
| description | string | optional |
| status | enum | `TODO`, `IN_PROGRESS`, `DONE` |
| dueDate | date | optional |
| createdAt | datetime | |

### Relationships
- A `User` can own multiple `Project`s
- A `User` can be a member of multiple `Project`s via `ProjectMember`, each with a `Role`
- A `Project` has multiple `Task`s
- A `Task` optionally has one `assignee` (`User`)

## Functional scope (v1)

- **Authentication**: registration, login, JWT tokens (access + refresh)
- **Authorization**: role-based — only `ADMIN`/`PROJECT_MANAGER` of a project can assign tasks, members can only update their own tasks
- **Projects**: CRUD, add/remove members with role
- **Tasks**: CRUD, change status, assign to member
- **Users**: registration, fetch/update profile

## Deliberately out of scope (v1)

- No frontend (tested via Swagger/Postman)
- No file uploads/attachments on tasks
- No notifications/emails
- No real-time updates (websockets)

*(Can be added later per stack as an extension, to demonstrate more advanced features.)*

## Shared non-functional requirements (each stack must implement)

- JWT authentication
- Role-based authorization
- Input validation with clear error messages
- Unit tests (services/business logic)
- Integration tests (API endpoints, with test database)
- Docker Compose setup (API + PostgreSQL)
- API documentation (OpenAPI/Swagger)
- Consistent error handling (standardized error response format)
- Own README per stack explaining architectural choices

## API Endpoints (identical across all 3 stacks)

```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/refresh

GET    /api/users/me
PATCH  /api/users/me

GET    /api/projects
POST   /api/projects
GET    /api/projects/{id}
PATCH  /api/projects/{id}
DELETE /api/projects/{id}
POST   /api/projects/{id}/members
DELETE /api/projects/{id}/members/{userId}

GET    /api/projects/{id}/tasks
POST   /api/projects/{id}/tasks
GET    /api/tasks/{id}
PATCH  /api/tasks/{id}
DELETE /api/tasks/{id}
```

## Author

Jelle - Graduate Software Developer student (VIVES)
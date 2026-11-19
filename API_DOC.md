
# Task Management API Documentation

## 1. Overview

The Task Management API is a backend application built with Node.js, Express, Prisma, and PostgreSQL (Supabase).

It allows authenticated users to create, view, update, and delete tasks and subtasks.

## 2. Base URL

Local development:

`http://localhost:3000`

After deployment to Render:

`https://YOUR-APP-NAME.onrender.com`

All task and subtask endpoints begin with `/api`.

## 3. Authentication

Task and subtask endpoints require a JSON Web Token (JWT).

Include the token in the request header:

```http
Authorization: Bearer YOUR_JWT_TOKEN
```

The API also includes authentication routes under `/api/auth`.

The exact registration and login request fields are defined in `routes/auth.js`.

A protected test endpoint is available:

`GET /api/protected`

Example successful response:

```json
{
  "success": true,
  "message": "This is a protected route",
  "user": {
    "id": 1
  }
}
```

## 4. Task Endpoints

| Method | Endpoint | Description | Success Status |
|---|---|---|---|
| GET | /api/tasks | Get all tasks for the authenticated user | 200 |
| GET | /api/tasks/:id | Get one task by ID | 200 |
| POST | /api/tasks | Create a new task | 201 |
| PUT | /api/tasks/:id | Update an existing task | 200 |
| DELETE | /api/tasks/:id | Delete a task | 200 |

### Get All Tasks

**Request**

```http
GET /api/tasks
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example response — 200 OK**

```json
{
  "success": true,
  "count": 0,
  "data": []
}
```

### Get Task by ID

**Request**

```http
GET /api/tasks/1
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example error response — 404 Not Found**

```json
{
  "success": false,
  "error": "Task not found"
}
```

### Create Task

**Request**

```http
POST /api/tasks
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```

The JSON request body must contain the fields required by the `createTask` service in `services/taskServices.js`.

**Example successful response — 201 Created**

```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Complete assignment"
  }
}
```

The task object above is illustrative; actual response fields depend on the Prisma model.

### Update Task

```http
PUT /api/tasks/1
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```

Send the task fields to be updated in the JSON request body.

**Example response — 200 OK**

```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Updated assignment"
  }
}
```

### Delete Task

```http
DELETE /api/tasks/1
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example response — 200 OK**

```json
{
  "success": true,
  "data": {
    "id": 1
  }
}
```

## 5. Subtask Endpoints

| Method | Endpoint | Description | Success Status |
|---|---|---|---|
| GET | /api/tasks/:taskId/subtasks | Get subtasks for a task | 200 |
| GET | /api/subtasks/:id | Get one subtask | 200 |
| POST | /api/tasks/:taskId/subtasks | Create a subtask | 201 |
| PUT | /api/subtasks/:id | Update a subtask | 200 |
| DELETE | /api/subtasks/:id | Delete a subtask | 200 |

### Get Subtasks for a Task

```http
GET /api/tasks/1/subtasks
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example response — 200 OK**

```json
{
  "success": true,
  "count": 0,
  "data": []
}
```

### Get Subtask by ID

```http
GET /api/subtasks/1
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example response — 200 OK**

```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Review code"
  }
}
```

### Create Subtask

```http
POST /api/tasks/1/subtasks
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```

The JSON request body must contain fields accepted by `createSubtask` in `services/taskServices.js`.

**Example response — 201 Created**

```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Review code"
  }
}
```

### Update Subtask

```http
PUT /api/subtasks/1
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```

**Example response — 200 OK**

```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Review updated code"
  }
}
```

### Delete Subtask

```http
DELETE /api/subtasks/1
Authorization: Bearer YOUR_JWT_TOKEN
```

**Example response — 200 OK**

```json
{
  "success": true,
  "data": {
    "id": 1
  }
}
```

## 6. HTTP Status Codes and Error Handling

| Status Code | Meaning |
|---|---|
| 200 | Request completed successfully |
| 201 | Resource created successfully |
| 400 | Invalid request or input |
| 401 | Authentication required or token invalid (subject to authentication middleware) |
| 404 | Requested resource or route not found |
| 500 | Internal server error |

### Example Bad Request

```json
{
  "success": false,
  "error": "Invalid task data"
}
```

### Example Not Found

```json
{
  "success": false,
  "error": "Task not found"
}
```

### Example Server Error

```json
{
  "success": false,
  "error": "An unexpected error occurred"
}
```

The exact error message may vary depending on the service.

## 7. Testing the API

Start the application:

```bash
npm run dev
```

The API runs locally at:

`http://localhost:3000`

Use Postman, Insomnia, or curl to send requests.

Example:

```bash
curl -i http://localhost:3000/api/tasks
```

Because task routes require authentication, a request without a valid JWT should be rejected by the authentication middleware.

## 8. Deployment

The application will be deployed to Render using:

- Runtime: Node.js
- Build command: `npm install`
- Start command: `npm start`
- Database: Supabase PostgreSQL
- Environment variables: `DATABASE_URL`, `JWT_SECRET`, and `PORT`

The final deployed URL will replace the placeholder in Section 2.




## Authentication API Endpoints

### 1. Register a User

**Endpoint:** `POST /api/auth/register`

Creates a new user account.

**Authentication required:** No

**Request body:**

```json
{
  "name": "Alex",
  "email": "alex@example.com",
  "password": "ExamplePassword123"
}
```

**Success response — 201 Created:**

```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": 1,
      "name": "Alex",
      "email": "alex@example.com"
    },
    "token": "YOUR_JWT_TOKEN"
  }
}
```

**Possible errors:**
- 400 — Missing required fields
- 400 — Email already exists
- 500 — Server error

### 2. Login

**Endpoint:** `POST /api/auth/login`

Authenticates an existing user.

**Authentication required:** No

**Request body:**

```json
{
  "email": "alex@example.com",
  "password": "ExamplePassword123"
}
```

**Success response — 200 OK:**

```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": 1,
      "name": "Alex",
      "email": "alex@example.com"
    },
    "token": "YOUR_JWT_TOKEN"
  }
}
```

**Possible errors:**
- 400 — Missing email or password
- 401 — Invalid email or password
- 500 — Server error

### 3. Get Current User

**Endpoint:** `GET /api/auth/me`

Returns the authenticated user's profile.

**Authentication required:** Yes

**Request header:**

`Authorization: Bearer YOUR_JWT_TOKEN`

**Success response — 200 OK:**

```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Alex",
    "email": "alex@example.com"
  }
}
```

**Possible errors:**
- Authentication error — Missing or invalid token
- 500 — Server error

### JWT Authentication

JWT tokens are generated during registration and login.

Tokens expire after 24 hours.

Include the token when accessing protected task, subtask, and profile endpoints:

`Authorization: Bearer YOUR_JWT_TOKEN`

The exact user fields returned depend on the database schema.

# REST API Based To-Do Application

A to-do app built around a REST API design: every action in the interface is an HTTP-style request to a set of endpoints, and every response carries a proper status code.

## Important: the API is simulated

**There is no real server in this project.** The "backend" is JavaScript code inside `index.html` that receives requests and returns responses in the same shape a real HTTP server would (method, URL, JSON body, status code, headers). I did this so the app runs by double-clicking the file, with no installation, no Node.js, and no database.

What this means in practice:

- No network requests are made. The requests are function calls inside the page.
- Data is saved in the browser's `localStorage`, so it stays on one browser on one computer.
- The routing, validation, and status codes follow REST conventions, so the same API design could be moved to a real server (Express, Flask, etc.) with little change.

## How to run

1. Download or clone this folder.
2. Double-click `index.html`.

That's all. It works offline in any modern browser (Chrome, Edge, Firefox, Safari).

## Features

- Create, read, update, and delete tasks
- Mark tasks done or not done
- Priority levels (low, medium, high)
- Filter by All / Active / Done, and search by title
- Delete all completed tasks at once
- **API console** that logs every request and response, and lets you send your own requests with example presets
- Input validation with clear error messages

## API reference

Base path: `/api/todos`. Request and response bodies are JSON.

| Method | Endpoint | Purpose | Success |
|--------|----------|---------|---------|
| GET | `/api/todos` | List tasks. Optional `?completed=true\|false` and `?search=text` | 200 |
| GET | `/api/todos/:id` | Get one task | 200 |
| POST | `/api/todos` | Create a task | 201 + `Location` header |
| PUT | `/api/todos/:id` | Replace a task (title required) | 200 |
| PATCH | `/api/todos/:id` | Change only the fields sent | 200 |
| DELETE | `/api/todos/:id` | Delete a task | 204 (no body) |
| DELETE | `/api/todos?completed=true` | Delete all completed tasks | 200 `{ "deleted": n }` |

### Task object

```json
{
  "id": 4,
  "title": "Write the README",
  "completed": false,
  "priority": "high",
  "createdAt": "2026-10-11T17:00:00.000Z",
  "updatedAt": "2026-10-11T17:00:00.000Z"
}
```

| Field | Rules |
|-------|-------|
| `title` | Required, text, 1 to 120 characters |
| `completed` | Optional, `true` or `false` (default `false`) |
| `priority` | Optional, `low`, `medium`, or `high` (default `medium`) |

### Status codes used

| Code | When |
|------|------|
| 200 OK | Request succeeded |
| 201 Created | A task was created |
| 204 No Content | A task was deleted |
| 400 Bad Request | Malformed JSON, missing body, or an invalid id |
| 404 Not Found | No task with that id, or unknown route |
| 405 Method Not Allowed | Route exists but not for that method (includes an `Allow` header) |
| 422 Unprocessable Entity | The JSON is valid but fails validation (details listed in the response) |

### Example

```
POST /api/todos
{ "title": "Buy milk", "priority": "low" }

201 Created
Location: /api/todos/4
{ "id": 4, "title": "Buy milk", "completed": false, "priority": "low", ... }
```

```
POST /api/todos
{ "title": "", "priority": "urgent" }

422 Unprocessable Entity
{
  "error": "Validation failed",
  "details": [
    "title is required and must be a non-empty string",
    "priority must be one of: low, medium, high"
  ]
}
```

## Project structure

```
todo-rest-api/
  index.html   App, simulated API server, and styles (single file)
  README.md    This file
```

Inside `index.html` the code is split into clearly marked sections:

1. **Simulated server**: routing, validation, storage, status codes
2. **Client API helper**: sends requests and records them
3. **Request log**: the console panel
4. **App UI**: rendering and event handling
5. **Console controls**: manual requests and presets

## Moving to a real backend

Only section 2 needs to change. Replace the call to `Server.handle(...)` in `send()` with a real `fetch(url, { method, body, headers })` and the rest of the app works unchanged, as long as the server follows the API reference above.

## Reset

Click **Reset demo data** in the console panel to restore the starting tasks.

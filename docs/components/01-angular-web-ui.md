[Architecture](../../ARCHITECTURE.md) > [Components](README.md) > Angular Task Management Web UI

# Angular Task Management Web UI

**Type**: Web UI (Single-Page Application)
**Technology**: Angular 17, TypeScript 5.x
**Version**: 1.0.0
**Location**: `todo-list-ui/` repository

**Purpose**:
Provide a responsive, intuitive web interface for users to manage tasks (add, view, complete, delete) with real-time updates and optimistic UI rendering.

**Responsibilities**:
- Render task list with visual status indicators (checkboxes, strikethrough for completed)
- Handle user input for new task creation (text input, form validation)
- Communicate with Spring Boot REST API for all CRUD operations
- Provide immediate UI feedback (optimistic updates before server confirmation)
- Display error messages for failed operations (network errors, validation failures)

**UI Features**:
- **Task Input Form**: Text input field (max 500 chars) with "Add" button
- **Task List Display**: Scrollable list showing all tasks with status indicators
- **Task Actions**: Checkbox (toggle complete/incomplete), Delete button per task
- **Loading Indicators**: Spinner during API calls
- **Error Messages**: Toast notifications for errors

**Dependencies**:
- **Depends on**: Task REST API (Tier 2, Spring Boot) — see [Task REST API Controller](02-task-rest-api-controller.md)
- **Depended by**: End users via web browsers (Chrome, Firefox, Safari, Edge)

**Configuration**:
- `API_BASE_URL`: Spring Boot backend URL (e.g., `https://api.todoapp.example.com`)
- `MAX_TASK_LENGTH`: Maximum task description characters (default: 500)
- `REQUEST_TIMEOUT`: HTTP request timeout in ms (default: 5000)

**Scaling**:
- **Horizontal**: Yes, static assets served via Azure CDN (globally distributed)
- **Vertical**: Not applicable (client-side application)

**Failure Modes**:
- **Backend API unavailable**: Display error message "Unable to connect to server. Please try again."
- **Network timeout**: Show retry button, preserve user input
- **Invalid input**: Client-side validation prevents submission, shows inline error

**Monitoring**:
- **Key metrics**: Page load time, API call latency (browser performance API)
- **Alerts**: Not applicable (client-side only)
- **Logs**: Console errors for debugging (dev mode only)

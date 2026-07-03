# Project Overview

Suraksha Sathi is a safety reporting and worker collaboration system oriented around mining operations. It combines a Node.js/Express backend with a static JavaScript frontend that supports offline request queuing, media upload workflows, and role-based authorization.

## Objectives

- Provide secure user authentication and session management
- Support hazard reporting and incident tracking
- Enable worker-generated media uploads with approval workflows
- Allow offline data capture and deferred synchronization
- Support web push notifications and subscription management
- Store structured safety and operational data in MongoDB

## Functional Requirements

- User registration, login, logout, token refresh, and account updates
- Role-based authorization for admin, manager, training officer, and worker functions
- Hazard report creation, viewing, update, and deletion
- Hazard media upload and retrieval
- Checklist management with checklist items and associated media
- Worker video upload, moderation, approval, rejection, and listing
- Attendance tracking through check-in/check-out actions
- Push notification subscription and message delivery
- Offline queueing of write operations and cached GET responses

## Architecture

The backend is organized into domain-specific routers, controllers, models, and middleware. The frontend uses static HTML pages and shared JavaScript modules to consume the API, with offline support via IndexedDB and a service worker.

### Backend architecture

- `app.js` configures Express middleware, JSON handling, CORS, and route mounts
- `index.js` loads environment variables, connects to MongoDB, and starts the server
- `db/index.js` manages MongoDB connection using `mongoose`
- Routes are mounted under `/api/v1/` by domain
- Controllers implement request logic and database interactions
- Models define schemas and relationships in MongoDB
- Middleware handles authentication, authorization, file uploads, moderation, and error handling

### Frontend architecture

- `api.client.js` provides a shared API request layer with retry and offline queueing
- `offline-db.js` manages IndexedDB stores for queued requests, hazard reports, attendance, checklists, and cached GET responses
- `push-manager.js` handles browser push subscriptions and sends subscription data to the backend
- Static pages in `frontend/features_pages/`, `frontend/admin/`, and `frontend/training_officers/` implement the user interface

## Authentication Flow

1. User submits email and password to `/api/v1/user/login`
2. Backend validates credentials and issues:
   - `accessToken` JWT
   - `refreshToken` JWT
3. Refresh token is persisted on the user document in MongoDB
4. Client stores tokens in cookies or local storage and uses the access token for protected requests
5. Client may refresh the access token via `/api/v1/user/refresh-token`
6. Logout removes the saved refresh token from the user record and clears cookies

## Authorization Model

- `verifyJWT` middleware validates the access token and loads the user from MongoDB
- Authorization is enforced by `authorizeRoles(...)`
- Role names supported include `Admin`, `Manager`, `TrainingOfficer`, `SafetyOfficer`, and `Worker`
- `TrainingOfficer` and `SafetyOfficer` are treated as equivalent in role checks for backward compatibility

## Report Lifecycle

1. Worker creates a hazard report with location, category, severity, and optional description
2. Report is stored in MongoDB with status `open`
3. Hazard media may be uploaded and linked to reports
4. Managers and training officers review reports and update status or delete them as needed
5. Notifications may be created for users related to report events

## Backend Components

### Controllers
- `user.controller.js` — authentication, session lifecycle, user CRUD, password updates
- `hazardReport.controller.js` — create, read, update, delete hazard reports
- `hazardMedia.controller.js` — upload and manage media linked to hazard reports
- `workerVideo.controller.js` — handle uploads, moderation state, approval, and user-facing video retrieval
- `checklistItem.controller.js` — manage checklist items and associated images
- `notification.controller.js` — record and deliver notification state
- `pushSubscription.controller.js` — manage push subscriptions and send notifications

### Routes
Routes are grouped by domain with permissions applied per endpoint.
- `/api/v1/user` — auth and user management
- `/api/v1/roles` — role CRUD
- `/api/v1/tasks` — task definitions
- `/api/v1/checklists` — checklist templates
- `/api/v1/checklist-items` — checklist item CRUD
- `/api/v1/checklist-item-media` — equipment or item-related media
- `/api/v1/hazard-reports` — hazard report lifecycle
- `/api/v1/hazard-media` — hazard report media
- `/api/v1/worker-videos` — worker-generated video uploads and approvals
- `/api/v1/attendance` — check-in/check-out and attendance records
- `/api/v1/push` — push subscription and notification endpoints
- `/api/v1/notifications` — app notification records

### Models
- `User` — authentication, roles, refresh token persistence
- `Role` — role name and description
- `Task` — task template metadata
- `Checklist` — checklist template for a role and task
- `ChecklistItem` — source steps with optional equipment images
- `HazardReport` — incident/finding records with category and severity
- `HazardMedia` — stored media associated with reports
- `WorkerVideo` — uploaded video content with moderation and approval state
- `PushSubscription` — web push subscription metadata
- `Notification` — notifications with read state
- `UserTaskAssignment` — assignment records linking users and tasks
- `Follow` — following relationships between users

### Middlewares
- `auth.middleware.js` — JWT validation, user lookup, refresh token revocation check
- `authorizeRoles.js` — role-based access control
- `upload.middleware.js` — multer disk storage for images, audio, and video
- `contentModeration.middleware.js` — keyword and metadata moderation before accepting uploads
- `videoApproval.middleware.js` — approval gating for worker video access
- `error.middleware.js` — centralized error handling

### Utilities
- `cloudinary.js` — Cloudinary client configuration
- `pushNotification.js` — web push payload assembly and sending
- `contentModeration.js` — keyword filtering, Cloudinary moderation integration, and review flags
- `ApiResponse.js` — consistent API response wrapper
- `ApiError.js` — standardized error class
- `AsyncHandler.js` — async controller error wrapper

## Frontend Structure

### Pages
- `frontend/index.html` — root page and service worker registration
- `frontend/features_pages/` — main user views for reports, attendance, camera, checklists, videos, leaderboard, profile, emergency, and map
- `frontend/admin/` — admin and management pages for users, tasks, roles, videos, attendance, checklists, and hazard maps
- `frontend/training_officers/` — training officer interfaces

### Shared JS
- `frontend/api.client.js` — request wrapper with retry, offline queueing, and cached GET support
- `frontend/offline-db.js` — IndexedDB management for offline storage
- `frontend/push-manager.js` — browser push subscription handling
- `frontend/offline-ui.js` — offline status monitoring UX

### Dashboard Modules
- Hazard reporting and incident tracking
- Offline queue sync and local caching
- Media upload and review workflows
- Notification and subscription UI

## Database Design

### User
Purpose: store account credentials, role, and session refresh token.
Important fields: `userName`, `fullName`, `email`, `password`, `phone`, `language_pref`, `role_id`, `role_name`, `refreshToken`
Relationships: references `Role`, used by `HazardReport`, `WorkerVideo`, `PushSubscription`, `Notification`

### Role
Purpose: define authorization roles.
Important fields: `roleName`, `description`

### Task
Purpose: define assignable tasks.
Important fields: `taskName`, `description`

### Checklist
Purpose: template for role/task checklists.
Important fields: `role_id`, `task_id`, `name`, `created_by`

### ChecklistItem
Purpose: represent steps within a checklist.
Important fields: `checklist_id`, `description`, `equipment_image_url`, `is_mandatory`, `order`, `created_by`

### HazardReport
Purpose: track reported safety issues.
Important fields: `user_id`, `reported_at`, `location`, `category_id`, `severity_id`, `description`, `status`
Relationships: references `User`, `HazardCategory`, `SeverityTag`

### HazardMedia
Purpose: attach media to hazard reports.
Important fields: `report_id`, `media_type`, `language_code`, `url`, `duration`, `file_size`, `mime_type`

### WorkerVideo
Purpose: worker-generated videos with moderation workflow.
Important fields: `uploaded_by`, `title`, `description`, `video_url`, `thumbnail_url`, `approval_status`, `moderation_status`, `moderation_score`, `moderation_flags`, `requires_manual_review`, `category`, `duration`, `views`, `like_count`

### PushSubscription
Purpose: store browser push endpoints.
Important fields: `user_id`, `endpoint`, `keys`, `device_type`, `browser`, `is_active`, `last_used`

### Notification
Purpose: record notifications sent to users.
Important fields: `user_id`, `report_id`, `notification_type`, `sent_at`, `read_at`

### UserTaskAssignment
Purpose: link users to assigned tasks.
Important fields: `user_id`, `task_id`, `assigned_date`, `due_date`

### Follow
Purpose: represent follower/following relationships.
Important fields: `follower_id`, `following_id`

## API Overview

### Authentication & User Management
- `/api/v1/user/register` — register a new user
- `/api/v1/user/login` — login and receive access/refresh tokens
- `/api/v1/user/logout` — clear session and revoke refresh token
- `/api/v1/user/refresh-token` — refresh access token
- `/api/v1/user/current-user` — fetch authenticated user profile
- `/api/v1/user/` — admin/manager user CRUD

### Role, Task, and Checklist Management
- `/api/v1/roles` — role CRUD (admin only)
- `/api/v1/tasks` — task CRUD
- `/api/v1/checklists` — checklist templates
- `/api/v1/checklist-items` — CRUD for checklist steps
- `/api/v1/checklist-item-media` — equipment/image media management

### Hazard and Incident Tracking
- `/api/v1/hazard-reports` — create/read/update/delete hazard reports
- `/api/v1/hazard-media` — upload and browse report media
- `/api/v1/follow-up-actions` — follow-up records for reports
- `/api/v1/escalations` — escalation tracking
- `/api/v1/hazard-audits` — audit records associated with incidents

### Media & Video Workflows
- `/api/v1/worker-videos/upload` — worker video upload with moderation
- `/api/v1/worker-videos/pending` — pending approvals
- `/api/v1/worker-videos/all` — admin video listing
- `/api/v1/worker-videos/approved` — approved video access
- `/api/v1/worker-videos/:id/approve` — approve video
- `/api/v1/worker-videos/:id/reject` — reject video
- `/api/v1/worker-videos/moderation/*` — review metrics and flagged content

### Attendance, Notifications, and Push
- `/api/v1/attendance/check-in` — record check-in
- `/api/v1/attendance/check-out` — record check-out
- `/api/v1/attendance/user/:user_id` — attendance history
- `/api/v1/notifications/user/:user_id` — user notification list
- `/api/v1/push/public-key` — get VAPID public key
- `/api/v1/push/subscribe` — register a push subscription
- `/api/v1/push/unsubscribe` — deregister a subscription
- `/api/v1/push/test` — send a test notification
- `/api/v1/push/send` — admin send notification

## Engineering Decisions

- JWT authentication with refresh token persistence enables session revocation and secure token refresh.
- Role-based authorization middleware separates permission logic from controllers.
- Multer local upload middleware stages files before Cloudinary storage and content moderation.
- Cloudinary is used for media storage and optional AI moderation data capture.
- Client-side IndexedDB offline queue supports write operations when connectivity is unavailable.
- Push notification logic uses `web-push` and VAPID keys to send browser notifications.
- Centralized API response and error classes standardize API contracts.

## Security Considerations

- Access tokens are verified against `ACCESS_TOKEN_SECRET`
- Refresh tokens are verified against `REFRESH_TOKEN_SECRET`
- Logged-out users are invalidated by clearing the stored refresh token
- Protected routes require `verifyJWT`
- Sensitive role checks are handled by `authorizeRoles`
- Unsupported file types are rejected at upload middleware

## Background Jobs

- No explicit cron jobs are implemented in code.
- Push subscription cleanup is handled via an admin endpoint.
- Offline sync is managed by the client when network connectivity returns.

## Notification Flow

- Browser requests VAPID public key from `/api/v1/push/public-key`
- User subscribes via service worker and sends subscription metadata to `/api/v1/push/subscribe`
- Backend stores active subscriptions in MongoDB
- Notifications are sent using `web-push` and the stored subscription endpoints
- Expired subscriptions are deactivated when push delivery returns 410/404

## Analytics Flow

- Worker video model includes view counts, like counts, and engagement score calculation
- Recommendation controller and like functionality are present to support content relevance

## File Upload Flow

- `upload.middleware.js` stores files locally into `src/media/{images,videos,audio}`
- Media endpoints use Cloudinary to upload files after validation
- Local temporary files are deleted after successful Cloudinary transfer
- Content moderation middleware inspects titles/descriptions and may reject flagged uploads

## Folder Structure

- `backend/src/` — backend application code
  - `controllers/` — request logic per domain
  - `routes/` — Express router definitions
  - `models/` — Mongoose schema definitions
  - `middlewares/` — auth, authorization, uploads, moderation, errors
  - `utils/` — helpers for Cloudinary, push notifications, moderation, API responses
  - `db/` — MongoDB connection logic
- `frontend/` — static frontend shell and assets
  - `features_pages/` — user-facing feature pages
  - `admin/` — admin pages
  - `training_officers/` — training officer pages
  - `offline-db.js` — IndexedDB offline data store
  - `api.client.js` — shared request and offline handling
  - `push-manager.js` — browser push manager

## Environment Variables

- `PORT`
- `MONGODB_URI`
- `ACCESS_TOKEN_SECRET`
- `ACCESS_TOKEN_EXPIRY`
- `REFRESH_TOKEN_SECRET`
- `REFRESH_TOKEN_EXPIRY`
- `CORS_ORIGIN`
- `CLOUDINARY_CLOUD_NAME`
- `CLOUDINARY_API_KEY`
- `CLOUDINARY_API_SECRET`
- `VAPID_PUBLIC_KEY`
- `VAPID_PRIVATE_KEY`
- `ENABLE_EXTERNAL_MODERATION`

## Local Development

1. Install backend dependencies: `cd backend && npm install`
2. Create `.env` with required values
3. Run backend: `npm run dev`
4. Open static frontend files in a browser or serve them via a local static server



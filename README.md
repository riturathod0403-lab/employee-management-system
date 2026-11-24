# Headcount - Employee Management System (MERN)

Register, log in, and manage employee records with full create, read, update and delete.

## Features
1. Employee (admin) registration
2. Login with JWT authentication
3. Add employee
4. View employee list (with search)
5. Edit employee details
6. Delete employee (with confirmation)
7. MongoDB integration through Mongoose
8. Employee master data: personal/contact/emergency details, employment history, reporting manager
9. Authenticated Aadhaar, PAN and offer-letter uploads

## Tech stack
MongoDB, Express.js, React (Vite), Node.js, JWT, bcryptjs, Axios, React Router.

## Folder structure
```
employee-management-system/
├── backend/    config, controllers, middleware, models, routes, server.js
└── frontend/   src/{api, components, context, pages}
```

## Run it locally

Requirements: Node.js 18+ and MongoDB (local or Atlas).

### 1. Backend
```bash
cd backend
npm install
# edit .env (a default copy of .env.example is included)
npm run dev
```
The API starts on http://localhost:5000.

### 2. Frontend
```bash
cd frontend
npm install
npm run dev
```
Open http://localhost:5173. Requests to `/api` are proxied to the backend.

## Environment variables (`backend/.env`)
| Variable | Purpose |
|---|---|
| PORT | API port (default 5000) |
| MONGO_URI | MongoDB connection string |
| JWT_SECRET | Secret used to sign tokens - change it |
| JWT_EXPIRES_IN | Token lifetime, e.g. 7d |
| CLIENT_URL | Allowed frontend origin for CORS |

## Employee master data and documents
Employee records now include nested `personalInfo`, `contact`, `emergencyContact`, `employmentHistory`, `documents`, and `reportingManager` fields. Document uploads accept PDF/JPG/PNG files up to 5 MB each. Files are stored in `backend/private_uploads` and are **not publicly served**; download them only through the authenticated endpoint `GET /api/employees/:id/documents/:type`, where type is `aadhaar`, `pan`, or `offerLetter`.

Install the added backend dependency after updating:
```bash
cd backend && npm install
```

For production, use encrypted private object storage, HTTPS, strict role-based access, audit logging, and retention policies for Aadhaar/PAN documents. Do not store Aadhaar numbers in plain text; collect only what is legally necessary.

## API
| Method | Endpoint | Description | Auth |
|---|---|---|---|
| POST | /api/auth/register | Create account, returns token | No |
| POST | /api/auth/login | Log in, returns token | No |
| GET | /api/auth/me | Current user | Yes |
| POST | /api/employees | Add employee | Yes |
| GET | /api/employees?search= | List / search employees | Yes |
| GET | /api/employees/:id | Get one employee | Yes |
| PUT | /api/employees/:id | Update employee | Yes |
| DELETE | /api/employees/:id | Delete employee | Yes |

Send the token as `Authorization: Bearer <token>`.

## Data models
- **User**: name, email (unique), password (bcrypt hash)
- **Employee**: name, email (unique), phone, department, designation, salary, joiningDate, createdBy


## Attendance module

The Attendance page adds employee self check-in/check-out, shift templates, overtime calculation, attendance history, and regularization requests. A login account must use the same email address as an Employee Master work email. Browser location permission is required for check-in and check-out.

To enforce the office geo-fence, set `OFFICE_LATITUDE`, `OFFICE_LONGITUDE`, and `GEOFENCE_RADIUS_METERS` in `backend/.env`. If office coordinates are blank, location is captured but distance enforcement is disabled. Use HTTPS (or localhost during development) for browser geolocation.

**Access model:** accounts default to the `employee` role. Employee accounts can see their own attendance and submit their own regularization requests. Admin accounts can see the attendance register, create shifts, and approve/reject requests. To promote an account, update its MongoDB `users` document and set `role` to `admin`, then sign out and sign back in. Biometric-device integration is not included; it requires the device vendor's SDK/API.

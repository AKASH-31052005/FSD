# Candidate Shortlisting Management System

A full-stack recruitment management application that streamlines the candidate shortlisting process with role-based access control for Recruiters and HR personnel.

![Node.js](https://img.shields.io/badge/Node.js-v18+-green)
![React](https://img.shields.io/badge/React-18.2-blue)
![MongoDB](https://img.shields.io/badge/MongoDB-6.0+-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Tech Stack](#-tech-stack)
- [Features](#-features)
- [User Roles & Permissions](#-user-roles--permissions)
- [Database Schema](#-database-schema)
- [API Documentation](#-api-documentation)
- [Installation & Setup](#-installation--setup)
- [Running the Application](#-running-the-application)
- [Deployment](#-deployment)
- [Status Transition Rules](#-status-transition-rules)

## 🎯 Project Overview

The Candidate Shortlisting Management System provides a platform for:
- **Recruiters** to manage candidate applications, update statuses, and track the recruitment pipeline
- **HR Personnel** to oversee the entire shortlisting process with read-only access

## 🛠 Tech Stack

### Backend
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: MongoDB with Mongoose ODM
- **Authentication**: JWT (JSON Web Tokens)
- **Validation**: express-validator
- **Security**: bcryptjs for password hashing

### Frontend
- **Framework**: React 18 with Vite
- **Routing**: React Router v6
- **HTTP Client**: Axios
- **Notifications**: react-hot-toast
- **Styling**: Vanilla CSS with modern design system

## ✨ Features

### Authentication
- ✅ Secure user registration and login
- ✅ JWT-based authentication
- ✅ Protected routes with role-based access control
- ✅ Automatic token refresh handling

### Candidate Management
- ✅ View all candidates with search and filter
- ✅ Add new candidates (Recruiter only)
- ✅ Update candidate status (Recruiter only)
- ✅ View detailed candidate profiles
- ✅ Status history timeline

### Dashboard
- ✅ Real-time statistics
- ✅ Separate dashboards for Recruiters and HR
- ✅ Pagination support
- ✅ Responsive design

## 👥 User Roles & Permissions

| Role | Permissions |
|------|-------------|
| **RECRUITER** | Register, login, view candidates, add candidates, update candidate status (APPLIED → SHORTLISTED/REJECTED) |
| **HR** | Login, view all candidates and their statuses (read-only access) |

## 📊 Database Schema

### User Schema
```javascript
{
  name: String,           // Required, max 50 chars
  email: String,          // Required, unique, lowercase
  password: String,       // Required, min 6 chars (hashed)
  role: String,           // Enum: 'RECRUITER', 'HR'
  createdAt: Date         // Auto-generated
}
```

### Candidate Schema
```javascript
{
  name: String,           // Required, max 100 chars
  email: String,          // Required, unique
  phone: String,          // Required
  position: String,       // Required
  experience: Number,     // Required, min 0
  skills: [String],       // Required array
  resumeUrl: String,      // Optional
  status: String,         // Enum: 'APPLIED', 'SHORTLISTED', 'REJECTED'
  statusHistory: [{       // Status change log
    status: String,
    changedBy: ObjectId,  // Reference to User
    changedAt: Date,
    notes: String
  }],
  appliedAt: Date,        // Auto-generated
  updatedAt: Date         // Auto-updated
}
```

## 📚 API Documentation

### Base URL
```
http://localhost:5000/api
```

### Authentication Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/auth/register` | Register new user | No |
| POST | `/auth/login` | Login user | No |
| GET | `/auth/me` | Get current user | Yes |
| POST | `/auth/logout` | Logout user | Yes |

#### Register User
```bash
POST /api/auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "role": "RECRUITER"  // Optional, defaults to RECRUITER
}
```

#### Login User
```bash
POST /api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

### Candidate Endpoints

| Method | Endpoint | Description | Auth Required | Roles |
|--------|----------|-------------|---------------|-------|
| GET | `/candidates` | Get all candidates | Yes | RECRUITER, HR |
| GET | `/candidates/stats` | Get statistics | Yes | RECRUITER, HR |
| GET | `/candidates/:id` | Get single candidate | Yes | RECRUITER, HR |
| POST | `/candidates` | Create candidate | Yes | RECRUITER |
| PUT | `/candidates/:id` | Update candidate | Yes | RECRUITER |
| PUT | `/candidates/:id/status` | Update status | Yes | RECRUITER |
| DELETE | `/candidates/:id` | Delete candidate | Yes | RECRUITER |

#### Get Candidates (with filters)
```bash
GET /api/candidates?page=1&limit=10&status=APPLIED&search=john
Authorization: Bearer <token>
```

#### Create Candidate
```bash
POST /api/candidates
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Jane Smith",
  "email": "jane@example.com",
  "phone": "+1-555-0123",
  "position": "Frontend Developer",
  "experience": 3,
  "skills": ["React", "JavaScript", "CSS"],
  "resumeUrl": "https://example.com/resume.pdf"
}
```

#### Update Candidate Status
```bash
PUT /api/candidates/:id/status
Authorization: Bearer <token>
Content-Type: application/json

{
  "status": "SHORTLISTED",  // or "REJECTED"
  "notes": "Great interview performance"
}
```

## 🚀 Installation & Setup

### Prerequisites
- Node.js v18 or higher
- MongoDB (local or MongoDB Atlas)
- npm or yarn

### 1. Clone the Repository
```bash
git clone <repository-url>
cd intern
```

### 2. Backend Setup
```bash
cd backend

# Install dependencies
npm install

# Create .env file (copy from .env.example)
cp .env.example .env

# Edit .env with your configuration
# MONGODB_URI=mongodb://localhost:27017/candidate-management
# JWT_SECRET=your_secret_key
# PORT=5000
```

### 3. Frontend Setup
```bash
cd frontend

# Install dependencies
npm install
```

### 4. Seed the Database
```bash
cd backend
npm run seed
```

This creates:
- **HR Account**: hr@company.com / password123
- **Recruiter Account**: recruiter@company.com / password123
- **8 Sample Candidates** with various statuses

## 🏃 Running the Application

### Development Mode

**Terminal 1 - Backend:**
```bash
cd backend
npm run dev
```
Server runs at: http://localhost:5000

**Terminal 2 - Frontend:**
```bash
cd frontend
npm run dev
```
Application runs at: http://localhost:5173

### Production Build

**Backend:**
```bash
cd backend
npm start
```

**Frontend:**
```bash
cd frontend
npm run build
npm run preview
```

## 🌐 Deployment

### Backend Deployment (e.g., Railway, Render, Heroku)

1. Set environment variables:
   - `MONGODB_URI`: Your MongoDB Atlas connection string
   - `JWT_SECRET`: A secure secret key
   - `NODE_ENV`: production
   - `PORT`: (provided by platform)

2. Deploy the `backend` folder

### Frontend Deployment (e.g., Vercel, Netlify)

1. Set environment variable:
   - `VITE_API_URL`: Your deployed backend URL

2. Build command: `npm run build`
3. Output directory: `dist`

## 🔄 Status Transition Rules

The system enforces strict status transition rules:

```
APPLIED ──────┬──────► SHORTLISTED (Final)
              │
              └──────► REJECTED (Final)
```

### Rules:
1. ✅ APPLIED → SHORTLISTED (Allowed)
2. ✅ APPLIED → REJECTED (Allowed)
3. ❌ SHORTLISTED → Any other status (Not allowed)
4. ❌ REJECTED → Any other status (Not allowed)
5. ❌ Skipping steps is not permitted

## 📁 Project Structure

```
intern/
├── backend/
│   ├── config/
│   │   └── db.js              # MongoDB connection
│   ├── controllers/
│   │   ├── authController.js  # Authentication logic
│   │   └── candidateController.js
│   ├── middleware/
│   │   ├── auth.js            # JWT & role verification
│   │   └── errorHandler.js    # Global error handling
│   ├── models/
│   │   ├── User.js            # User model
│   │   └── Candidate.js       # Candidate model
│   ├── routes/
│   │   ├── authRoutes.js      # Auth routes
│   │   └── candidateRoutes.js # Candidate routes
│   ├── .env                   # Environment variables
│   ├── package.json
│   ├── seeder.js              # Database seeder
│   └── server.js              # Express app entry
│
└── frontend/
    ├── public/
    ├── src/
    │   ├── components/
    │   │   ├── AddCandidateModal.jsx
    │   │   ├── CandidateTable.jsx
    │   │   ├── Header.jsx
    │   │   ├── ProtectedRoute.jsx
    │   │   ├── StatsCard.jsx
    │   │   └── StatusBadge.jsx
    │   ├── context/
    │   │   └── AuthContext.jsx
    │   ├── pages/
    │   │   ├── CandidateDetails.jsx
    │   │   ├── HRDashboard.jsx
    │   │   ├── Login.jsx
    │   │   ├── RecruiterDashboard.jsx
    │   │   └── Register.jsx
    │   ├── services/
    │   │   ├── api.js
    │   │   └── candidateService.js
    │   ├── styles/
    │   │   └── index.css
    │   ├── App.jsx
    │   └── main.jsx
    ├── index.html
    ├── package.json
    └── vite.config.js
```

## 🔐 Security Features

- Password hashing with bcryptjs
- JWT token authentication
- Role-based access control (RBAC)
- Protected API routes
- CORS configuration
- Input validation with express-validator
- Environment variable configuration

## 📝 License

This project is licensed under the MIT License.

---

**Built with ❤️ for streamlined recruitment workflows**

# 🏛️ CivicResolve — AI-Powered Civic Issue Reporting & Resolution Platform

**CivicResolve** is a full-stack, smart city civic engagement platform that bridges citizens and municipal administration. Powered by real-time Natural Language Processing (NLP), geospatial analysis, and a secure role-based authentication system, it automatically categorizes citizen reports, detects emergency urgency levels, prevents duplicate complaints, and coordinates rapid municipal resolution.

---

## 🔐 Authentication & Security System (New)

CivicResolve implements an enterprise-grade authentication and role-based access control (RBAC) architecture:

### 1. 👥 Three-Tier User Roles
- **🟢 Citizen**:
  - Register and sign in via dedicated Citizen Sign Up and Login pages.
  - Report civic issues with auto-filled reporter details.
  - Upvote existing community reports and view citywide repair statuses.
  - *Strict Non-Escalation Guarantee*: Public registration is strictly restricted to the `citizen` role. Users cannot self-register as Staff or Admin.
- **🟠 City Staff (Field Officers & Supervisors)**:
  - Authorized access to the protected **City Operations Command Center**.
  - Review incoming civic triage queues and dispatch field maintenance crews.
  - Inspect issue details, update resolution statuses, and post official work order logs.
  - Merge duplicate complaint clusters into unified work tickets.
- **🔴 Municipal Administrator**:
  - Full supervisory authority over all municipal departments.
  - System-level operations including platform analytics and demo database restoration.

### 2. 🛡️ Security Architecture
- **Password Hashing**: Passwords are cryptographically hashed using **bcrypt** with 10 salt rounds before storage.
- **JWT in Secure HTTP-Only Cookies**: Authentication sessions are signed JSON Web Tokens (JWT) transmitted inside secure `HttpOnly`, `SameSite=Lax` cookies, preventing XSS token theft.
- **Backend Role Guard Middleware**:
  - `authenticate`: Validates HTTP-only cookie session and loads active user profile.
  - `authorizeRoles('staff', 'admin')`: Enforces strict role authorization on municipal operations, returning `403 Forbidden` for unauthorized attempts.
  - `optionalAuth`: Gracefully enhances public endpoints when authenticated without blocking guest visitors.
- **Protected Dashboard Gate**: Unauthenticated users or users with the `citizen` role attempting to access City Staff & Admin features are blocked by an interactive security gateway.

### 3. 🔑 Pre-Configured Demo Accounts (Ready to Test)
All accounts are stored in `backend/data/users.json` with real bcrypt hashes:

| Role | Account Name | Email | Password | Access Level |
|---|---|---|---|---|
| **🟢 Citizen** | Elena Rostova | `citizen@example.com` | `Citizen@1234` | Citizen reporting, issue tracking & upvoting |
| **🟠 City Staff** | Sarah Jenkins | `staff@civicresolve.gov` | `Staff@1234` | Field crew dispatch & duplicate cluster merging |
| **🔴 Admin** | Marcus Vance | `admin@civicresolve.gov` | `Admin@1234` | Supervisory administration & database controls |

*(The Sign In page features 1-click demo chips to instantly fill credentials for easy testing).*

---

## 🌟 Key Civic Platform Features

### 1. 📢 Citizen Reporting Portal
- **Intuitive Multi-Step Wizard**: Report issues with Title, Description, Location, Category, and Photo Evidence.
- **One-Click Demo Templates**: Test real-world scenarios instantly (Potholes, Burst Water Mains, Sparking Streetlights, Overflowing Trash).
- **Interactive Location Picker**: Click from realistic city presets (e.g. Elm St Library, Congress Ave, Midtown Elementary) or enter custom addresses.
- **Photo Upload & Samples**: Upload custom images or select from high-resolution civic photo presets.

### 2. ⚡ Real-Time AI Triage Engine
- **Autonomous Categorization**: Classifies reports across 7 municipal departments (Roads, Sanitation, Electrical, Water, Parks, Traffic, Public Health).
- **Urgency & Hazard Detection**: Flags **Critical**, **High**, **Medium**, or **Low** urgency with human-readable rationale and safety hazard warnings.
- **Department Routing**: Recommends the specific municipal agency and generates field inspection checklists.
- **Dynamic SLA Targets**: Computes realistic turnaround hours (e.g., 6 hours for critical electrical hazards, 48 hours for road repairs).
- *Optional Gemini API Integration*: Plug in `GEMINI_API_KEY` for live Google Gemini 1.5 Flash analysis, or use the built-in, zero-latency local NLP engine.

### 3. 🔍 UNIQUE FEATURE: Duplicate & Nearby Complaints Detection
- **Geo-Proximity (Haversine) + Semantic Keyword Matching**:
  - Automatically calculates distance in meters from existing complaints.
  - Computes text token similarity across titles, descriptions, and categories.
  - Produces an instant similarity score (e.g., **94% match**, 29m away).
- **Citizen "Upvote Instead of Duplicate" Flow**:
  - When typing a report, citizens are gently notified if a matching complaint already exists nearby.
  - Citizens can click **"Upvote / Boost Existing Report"**, increasing the ticket's civic priority score without cluttering municipal dispatch queues with duplicate work orders.
- **Admin Duplicate Cluster Merging**:
  - City staff can view auto-detected geographic clusters and merge multiple duplicate complaints into a master ticket with a single click.

### 4. 📊 Public Civic Dashboard
- **Status Tracking**: Filter and view issues categorized into **Reported**, **In Progress**, and **Resolved**.
- **Interactive City Map**: Built with Leaflet & OpenStreetMap, displaying custom colored status markers, popups, and details across the city grid.
- **View Modes**: Switch between **Grid Cards**, **Interactive Map**, and **Compact Table**.
- **Citizen Upvotes**: Community members can support and prioritize issues affecting their neighborhood.

### 5. 🛡️ City Staff & Admin Command Center (Protected)
- **Triage Queue**: Filter issues by Critical emergencies, unassigned field tickets, or in-progress crews.
- **Field Dispatch**: Assign field officers, update statuses, and log official inspection notes.
- **AI Auto-Response Generator**: Automatically draft polite, official citizen notifications based on current issue status.
- **CSV Data Export**: Export all civic complaints with full metadata for municipal reporting.
- **Demo Reset Button**: 1-click restore to preloaded realistic dataset (Admin role only).

---

## 🏗️ Architecture & Tech Stack

```
CivicResolve/
├── backend/
│   ├── data/
│   │   ├── users.json               # Hashed user database (Citizen, Staff, Admin)
│   │   ├── seedData.js              # 12 pre-loaded realistic civic issues
│   │   └── issues.json              # Persistent issues database
│   ├── middleware/
│   │   └── auth.js                  # JWT validation & role-based authorization guard
│   ├── routes/
│   │   ├── auth.js                  # Register, Login, Logout, Session check
│   │   ├── issues.js                # Protected issue management & public browsing
│   │   ├── ai.js                    # Real-time AI triage & response drafting
│   │   └── stats.js                 # Municipal analytics & admin reset
│   ├── services/
│   │   ├── userStorage.js           # Bcrypt password hashing & user persistence
│   │   ├── aiService.js             # Autonomous NLP classification & urgency engine
│   │   ├── duplicateDetector.js     # Haversine spatial & text similarity matcher
│   │   └── storage.js               # Issue persistence & duplicate merger
│   ├── test-auth-flow.js            # Automated 6-step auth test suite
│   ├── server.js                    # Express server with cookie & CORS support
│   └── package.json
│
├── frontend/
│   ├── src/
│   │   ├── context/
│   │   │   └── AuthContext.jsx      # Global auth state & session provider
│   │   ├── pages/
│   │   │   ├── LoginPage.jsx        # Dedicated Login page with demo selector
│   │   │   └── SignUpPage.jsx       # Dedicated Citizen Sign Up with strength meter
│   │   ├── components/
│   │   │   ├── Navbar.jsx           # Profile status, role pills & sign in/out
│   │   │   ├── ProtectedAdminGate.jsx # Role-based security gate for admin view
│   │   │   ├── StatsBanner.jsx      # KPI metrics & duplicate prevention counter
│   │   │   ├── FilterBar.jsx        # Search, category, urgency, & view toggles
│   │   │   ├── IssueCard.jsx        # Cards with status badges & upvote controls
│   │   │   ├── MapView.jsx          # Interactive Leaflet map with custom pins
│   │   │   ├── DuplicateAlertCard.jsx # Similarity meter & upvote prompt
│   │   │   ├── ReportIssueModal.jsx # Wizard with live AI triage assistant
│   │   │   ├── IssueDetailsModal.jsx# Details, before/after photos, timeline
│   │   │   └── AdminTriageView.jsx  # Staff queue, cluster merge, CSV export
│   │   ├── services/api.js          # Fetch client with credentials: 'include'
│   │   ├── App.jsx                  # Main view coordinator
│   │   └── main.jsx
│   ├── index.html
│   ├── vite.config.js               # Vite config with backend proxy
│   └── package.json
│
├── start-dev.js                     # Unified concurrent runner
├── package.json                     # Root scripts
└── README.md
```

---

## 🚀 Quick Setup & Run Instructions

### 1. Launch the Complete Platform (Backend + Frontend)
From the root directory:

```bash
npm run dev
```

This concurrently launches:
- **Backend API**: `http://localhost:5000`
- **Frontend App**: `http://localhost:3000`

### 2. Run Test Suite
To run the automated 6-step authentication and role-authorization test suite:

```bash
npm --prefix backend run test:auth
```

---

## 🧪 How to Test the Complete Authentication Flow

1. Open **`http://localhost:3000`**.
2. **Test Guest Protection**:
   - In the top navbar, click **`City Staff & Admin`**.
   - Notice you are blocked by the **Protected Dashboard Gate**: *"City Staff & Admin Authentication Required"*.
3. **Test Dedicated Login Page**:
   - Click **`Sign In`** in the navbar or on the security gate.
   - Click the 1-click demo chip **`City Staff Demo`** (`staff@civicresolve.gov` / `Staff@1234`).
   - Click **`Sign In`**.
   - You are securely logged in: your name (*Sarah Jenkins*) and **`STAFF`** badge appear in the navbar.
   - The **City Operations Command Center** opens immediately, allowing field dispatch and duplicate cluster merging!
4. **Test Role Restriction (Citizen Account)**:
   - Click **Logout** (door icon in navbar).
   - Sign in using **`Citizen Demo`** (`citizen@example.com` / `Citizen@1234`).
   - Try clicking **`City Staff & Admin`** tab.
   - The security gate blocks access with a clear role authorization alert:
     *“Access Restricted: You are currently signed in with a Citizen role. The City Operations & Triage Center is restricted to City Staff and Municipal Administrators.”*
5. **Test Citizen Registration Page**:
   - Log out, then click **`Sign Up`** in the navbar.
   - Enter your name, email, and password.
   - Notice the live password strength meter and strict citizen role guarantee.
   - Submit registration: your account is created, hashed with bcrypt, assigned a JWT session, and you are logged in!
6. **Test Issue Reporting**:
   - Click **`+ Report Issue`**: Notice your citizen name and email are automatically pre-populated in the report form.

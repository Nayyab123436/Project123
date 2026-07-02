# CMGHS Backend — City Model Girls High School, Malakwal

Node.js + Express + SQLite backend for the school website.

---

## ⚡ Quick Start

```bash
# 1. Install dependencies
npm install

# 2. Create your .env file
cp .env.example .env
# Edit .env and set your JWT_SECRET and admin credentials

# 3. Seed the database with sample data
npm run seed

# 4. Start the development server
npm run dev

# API is now running at http://localhost:5000
```

---

## 📁 Project Structure

```text
school-backend/
├── server.js              # Entry point
├── .env.example           # Environment variables template
├── db/
│   ├── database.js        # SQLite setup & schema
│   ├── seed.js            # Sample data seeder
│   └── school.db          # Auto-created SQLite database
├── middleware/
│   └── auth.js            # JWT authentication middleware
├── routes/
│   ├── auth.js            # Login / token management
│   ├── notices.js         # School notices & announcements
│   ├── events.js          # Calendar events
│   ├── admissions.js      # Student admission applications
│   ├── staff.js           # Teachers & staff
│   ├── gallery.js         # Photo gallery (with file upload)
│   ├── results.js          # Exam results
│   └── contact.js         # Contact / inquiry messages
└── uploads/
    └── gallery/           # Uploaded gallery images
```

---

## 🔑 Authentication

Protected routes require a **Bearer token** in the Authorization header:

Authorization: Bearer <your_jwt_token>

Get a token by calling `POST /api/auth/login`.

---

## 📡 API Reference

### Health Check

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | `/api/health` | ❌ | Check server status |

---

### Auth

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| POST | `/api/auth/login` | ❌ | Admin login → returns JWT token |
| GET | `/api/auth/me` | ✅ | Get current admin info |
| POST | `/api/auth/change-password` | ✅ | Change admin password |

**Login example:**

```json
POST /api/auth/login
{ "email": "admin@cmghs.edu.pk", "password": "Admin@1234" }
```

---

### Notices

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | `/api/notices` | ❌ | List all notices |
| GET | `/api/notices/:id` | ❌ | Get single notice |
| POST | `/api/notices` | ✅ | Create notice |
| PUT | `/api/notices/:id` | ✅ | Update notice |
| DELETE | `/api/notices/:id` | ✅ | Delete notice |

**Query params:** `category`, `pinned=true`, `limit`, `page`

**Body fields:** `title`*, `description`, `category` (General/Exam/Holiday/Event), `is_pinned`, `posted_date`

---

### Events

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | `/api/events` | ❌ | List events |
| GET | `/api/events/:id` | ❌ | Get single event |
| POST | `/api/events` | ✅ | Create event |
| PUT | `/api/events/:id` | ✅ | Update event |
| DELETE | `/api/events/:id` | ✅ | Delete event |

**Query params:** `upcoming=true` (filters future events), `limit`, `page`

**Body fields:** `title`*, `event_date`* (YYYY-MM-DD), `description`, `event_time` (HH:MM), `location`

---

### Admissions

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| POST | `/api/admissions` | ❌ | Submit application (public) |
| GET | `/api/admissions` | ✅ | List all applications |
| GET | `/api/admissions/:id` | ✅ | View single application |
| PATCH | `/api/admissions/:id/status` | ✅ | Approve / Reject |
| DELETE | `/api/admissions/:id` | ✅ | Delete application |

**Public body fields:** `student_name`*, `father_name`*, `date_of_birth`*, `class_applying`*, `parent_phone`*, `previous_school`, `marks_obtained`, `total_marks`, `parent_email`, `address`

**Status update:** `{ "status": "Approved", "remarks": "Welcome!" }` — status must be Pending / Approved / Rejected

---

### Staff

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | `/api/staff` | ❌ | List active staff |
| GET | `/api/staff/:id` | ❌ | Get single staff member |
| POST | `/api/staff` | ✅ | Add staff member |
| PUT | `/api/staff/:id` | ✅ | Update staff member |
| DELETE | `/api/staff/:id` | ✅ | Remove staff member |

**Query params:** `designation`, `active=true/false`

**Body fields:** `name`*, `designation`*, `subject`, `qualification`, `phone`, `email`, `join_date`, `photo_url`, `is_active`

---

### Gallery

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | `/api/gallery` | ❌ | List all images |
| POST | `/api/gallery` | ✅ | Upload image (multipart/form-data) |
| DELETE | `/api/gallery/:id` | ✅ | Delete image + file |

**Upload:** Send as `multipart/form-data` with field `image` (jpg/png/webp, max 5MB) + `title`*, `description`, `category`

**Query params:** `category`, `limit`, `page`

---

### Results

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| GET | `/api/results` | ❌ | Search results |
| POST | `/api/results` | ✅ | Add result |
| PUT | `/api/results/:id` | ✅ | Update result |
| DELETE | `/api/results/:id` | ✅ | Delete result |

**Query params:** `class`, `exam_year`, `exam_type`, `roll_number`

**Body fields:** `student_name`*, `class`* (e.g. "Class 10"), `marks_obtained`*, `total_marks`*, `exam_year`*, `roll_number`, `subject`, `grade`, `exam_type` (Annual/Half-Yearly/Test)

---

### Contact Messages

| Method | Endpoint | Auth | Description |
| --- | --- | --- | --- |
| POST | `/api/contact` | ❌ | Send a message (public) |
| GET | `/api/contact` | ✅ | View all messages |
| PATCH | `/api/contact/:id/read` | ✅ | Mark as read |
| DELETE | `/api/contact/:id` | ✅ | Delete message |

**Query params:** `unread=true`

---

## 🔗 Connecting Your Frontend

In your HTML file, fetch data like this:

```javascript
// Get notices
const res = await fetch('http://localhost:5000/api/notices?limit=5');
const { data } = await res.json();

// Submit admission form
await fetch('http://localhost:5000/api/admissions', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ student_name, father_name, ... })
});

// Admin login
const res = await fetch('http://localhost:5000/api/auth/login', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ email, password })
});
const { token } = await res.json();
localStorage.setItem('token', token);

// Create notice (admin)
await fetch('http://localhost:5000/api/notices', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${localStorage.getItem('token')}`
  },
  body: JSON.stringify({ title: 'New Notice', category: 'General' })
});
```

---

## 📦 Dependencies

| Package | Purpose |
| --------- | --------- |
| `express` | Web framework |
| `better-sqlite3` | Fast SQLite database (no server needed) |
| `jsonwebtoken` | JWT auth tokens |
| `bcryptjs` | Password hashing |
| `cors` | Cross-origin requests |
| `multer` | Image file uploads |
| `express-rate-limit` | Prevent spam/abuse |
| `dotenv` | Environment variables |

---

*fields marked `*` are required
# Project123

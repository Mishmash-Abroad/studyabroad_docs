# OpenAPI 3.0 Documentation for Study Abroad Program API

This document provides the OpenAPI 3.0 specification for the Study Abroad Program API, detailing the available endpoints, request/response formats, and authentication requirements.

## Overview

- **Base URL:** `/api/`
- **Authentication:** Token-based (`Token <your_token>`)
- **Content Type:** `application/json`

## Tags
- **Programs**: Manage study abroad programs.
- **Applications**: Submit and track applications.
- **Users**: User authentication and profile management.
- **Questions**: Application questions.
- **Responses**: Responses to application questions.
- **Announcements**: Program announcements.

## Security
- Most endpoints require authentication.
- Admin-only operations enforce `is_admin = True`.

---

## Paths

### 1. Programs

**Tag:** `Programs`

#### List Programs
```http
GET /api/programs/
```
- **Auth:** Optional
- **Query Params:** `search=<string>`
- **Response:**
```json
[
  {
    "id": 1,
    "title": "Engineering in Germany",
    "faculty_leads": "Dr. Smith",
    "application_deadline": "2025-03-15"
  }
]
```

#### Create Program (Admin)
```http
POST /api/programs/
```
- **Auth:** Admin
- **Request:**
```json
{
  "title": "Engineering in Germany",
  "year_semester": "Fall 2025",
  "faculty_leads": "Dr. Smith",
  "application_open_date": "2025-01-01",
  "application_deadline": "2025-03-15",
  "start_date": "2025-05-01",
  "end_date": "2025-07-31",
  "description": "An amazing program in Germany."
}
```
- **Response:**
```json
{
  "id": 1,
  "title": "Engineering in Germany"
}
```

#### Retrieve Program
```http
GET /api/programs/{id}/
```
- **Auth:** Optional
- **Response:** Program details.

#### Update Program (Admin)
```http
PUT /api/programs/{id}/
```
- **Auth:** Admin

#### Delete Program (Admin)
```http
DELETE /api/programs/{id}/
```
- **Auth:** Admin

#### Application Status
```http
GET /api/programs/{id}/application_status/
```
- **Auth:** User
- **Response:**
```json
{
  "status": "Applied",
  "application_id": 12
}
```

#### Applicant Counts (Admin)
```http
GET /api/programs/{id}/applicant_counts/
```
- **Auth:** Admin

#### Program Questions
```http
GET /api/programs/{id}/questions/
```
- **Auth:** Public

---

### 2. Applications

**Tag:** `Applications`

#### List Applications
```http
GET /api/applications/
```
- **Auth:** User/Admin
- **Query Params:** `student=<id>`, `program=<id>`

#### Create Application
```http
POST /api/applications/
```
- **Auth:** User
- **Request:**
```json
{
  "program": 1,
  "date_of_birth": "2006-05-20",
  "gpa": 3.5,
  "major": "Computer Science"
}
```

#### Update Application
```http
PATCH /api/applications/{id}/
```
- **Auth:** User/Admin

#### Delete Application (Admin)
```http
DELETE /api/applications/{id}/
```

---

### 3. Users

**Tag:** `Users`

#### Sign Up
```http
POST /api/users/signup/
```
- **Auth:** None
- **Request:**
```json
{
  "username": "newuser",
  "password": "securepassword",
  "display_name": "John Doe",
  "email": "john@example.com"
}
```

#### Login
```http
POST /api/users/login/
```
- **Auth:** None
- **Request:**
```json
{
  "username": "user123",
  "password": "securepassword"
}
```

#### Logout
```http
POST /api/users/logout/
```
- **Auth:** Token

#### Current User Info
```http
GET /api/users/current_user/
```
- **Auth:** Token

#### Change Password
```http
PATCH /api/users/change_password/
```
- **Auth:** Token
- **Request:**
```json
{
  "password": "newpassword",
  "confirm_password": "newpassword"
}
```

---

### 4. Questions

**Tag:** `Questions`

#### List Questions
```http
GET /api/questions/
```
- **Auth:** Public
- **Query Params:** `program=<id>`

#### Retrieve Question
```http
GET /api/questions/{id}/
```
- **Auth:** Public

---

### 5. Responses

**Tag:** `Responses`

#### List Responses
```http
GET /api/responses/
```
- **Auth:** User/Admin
- **Query Params:** `application=<id>`, `question=<id>`

#### Create Response
```http
POST /api/responses/
```
- **Auth:** User
- **Request:**
```json
{
  "application": 5,
  "question": 3,
  "response": "This is my answer."
}
```

#### Update Response
```http
PATCH /api/responses/{id}/
```
- **Auth:** User

---

### 6. Announcements

**Tag:** `Announcements`

#### List Announcements
```http
GET /api/announcements/
```
- **Auth:** Public

#### Create Announcement (Admin)
```http
POST /api/announcements/
```
- **Auth:** Admin

#### Update Announcement (Admin)
```http
PATCH /api/announcements/{id}/
```
- **Auth:** Admin

#### Delete Announcement (Admin)
```http
DELETE /api/announcements/{id}/
```
- **Auth:** Admin

---

## Security Definitions

```yaml
securitySchemes:
  BearerAuth:
    type: http
    scheme: bearer
    bearerFormat: JWT
```

## Example OpenAPI Components

```yaml
components:
  schemas:
    Program:
      type: object
      properties:
        id:
          type: integer
        title:
          type: string
        description:
          type: string
    Application:
      type: object
      properties:
        id:
          type: integer
        student_id:
          type: integer
        program_id:
          type: integer
        status:
          type: string
    User:
      type: object
      properties:
        id:
          type: integer
        username:
          type: string
        email:
          type: string
    Announcement:
      type: object
      properties:
        id:
          type: integer
        content:
          type: string
        is_active:
          type: boolean
```

## Authentication Example

Include your token in the request header:
```http
Authorization: Token <your_token>
```

---

### End of Document

This document describes the available endpoints following OpenAPI 3.0 standards for the Study Abroad Program API.


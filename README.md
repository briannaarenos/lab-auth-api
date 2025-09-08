# Lab-Authentication API
- A standalone authentication API built with **Express.js** and **MySQL**.  
- Implements secure authentication using **bcrypt password hashing** and **JSON Web Tokens (JWT)**.  
- Features include **Signup**, **Login**, **Logout (token revocation)**, and a protected **/profile** endpoint.  

## Project Overview
This project demonstrates secure authentication in Node.js with MySQL as the database.  
- Passwords are stored as **bcrypt hashes**, never in plain text.  
- JWT is used for authentication, with token revocation supported via a **revoked_tokens** table.  
- Includes middleware for verifying and protecting routes.  
- Fully tested with Postman.  

## Setup Instructions

### 1. Clone Repository
- git clone https://github.com/<your-username>/lab-auth-api.git
- cd lab-auth-api
### 2. Install Dependencies
npm install
### 3. Configure Environment
Create a .env file in the root directory (use .env.example as a template):

DB_HOST=localhost
DB_USER=root
DB_PASS=
DB_NAME=lab_auth
DB_PORT=3306
SERVER_PORT=3000

JWT_SECRET=replace_with_a_long_random_string
JWT_EXPIRES=1h

### 4. Database Setup
Create a new MySQL database and required tables:

CREATE DATABASE lab_auth;

USE lab_auth;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  full_name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'student',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE revoked_tokens (
  id INT AUTO_INCREMENT PRIMARY KEY,
  jti VARCHAR(255) UNIQUE NOT NULL,
  expires_at DATETIME NOT NULL
);

### 5. Run the Server
- npm run dev
- If successful, you should see:
🚀 Server running at http://localhost:3000
✅ MySQL connected

### API Endpoints
Health Check
- GET /api/health → Verify API and DB connection.

Authentication
- POST /api/auth/signup → Register a new user
  Body: { "email": "user1@example.com", "password": "Pass@1234", "full_name": "User One", "role": "student" }
  
- POST /api/auth/login → Login and receive JWT
  Body: { "email": "user1@example.com", "password": "Pass@1234" }

- POST /api/auth/logout → Logout (revoke token)
  Headers: Authorization: Bearer <token>

Protected Route
- GET /api/profile → Get user profile (requires JWT)
  Headers: Authorization: Bearer <token>

Testing
- Use Postman with a saved environment variable:
- baseUrl = http://localhost:3000/api
- token = <set after login>

### Typical flow to test
- Signup → POST /auth/signup
- Login → POST /auth/login → Save JWT as token
- Access profile → GET /profile with Authorization: Bearer {{token}}
- Logout → POST /auth/logout
- Retry profile → Expect 401 Unauthorized (token revoked)

### Notes
- Never commit your .env file. Always use .env.example for sharing.
- Ensure node_modules/ and .env are listed in .gitignore.

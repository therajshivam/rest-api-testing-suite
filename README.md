# REST API Testing Suite

![CI](https://github.com/yourusername/rest-api-testing-suite/actions/workflows/ci.yml/badge.svg)
![Postman](https://img.shields.io/badge/tested%20with-postman-FF6C37.svg)
![Newman](https://img.shields.io/badge/cli-newman-darkgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

> A dedicated REST API testing suite built with Postman and Newman,
> covering full CRUD operations, negative testing, request chaining,
> and automated CI/CD execution — demonstrating industry-ready
> API testing skills.

---

## Project Walkthrough
[Click here to watch the full project walkthrough](#) ← Loom link

---

## Overview

This project demonstrates a complete API testing workflow — from writing
test cases in Postman to running them automatically via Newman in a
GitHub Actions CI/CD pipeline. The API under test is ReqRes.in, a
real hosted REST API that simulates real-world user management endpoints.

**What is covered:**
- 30+ API test cases across all HTTP methods
- Positive and negative test scenarios
- Status code validation
- Response body and schema validation
- Request chaining — use response of one request as input to next
- Pre-request scripts for dynamic data generation
- Environment variables for base URL and auth tokens
- Newman CLI execution with HTML report generation
- Automated CI/CD pipeline via GitHub Actions

---

## Application Under Test

| Detail | Value |
|--------|-------|
| API | ReqRes.in |
| Base URL | https://reqres.in/api |
| Type | REST API — User Management |
| Auth | Bearer Token |
| Format | JSON |

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Postman | API test design and execution |
| Newman | CLI runner for Postman collections |
| newman-reporter-htmlextra | HTML report generation |
| GitHub Actions | CI/CD pipeline automation |
| JavaScript | Pre-request scripts and test assertions |

---

## API Endpoints Tested

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /users | Get list of users |
| GET | /users/:id | Get single user |
| GET | /users/:id | Get non-existing user |
| POST | /users | Create new user |
| PUT | /users/:id | Update user completely |
| PATCH | /users/:id | Update user partially |
| DELETE | /users/:id | Delete user |
| POST | /register | Register with valid data |
| POST | /register | Register with missing password |
| POST | /login | Login with valid credentials |
| POST | /login | Login with missing password |

---

## Test Coverage

| Category | Test Cases |
|----------|-----------|
| GET requests | 6 |
| POST requests | 6 |
| PUT requests | 4 |
| PATCH requests | 4 |
| DELETE requests | 3 |
| Auth — Register | 4 |
| Auth — Login | 4 |
| Negative cases | 8 |
| **Total** | **39** |

---

## Test Case Types

**Positive Testing**
- Valid GET returns 200 with correct user data
- Valid POST creates user and returns 201
- Valid PUT updates all fields and returns 200
- Valid PATCH updates single field and returns 200
- Valid DELETE returns 204

**Negative Testing**
- GET with invalid user ID returns 404
- POST with missing required fields returns 400
- POST /register without password returns 400
- POST /login without password returns 400
- PUT with invalid ID returns 404

**Response Validation**
- Status code assertion on every request
- Response body field existence check
- Response body data type check
- Response time under 2000ms assertion

**Request Chaining**
- Create user via POST → extract ID from response
- Use extracted ID to GET the same user
- Use extracted ID to PUT update the user
- Use extracted ID to DELETE the user
- Assert each step in the chain

---

## Postman Collection Structure
```
REST API Testing Suite/
│
├── Auth/
│   ├── Register - Valid
│   ├── Register - Missing Password
│   ├── Login - Valid
│   └── Login - Missing Password
│
├── Users - GET/
│   ├── Get All Users - Page 1
│   ├── Get All Users - Page 2
│   ├── Get Single User - Valid ID
│   ├── Get Single User - Invalid ID
│   └── Get Delayed Response
│
├── Users - POST/
│   ├── Create User - Valid Data
│   ├── Create User - Missing Name
│   ├── Create User - Missing Job
│   └── Create User - Empty Body
│
├── Users - PUT/
│   ├── Update User - Valid
│   ├── Update User - Invalid ID
│   └── Update User - Empty Body
│
├── Users - PATCH/
│   ├── Partial Update - Valid
│   └── Partial Update - Invalid ID
│
└── Users - DELETE/
    ├── Delete User - Valid ID
    ├── Delete User - Invalid ID
    └── Delete User - Already Deleted
```

---

## Sample Postman Test Scripts

**Status code assertion:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**Response body validation:**
```javascript
pm.test("Response has correct user data", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.data).to.have.property("id");
    pm.expect(jsonData.data).to.have.property("email");
    pm.expect(jsonData.data.id).to.be.a("number");
    pm.expect(jsonData.data.email).to.be.a("string");
});
```

**Response time assertion:**
```javascript
pm.test("Response time is under 2000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});
```

**Request chaining — save ID from POST:**
```javascript
pm.test("Save created user ID", function () {
    const jsonData = pm.response.json();
    pm.environment.set("created_user_id", jsonData.id);
});
```

**Pre-request script — dynamic data:**
```javascript
const timestamp = Date.now();
pm.environment.set("user_name", `TestUser${timestamp}`);
pm.environment.set("user_job", "QA Engineer");
```

---

## Environments

| Variable | Dev Value | Description |
|----------|-----------|-------------|
| base_url | https://reqres.in/api | API base URL |
| auth_token | Bearer token value | Authorization header |
| created_user_id | dynamic | Set by POST request chain |
| user_name | dynamic | Set by pre-request script |

---

## How to Run Locally

**Prerequisites:**
- Node.js v18 or above
- Newman installed globally

**Step 1 — Clone the repo**
```bash
git clone https://github.com/yourusername/rest-api-testing-suite.git
cd rest-api-testing-suite
```

**Step 2 — Install dependencies**
```bash
npm install
```

**Step 3 — Run collection via Newman**
```bash
newman run postman/collection.json \
  -e postman/environment.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export reports/report.html
```

**Step 4 — Open the HTML report**
```bash
open reports/report.html
```

---

## Project Structure
```
rest-api-testing-suite/
│
├── postman/
│   ├── collection.json          ← exported Postman collection
│   └── environment.json         ← exported Postman environment
│
├── reports/
│   └── .gitkeep                 ← Newman HTML reports generated here
│
├── .github/
│   └── workflows/
│       └── ci.yml               ← GitHub Actions pipeline
│
├── package.json
└── README.md
```

---

## CI/CD Pipeline

Every push to `main` triggers:
```
Install Node.js
      ↓
Install Newman globally
      ↓
Run Postman collection via Newman
      ↓
Generate HTML report
      ↓
Upload report as artifact
      ↓
Fail build if any test fails
```

**ci.yml:**
```yaml
name: API Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install Newman
        run: npm install -g newman newman-reporter-htmlextra

      - name: Run API Tests
        run: |
          newman run postman/collection.json \
            -e postman/environment.json \
            --reporters cli,htmlextra \
            --reporter-htmlextra-export reports/report.html

      - name: Upload HTML Report
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: api-test-report
          path: reports/report.html
```

---

## Newman HTML Report

![Newman Report](#) ← screenshot goes here after project is built

---

## Bug Reports

| Bug ID | Endpoint | Issue | Severity |
|--------|----------|-------|----------|
| BUG-001 | POST /register | Returns 200 instead of 201 on success | Minor |
| BUG-002 | DELETE /users/:id | Returns 204 with no confirmation body | Minor |
| BUG-003 | GET /users/:id | Error message not descriptive for invalid ID | Minor |

---

## Key Learnings

- How to structure a Postman collection professionally
- How to chain requests using environment variables
- How to write pre-request scripts for dynamic test data
- How to validate response body structure and data types
- How to run Postman collections from CLI using Newman
- How to integrate Newman into a CI/CD pipeline

---

## Author

**Your Name**
[LinkedIn](#) | [GitHub](https://github.com/yourusername)

---

## License
MIT

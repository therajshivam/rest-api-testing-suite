# REST API Testing Suite

![Postman](https://img.shields.io/badge/tested%20with-postman-FF6C37.svg)
![Newman](https://img.shields.io/badge/cli-newman-darkgreen)
![GitHub Actions](https://img.shields.io/badge/ci%2Fcd-github%20actions-2088FF.svg)
![License](https://img.shields.io/badge/license-ISC-blue)

> A comprehensive REST API testing suite built with Postman and Newman,
> covering CRUD operations, negative testing, request chaining,
> and automated CI/CD execution via GitHub Actions — demonstrating
> industry-ready API testing skills.

---

## Overview

This project demonstrates a complete API testing workflow — from writing
test cases in Postman to running them automatically via Newman CLI in a
GitHub Actions CI/CD pipeline. The API under test is JSONPlaceholder,
a free fake REST API for testing and learning.

**What is covered:**
- 7 comprehensive API test cases across all HTTP methods
- Positive and negative test scenarios
- Status code validation (200, 201, 404)
- Response body and field validation
- Request chaining — extract and reuse data from previous requests
- Dynamic environment variable management
- Newman CLI execution with HTML report generation
- Automated CI/CD pipeline via GitHub Actions

---

## Application Under Test

| Detail | Value |
|--------|-------|
| API | JSONPlaceholder |
| Base URL | https://jsonplaceholder.typicode.com |
| Type | REST API — User Management (Free Fake API) |
| Auth | None |
| Format | JSON |

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Postman | API test design and execution |
| Newman | CLI runner for Postman collections |
| newman-reporter-htmlextra | HTML report generation |
| GitHub Actions | CI/CD pipeline automation |
| JavaScript | Test assertions and validation |

---

## API Endpoints Tested

| Method | Endpoint | Description | Status |
|--------|----------|-------------|--------|
| GET | /users/1 | Get single user | 200 |
| GET | /users | Get all users | 200 |
| GET | /users/9999 | Get non-existing user | 404 |
| POST | /users | Create new user | 201 |
| PUT | /users/1 | Update user completely | 200 |
| PATCH | /users/1 | Update user partially | 200 |
| DELETE | /users/1 | Delete user | 200/204 |

---

## Test Coverage

| Category | Test Cases |
|----------|-----------|
| GET requests | 3 |
| POST requests | 1 |
| PUT requests | 1 |
| PATCH requests | 1 |
| DELETE requests | 1 |
| Negative cases | 1 |
| **Total** | **7** |

---

## Test Case Types

**Positive Testing**
- GET /users returns 200 with valid user data
- GET /users/1 returns 200 with user ID 1
- POST /users creates user and returns 201
- PUT /users/1 updates all fields and returns 200
- PATCH /users/1 updates single field and returns 200
- DELETE /users/1 returns 200/204

**Negative Testing**
- GET /users/9999 tests handling of non-existent user (404 or empty response)

**Response Validation**
- Status code assertion on every request
- Response body field existence check (e.g., response.id)
- Response body data value check (e.g., response.name matches expected value)
- Array response validation

**Request Chaining & Environment Variables**
- Create User request sets `userId` environment variable from response
- Subsequent requests can reference this variable for testing dependent operations

---

## Postman Collection Structure

```
REST API Testing Suite/
├── Get User 1                    # GET /users/1 - fetch single user
├── Get All Users                 # GET /users - fetch all users
├── Get Invalid User              # GET /users/9999 - negative test (404 handling)
├── Create User                   # POST /users - create with body {name, job}
├── Update User                   # PUT /users/1 - full update
├── Patch User                    # PATCH /users/1 - partial update
└── Delete User                   # DELETE /users/1 - delete operation
```

## Sample Postman Test Scripts

**Status code assertion:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**Response array validation:**
```javascript
pm.test("Response is an array", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.be.an("array");
});

pm.test("Array is not empty", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.length).to.be.above(0);
});
```

**Response body field validation:**
```javascript
pm.test("Response has an id", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property("id");
});

pm.test("Name is Shivam", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.name).to.eql("Shivam");
});
```

**Request chaining — save ID from POST response:**
```javascript
const jsonData = pm.response.json();
pm.environment.set("userId", jsonData.id);
```

---

## Installation & Setup

### Prerequisites
- Node.js 20+ installed
- npm (comes with Node.js)
- Git (for version control)

### Local Setup

```bash
# Clone the repository
git clone <your-repo-url>
cd rest-api-testing-suite

# Install dependencies
npm install

# Run tests via Newman CLI
npx newman run collections/api-tests.postman_collection.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export reports/report.html
```

### View Test Report
Open `reports/report.html` in your browser to view the detailed test report with:
- Pass/fail counts per test
- Response times
- Request/response bodies
- Test assertions

---

## CI/CD Pipeline

Tests automatically run on:
- **Push** to any branch
- **Pull Requests** to any branch

### Workflow File
[.github/workflows/api-tests.yml](.github/workflows/api-tests.yml)

The GitHub Actions workflow:
1. Checks out the code
2. Sets up Node.js 20
3. Installs dependencies
4. Runs Newman with CLI and HTML reporters
5. Uploads test report as artifact

View results in the "Actions" tab of your GitHub repository.

---

## Project Files

- `collections/api-tests.postman_collection.json` — Postman collection with all test cases
- `.github/workflows/api-tests.yml` — GitHub Actions CI/CD configuration
- `.gitignore` — Git ignore rules
- `package.json` — Project metadata and dependencies
- `reports/report.html` — Generated test report (created after running tests)
- `README.md` — This file

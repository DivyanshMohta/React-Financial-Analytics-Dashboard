# Financial Analytics API Documentation

## Overview

The Financial Analytics API provides a comprehensive set of endpoints for managing financial transactions, user authentication, and data analytics. This RESTful API is built with Node.js, Express, and MongoDB, featuring JWT authentication and comprehensive error handling.

## Base URL

```
http://localhost:5000/api
```

## Authentication

All protected endpoints require a JWT token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

## Response Format

All API responses follow a consistent format:

### Success Response

```json
{
  "data": "response_data",
  "message": "Success message",
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "totalPages": 10,
    "hasNext": true,
    "hasPrev": false
  }
}
```

### Error Response

```json
{
  "error": "Error message",
  "details": "Additional error details",
  "field": "Specific field causing error"
}
```

## Endpoints

---

## 🔐 Authentication Endpoints

### 1. Register User

**Endpoint:** `POST /auth/register`

**Description:** Register a new user account

**Request Body:**

```json
{
  "username": "string (3-20 characters, alphanumeric)",
  "password": "string (6-50 characters)"
}
```

**Response (201 Created):**

```json
{
  "message": "User registered successfully",
  "user": {
    "id": "user_id",
    "username": "username"
  }
}
```

**Error Responses:**

- `400 Bad Request`: Invalid input validation
- `409 Conflict`: Username already exists

**Example Request:**

```bash
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john_doe",
    "password": "password123"
  }'
```

---

### 2. Login User

**Endpoint:** `POST /auth/login`

**Description:** Authenticate user and receive JWT token

**Request Body:**

```json
{
  "username": "string",
  "password": "string"
}
```

**Response (200 OK):**

```json
{
  "message": "Login successful",
  "token": "jwt_token_here",
  "user": {
    "id": "user_id",
    "username": "username"
  }
}
```

**Error Responses:**

- `400 Bad Request`: Invalid input validation
- `401 Unauthorized`: Invalid credentials

**Example Request:**

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john_doe",
    "password": "password123"
  }'
```

---

### 3. Get User Profile

**Endpoint:** `GET /auth/profile`

**Description:** Get current user profile information

**Headers:**

```
Authorization: Bearer <jwt_token>
```

**Response (200 OK):**

```json
{
  "user": {
    "id": "user_id",
    "username": "username"
  }
}
```

**Error Responses:**

- `401 Unauthorized`: Invalid or missing token
- `403 Forbidden`: Token expired

**Example Request:**

```bash
curl -X GET http://localhost:5000/api/auth/profile \
  -H "Authorization: Bearer your_jwt_token_here"
```

---

## 💰 Transaction Endpoints

### 4. Get Transactions

**Endpoint:** `GET /transactions`

**Description:** Retrieve paginated list of transactions with filtering and sorting

**Headers:**

```
Authorization: Bearer <jwt_token>
```

**Query Parameters:**

| Parameter   | Type   | Description                                              | Example                 |
| ----------- | ------ | -------------------------------------------------------- | ----------------------- |
| `page`      | number | Page number (default: 1)                                 | `?page=1`               |
| `limit`     | number | Items per page (1-100, default: 10)                      | `?limit=20`             |
| `sortBy`    | string | Sort field (id, date, amount, category, status, user_id) | `?sortBy=date`          |
| `order`     | string | Sort order (asc, desc, default: desc)                    | `?order=asc`            |
| `category`  | string | Filter by category (Revenue, Expense)                    | `?category=Revenue`     |
| `status`    | string | Filter by status (Paid, Pending)                         | `?status=Paid`          |
| `user_id`   | string | Filter by user ID                                        | `?user_id=user123`      |
| `search`    | string | Search across multiple fields                            | `?search=test`          |
| `minAmount` | number | Minimum amount filter                                    | `?minAmount=100`        |
| `maxAmount` | number | Maximum amount filter                                    | `?maxAmount=1000`       |
| `startDate` | string | Start date (YYYY-MM-DD)                                  | `?startDate=2024-01-01` |
| `endDate`   | string | End date (YYYY-MM-DD)                                    | `?endDate=2024-12-31`   |

**Response (200 OK):**

```json
{
  "data": [
    {
      "_id": "transaction_id",
      "id": 1,
      "date": "2024-01-15T00:00:00.000Z",
      "amount": 1500.0,
      "category": "Revenue",
      "status": "Paid",
      "user_id": "user123",
      "user_profile": "John Doe"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "totalPages": 10,
    "hasNext": true,
    "hasPrev": false
  },
  "filters": {
    "applied": ["category", "status"],
    "search": "test"
  }
}
```

**Error Responses:**

- `400 Bad Request`: Invalid query parameters
- `401 Unauthorized`: Invalid or missing token

**Example Request:**

```bash
curl -X GET "http://localhost:5000/api/transactions?page=1&limit=10&sortBy=date&order=desc&category=Revenue&status=Paid" \
  -H "Authorization: Bearer your_jwt_token_here"
```

---

### 5. Get Analytics Data

**Endpoint:** `GET /transactions/analytics`

**Description:** Retrieve aggregated analytics data for dashboard

**Headers:**

```
Authorization: Bearer <jwt_token>
```

**Query Parameters:**

| Parameter   | Type   | Description                           | Example                 |
| ----------- | ------ | ------------------------------------- | ----------------------- |
| `startDate` | string | Start date for analytics (YYYY-MM-DD) | `?startDate=2024-01-01` |
| `endDate`   | string | End date for analytics (YYYY-MM-DD)   | `?endDate=2024-12-31`   |

**Response (200 OK):**

```json
{
  "revenueExpenses": [
    {
      "_id": "Revenue",
      "total": 50000.0,
      "count": 25
    },
    {
      "_id": "Expense",
      "total": 30000.0,
      "count": 15
    }
  ],
  "statusBreakdown": [
    {
      "_id": "Paid",
      "total": 60000.0,
      "count": 30
    },
    {
      "_id": "Pending",
      "total": 20000.0,
      "count": 10
    }
  ],
  "monthlyTrends": [
    {
      "_id": {
        "year": 2024,
        "month": 1
      },
      "revenue": 5000.0,
      "expenses": 3000.0,
      "count": 8
    }
  ],
  "topUsers": [
    {
      "_id": "user123",
      "total": 15000.0,
      "count": 5
    }
  ],
  "dateRange": {
    "startDate": "2024-01-01",
    "endDate": "2024-12-31"
  }
}
```

**Error Responses:**

- `400 Bad Request`: Invalid date parameters
- `401 Unauthorized`: Invalid or missing token

**Example Request:**

```bash
curl -X GET "http://localhost:5000/api/transactions/analytics?startDate=2024-01-01&endDate=2024-12-31" \
  -H "Authorization: Bearer your_jwt_token_here"
```

---

### 6. Get Filter Options

**Endpoint:** `GET /transactions/filters`

**Description:** Retrieve unique values for filter dropdowns

**Headers:**

```
Authorization: Bearer <jwt_token>
```

**Response (200 OK):**

```json
{
  "categories": ["Revenue", "Expense"],
  "statuses": ["Paid", "Pending"],
  "users": ["user123", "user456", "user789"]
}
```

**Error Responses:**

- `401 Unauthorized`: Invalid or missing token

**Example Request:**

```bash
curl -X GET http://localhost:5000/api/transactions/filters \
  -H "Authorization: Bearer your_jwt_token_here"
```

---

### 7. Export CSV

**Endpoint:** `POST /transactions/export`

**Description:** Export filtered transactions as CSV file

**Headers:**

```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body:**

```json
{
  "columns": ["id", "date", "amount", "category", "status", "user_id"],
  "category": "Revenue",
  "status": "Paid",
  "search": "test",
  "sortBy": "date",
  "order": "desc",
  "startDate": "2024-01-01",
  "endDate": "2024-12-31",
  "minAmount": 100,
  "maxAmount": 1000,
  "user_id": "user123"
}
```

**Response (200 OK):**

- File download with CSV content
- Content-Type: `text/csv`
- Filename: `transactions_YYYY-MM-DD.csv`

**CSV Format:**

```csv
id,date,amount,category,status,user_id
1,2024-01-15,1500.00,Revenue,Paid,user123
2,2024-01-16,750.50,Expense,Pending,user456
```

**Error Responses:**

- `400 Bad Request`: Invalid parameters or no data found
- `401 Unauthorized`: Invalid or missing token
- `500 Internal Server Error`: Export generation failed

**Example Request:**

```bash
curl -X POST http://localhost:5000/api/transactions/export \
  -H "Authorization: Bearer your_jwt_token_here" \
  -H "Content-Type: application/json" \
  -d '{
    "columns": ["id", "date", "amount", "category", "status", "user_id"],
    "category": "Revenue",
    "status": "Paid"
  }' \
  --output transactions.csv
```

---

## 🔧 System Endpoints

### 8. Health Check

**Endpoint:** `GET /health`

**Description:** Check API server status

**Response (200 OK):**

```json
{
  "status": "OK",
  "timestamp": "2024-01-15T10:30:00.000Z",
  "uptime": 3600
}
```

**Example Request:**

```bash
curl -X GET http://localhost:5000/api/health
```

---

## 📊 Data Models

### Transaction Model

```json
{
  "_id": "ObjectId",
  "id": "number (unique)",
  "date": "Date",
  "amount": "number",
  "category": "string (Revenue|Expense)",
  "status": "string (Paid|Pending)",
  "user_id": "string",
  "user_profile": "string"
}
```

### User Model

```json
{
  "_id": "ObjectId",
  "username": "string (unique)",
  "password": "string (hashed)",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

---

## 🚨 Error Codes

| Status Code | Description           | Common Causes                             |
| ----------- | --------------------- | ----------------------------------------- |
| `200`       | Success               | Request completed successfully            |
| `201`       | Created               | Resource created successfully             |
| `400`       | Bad Request           | Invalid input parameters                  |
| `401`       | Unauthorized          | Missing or invalid JWT token              |
| `403`       | Forbidden             | Token expired or insufficient permissions |
| `404`       | Not Found             | Resource not found                        |
| `409`       | Conflict              | Resource already exists                   |
| `500`       | Internal Server Error | Server-side error                         |

---

## 🔒 Security

### JWT Token Format

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI2NGE5YjQ5YjQ5YjQ5YjQ5IiwidXNlcm5hbWUiOiJqb2huX2RvZSIsImlhdCI6MTcwNTQ5MjAwMCwiZXhwIjoxNzA1NTc4NDAwfQ.signature
```

### Token Expiration

- JWT tokens expire after 24 hours
- Refresh token functionality not implemented in current version

### Password Requirements

- Minimum 6 characters
- Maximum 50 characters
- Passwords are hashed using bcryptjs with 12 salt rounds

---

## 📝 Validation Rules

### Username Validation

- Minimum 3 characters
- Maximum 20 characters
- Alphanumeric characters only
- Must be unique

### Password Validation

- Minimum 6 characters
- Maximum 50 characters
- No specific character requirements

### Transaction Amount Validation

- Must be a positive number
- Maximum value: 999999999.99

### Date Validation

- Must be valid ISO date format
- Start date cannot be after end date
- Date range cannot exceed 1 year

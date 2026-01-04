# JSON Normalization Workflow with n8n

A production-ready n8n workflow that transforms messy CSV data with inconsistent headers into clean, validated JSON output. Built for automation pipelines at Trilles AI.

## 🎯 Overview

This workflow solves a common data integration challenge: handling CSV imports with inconsistent or varying column names. It automatically detects, maps, and validates data against a strict schema, ensuring all downstream systems receive reliable, structured data.

### Key Features

- **Smart Header Detection**: Automatically maps variations like "F. Name", "fname", "First Name" → `firstName`
- **Schema Validation**: Enforces required fields before data reaches downstream systems
- **Error Tracking**: Captures unmapped fields and validation failures without breaking the workflow
- **Production-Safe**: No silent failures—every error is logged with context

## 🏗️ Architecture

```
Manual Trigger
    ↓
Mock CSV Input
    ↓
Normalize Headers + Track Errors
    ↓
Schema Validation Gate (IF Node)
    ↓               ↓
Valid Data    Invalid Data
    ↓               ↓
Final Output   Error Formatter
```

## 📋 Workflow Nodes

| Node | Type | Purpose |
|------|------|---------|
| **Manual Trigger** | Trigger | Initiates workflow execution |
| **Mock CSV Input** | Code | Simulates incoming CSV data with inconsistent headers |
| **Normalize Headers** | Code | Maps dynamic headers to strict schema (`firstName`, `email`) |
| **Schema Validation Gate** | IF | Routes valid/invalid records to appropriate handlers |
| **Final Output** | Code | Produces clean, trimmed JSON for downstream consumption |
| **Error Formatter** | Code | Structures error details for logging and monitoring |

## 🔧 How It Works

### 1. Header Normalization

The `normalizeKey()` function intelligently maps header variations:

```javascript
// Example mappings
"F. Name" → firstName
"First Name" → firstName
"fname" → firstName
"Email Address" → email
"e-mail" → email
```

**Unmapped headers** are automatically captured in an error log for review.

### 2. Schema Validation

The workflow enforces a strict schema with two required fields:
- `firstName` (string)
- `email` (string)

Records missing either field are routed to error handling instead of failing silently.

### 3. Error Handling

Each error includes comprehensive context:

```json
{
  "status": "FAILED",
  "reason": "Schema validation failed",
  "receivedData": { "Name": "John" },
  "detectedIssues": ["Missing required field: email"],
  "timestamp": "2025-01-04T10:30:00.000Z"
}
```

## 📊 Sample Transformation

### Input (Messy CSV)
```json
[
  { "F. Name": "Ali", "Email Address": "ali@gmail.com" },
  { "First Name": "Sara", "email": "sara@company.com" },
  { "Name": "Usman", "e-mail": "usman@yahoo.com" }
]
```

### Output (Clean JSON)
```json
[
  { "firstName": "Ali", "email": "ali@gmail.com" },
  { "firstName": "Sara", "email": "sara@company.com" },
  { "firstName": "Usman", "email": "usman@yahoo.com" }
]
```

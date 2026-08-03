<div align="center">

# 🚀 Enterprise Development Agent Setup
### Claude Code Development Instructions for `ecommerce-api`

<img src="https://img.shields.io/badge/Spring_Boot-3.x-brightgreen?style=for-the-badge&logo=springboot"/>
<img src="https://img.shields.io/badge/Java-21-orange?style=for-the-badge&logo=openjdk"/>
<img src="https://img.shields.io/badge/Maven-3.9-red?style=for-the-badge&logo=apachemaven"/>
<img src="https://img.shields.io/badge/MySQL-8-blue?style=for-the-badge&logo=mysql"/>
<img src="https://img.shields.io/badge/Claude_Code-AI-purple?style=for-the-badge"/>

---

### 📚 Project
**ecommerce-api**

Production Quality Development Setup

</div>

---

# 📑 Table of Contents

- Overview
- Claude Configuration
- Rule Configuration
- Git Setup
- Git Ignore Review
- API Error Standards
- Generate Data Layer
- Database Configuration
- Business Rules
- Expected Project Structure
- Definition of Done

---

# 🎯 Objective

Configure an enterprise development environment for **Claude Code**
and generate a complete production-ready data layer for the
`ecommerce-api` project.

The implementation must follow:

- Clean Architecture
- Spring Boot Best Practices
- SOLID Principles
- REST Standards
- Java Coding Standards
- Enterprise Naming Conventions

---

# ⚙️ Step 1 — Configure Claude

## Global Claude Configuration

Copy

```
resources/global/claude.md
```

into

```
C:\Users\hp\.claude\
```

Result

```
C:\
└── Users
    └── hp
        └── .claude
            └── claude.md
```

---

## Project Claude Configuration

Inside the project create

```
.claude
```

Copy

```
resources/project/claude.md
```

to

```
ecommerce-api/
    .claude/
        claude.md
```

Final Structure

```
ecommerce-api
│
├── .claude
│      claude.md
│
└── src
```

---

# 📜 Step 2 — Configure Rules

Inside

```
.claude
```

create

```
rules
```

Copy

```
resources/rules/api-design.md
resources/rules/testing.md
```

Final structure

```
ecommerce-api

│
├── .claude
│
├── claude.md
│
└── rules
    ├── api-design.md
    └── testing.md
```

---

# 🔍 Step 3 — Review Changes

Always inspect generated files before committing.

```bash
git diff
```

Expected Review

- Added files
- Modified files
- Deleted files
- Formatting changes
- Generated code quality

---

# 🌐 Step 4 — API Error Standard

All API errors must follow a consistent JSON structure.

## Standard Response

```json
{
  "timestamp": "2026-07-29T14:25:11",
  "status": 404,
  "error": "NOT_FOUND",
  "message": "Product not found",
  "path": "/api/products/101",
  "traceId": "abc123xyz"
}
```

---

## Validation Error

```json
{
  "timestamp": "...",
  "status": 400,
  "error": "VALIDATION_ERROR",
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "must be a valid email"
    },
    {
      "field": "password",
      "message": "must not be blank"
    }
  ]
}
```

---

## Error Conventions

| Property | Required |
|------------|----------|
| timestamp | ✅ |
| status | ✅ |
| error | ✅ |
| message | ✅ |
| path | ✅ |
| traceId | ✅ |
| validation errors | Optional |

---

# 📦 Step 5 — Review .gitignore

Review the existing `.gitignore`.

Ensure support for

- Spring Boot
- Maven
- IntelliJ
- VS Code
- Claude Code
- MySQL
- Logs
- Build Artifacts

---

## Missing Entries

```gitignore
#################################################
# Claude Code
#################################################

.claude/cache/
.claude/tmp/
.claude/logs/

#################################################
# VS Code
#################################################

.vscode/

#################################################
# IntelliJ
#################################################

.idea/
*.iml

#################################################
# Maven
#################################################

target/

#################################################
# Spring Boot
#################################################

*.log
logs/

#################################################
# OS Files
#################################################

.DS_Store
Thumbs.db

#################################################
# Environment
#################################################

.env
.env.*

#################################################
# Database
#################################################

*.sql.bak

#################################################
# Temporary
#################################################

tmp/
temp/

#################################################
# Generated Files
#################################################

generated/
```

---

## Why These Were Added

| Entry | Reason |
|--------|--------|
| target | Maven build |
| .idea | IntelliJ |
| .vscode | VS Code |
| .claude/cache | Claude cache |
| logs | Spring logs |
| .env | Secrets |
| generated | Generated code |
| tmp | Temporary files |

---

# 🌳 Step 6 — Git Setup

Initialize repository

```bash
git init
```

Stage files

```bash
git add .
```

First Commit

```bash
git commit -m "chore: initial project setup"
```

Repository Status

```bash
git status
```

---

# 🗄 Step 7 — Generate Data Layer

Before generating code

Read

```
db/schema.sql
```

Generate

✅ Entities

✅ Repositories

✅ Enums

✅ Relationships

✅ Cascade Rules

✅ Validation

---

# ⚙ Application Configuration

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/ecommerce_db

spring.datasource.username=root

spring.datasource.password=root12345

spring.jpa.hibernate.ddl-auto=validate

spring.jpa.show-sql=true

spring.jpa.properties.hibernate.format_sql=true
```

---

# 🧩 Required Enums

---

## OrderStatus

```java
PENDING

CONFIRMED

PROCESSING

SHIPPED

DELIVERED

CANCELLED

REFUNDED
```

---

## PaymentMethod

```java
CREDIT_CARD

DEBIT_CARD

UPI

NET_BANKING

WALLET

CASH_ON_DELIVERY
```

---

## PaymentStatus

```java
PENDING

COMPLETED

FAILED

REFUNDED
```

---

# 📋 Business Rules

## Rule 1

Generate Order Number automatically.

Format

```
ORD-AB12CD34
```

Implementation

```
ORD-
+
First 8 uppercase UUID characters
```

Example

```
ORD-8F9A2BCD
```

---

## Rule 2

OrderItem subtotal

Automatically calculate

```
subtotal = unitPrice × quantity
```

No manual assignment.

---

## Rule 3

Saving an Order

Must cascade automatically

```
Order

├── Order Items

└── Payment
```

Recommended Cascade

```java
CascadeType.ALL
```

---

# 📁 Expected Package Structure

```
src/main/java

com.example.ecommerce

├── config
│
├── controller
│
├── dto
│
├── entity
│
│   ├── Order
│   ├── OrderItem
│   ├── Payment
│   ├── Product
│   └── Customer
│
├── enums
│
│   ├── OrderStatus
│   ├── PaymentMethod
│   └── PaymentStatus
│
├── repository
│
├── service
│
├── exception
│
└── util
```

---

# ✅ Definition of Done

- [ ] Claude configured globally
- [ ] Claude configured locally
- [ ] Rules added
- [ ] `.gitignore` reviewed
- [ ] Git initialized
- [ ] Initial commit completed
- [ ] Database schema reviewed
- [ ] Entities generated
- [ ] Repositories created
- [ ] Enums implemented
- [ ] Relationships mapped
- [ ] Cascade rules applied
- [ ] Order number generation implemented
- [ ] Subtotal auto-calculation implemented
- [ ] SQL logging enabled
- [ ] `ddl-auto=validate` configured
- [ ] Project builds successfully
- [ ] Tests pass

---

<div align="center">

# 🎉 Enterprise Setup Complete

**Ready for production-grade development with Claude Code.**

> Follow this checklist before every new feature or release to maintain consistency and code quality.

</div>
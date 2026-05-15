# Day 7 - Testing & Developer Experience (DX)

## 1. Why Testing Matters

Testing is like checking your homework before submitting it. It helps you find mistakes before they become big problems.

### Why Testing is Important in Salesforce:

| Reason | Explanation |
|--------|-------------|
| **Finds bugs early** | Catch errors before users see them |
| **Prevents breaking old code** | New changes shouldn't break working features |
| **Required for deployment** | Salesforce needs 75% code coverage to deploy |
| **Saves time** | Fixing bugs after deployment is expensive and slow |
| **Builds trust** | Users trust software that works correctly every time |

### Salesforce Testing Rule:

Before deploying any Apex code:
- ✅ At least **75% code coverage**
- ✅ All test methods must **pass**
- ✅ Every trigger must have **some coverage**

Without tests → No deployment to production.

---

## 2. What is Asynchronous Apex?

**Simple meaning:** Code that runs in the background, not right away.

### Synchronous vs Asynchronous:

| Synchronous (Normal) | Asynchronous (Background) |
|---------------------|---------------------------|
| Runs immediately | Runs later |
| User waits for result | User can do other work |
| Slower for big tasks | Faster for complex operations |
| Governor limits apply strictly | Higher limits for some operations |

### Examples of Asynchronous Apex:

| Method | What it does | When to use |
|--------|--------------|-------------|
| **Future Methods** | Runs in background | Web callouts, heavy processing |
| **Queueable Apex** | Better than future, can chain jobs | Complex background jobs |
| **Batch Apex** | Processes millions of records | Large data cleanup, reporting |
| **Scheduled Apex** | Runs at specific time | Daily reports, midnight cleanup |

### Real Example:

**Without Asynchronous:**
User clicks button → Waits 2 minutes while 10,000 records process → User frustrated

**With Asynchronous:**
User clicks button → "Processing in background" message → User continues working → Email sent when done

---

## 3. What is Salesforce DX?

**Simple meaning:** A set of tools that helps developers write, test, and manage Salesforce code professionally.

### Main Components:

| Component | What it does |
|-----------|--------------|
| **Salesforce CLI** | Control Salesforce using terminal commands |
| **Scratch Orgs** | Temporary practice environments |
| **Dev Hub** | Main org that manages scratch orgs |
| **Source Control (Git)** | Track code changes, work with teams |

### Why Salesforce DX Matters:

| Problem | Solution with DX |
|---------|------------------|
| Working on same code as team | Git branches keep everyone's work separate |
| Testing new features safely | Scratch orgs = temporary practice environments |
| Deploying to production | Structured workflow prevents mistakes |
| Tracking who changed what | Git history shows every change |

### Important CLI Commands:

| Command | Purpose |
|---------|---------|
| `sf org login web -d -a DevHub` | Connect to Dev Hub |
| `sf org create scratch -f config.json -a my-org` | Create scratch org |
| `sf project deploy start` | Deploy code to scratch org |
| `sf org open` | Open scratch org in browser |

### The DX Workflow:

```
GitHub (Code Storage)
    ↓
Clone to Computer
    ↓
Create Scratch Org
    ↓
Deploy Code
    ↓
Test & Fix
    ↓
Commit to GitHub
    ↓
Deploy to Production
```

---

## 4. Complete System Workflow (End-to-End)

Here is how everything connects from Day 1 to Day 7:

### Step-by-Step Flow:

```
┌─────────────────────────────────────────────────────────────────┐
│                    BUSINESS REQUIREMENT                          │
│   "We need a College Management System"                          │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│                     DAY 1-3: DATA MODELING                       │
│  • Create Objects (Student, Course, Faculty, Enrollment)        │
│  • Add Fields (Name, Email, GPA, Max Seats)                     │
│  • Create Relationships (Student ↔ Course)                      │
│  • Add Validation Rules (Email required, Age not negative)      │
│  • Add Formula Fields (Full Name, Remaining Seats)              │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│                     DAY 4: AUTOMATION (FLOWS)                    │
│  • Auto-email after registration                                 │
│  • Update remaining seats when student enrolls                  │
│  • Notify faculty when course is full                           │
│  • Send reminder before fee deadline                            │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│                   DAY 5-6: CODE (APEX & TRIGGERS)               │
│  • Complex fee calculation (Apex)                               │
│  • Integration with payment gateway                             │
│  • Auto block registration if fees pending (Trigger)            │
│  • Send welcome email after insert (Trigger)                    │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│                     DAY 7: TESTING & DX                          │
│  • Write unit tests for Apex code (75% coverage)                │
│  • Test triggers (valid and invalid data)                       │
│  • Create scratch org for safe testing                          │
│  • Deploy to production only after all tests pass               │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│                    PRODUCTION DEPLOYMENT                         │
│   Students can register, faculty can manage, system works! ✅    │
└─────────────────────────────────────────────────────────────────┘
```

### Complete College Management System Example:

| Layer | Technology Used | Example |
|-------|----------------|---------|
| **Data Storage** | Objects, Fields | Student object with Name, Email, GPA fields |
| **Data Quality** | Validation Rules, Formulas | Email cannot be empty, Full Name auto-calculated |
| **Automation** | Flow Builder | Auto-update remaining seats after enrollment |
| **Complex Logic** | Apex, Triggers | Calculate total fees, send email on registration |
| **Integration** | SOQL, DML | Query student data, insert enrollment record |
| **Testing** | Apex Unit Tests | Test registration with valid/invalid data |
| **Deployment** | Salesforce DX, Scratch Orgs | Test in scratch org, then deploy to production |

---

## 5. Important Test Cases (Your Examples)

### Test Cases for College Management System:

| Test Case | Type | What it checks | Expected Result |
|-----------|------|----------------|-----------------|
| **Valid Student Registration** | Positive | Student with all correct fields | Record saved ✅ |
| **Empty Email** | Negative | Email field left blank | Error message, record not saved ❌ |
| **Negative Age** | Boundary | Age = -5 | Error message, record not saved ❌ |
| **Enroll When Seats Full** | Negative | Course has 0 remaining seats | Block enrollment, show error ❌ |
| **Valid Course Enrollment** | Positive | Student enrolls with seats available | Enrollment created, seats decrease ✅ |
| **Calculate Total Fees** | Logic | Student takes 3 courses | Correct total amount calculated ✅ |
| **Trigger: Block Invalid Name** | Trigger | Contact LastName = 'INVALIDNAME' | Error message, save blocked ❌ |
| **Trigger: Valid Name** | Trigger | Contact LastName = 'Kumar' | Record saved successfully ✅ |

### Why Multiple Test Cases Matter:

| Test Type | Purpose |
|-----------|---------|
| **Positive Tests** | Ensure good data works |
| **Negative Tests** | Ensure bad data is blocked |
| **Boundary Tests** | Test edge cases (zero, negative, maximum) |
| **Bulk Tests** | Test with 200+ records at once |

### Rule of Thumb:

> One test method is NOT enough. Test normal values, negative values, boundary values, and invalid values.

---

## 6. Reflection: Why Enterprise Software Development Needs Structured Workflows

### The Problem Without Structure:

| Problem | What Happens |
|---------|--------------|
| **No testing** | Bug reaches production, users angry |
| **No version control** | Two developers overwrite each other's work |
| **No scratch orgs** | Testing in production breaks real data |
| **No deployment process** | Changes deployed directly, no review |
| **No documentation** | Nobody understands how system works |

### The Solution: Structured Workflow

```
Local Development (Scratch Org)
        ↓
Write Tests
        ↓
Run Tests (Must pass)
        ↓
Code Review (Team checks)
        ↓
Deploy to Testing Org
        ↓
User Acceptance Testing
        ↓
Deploy to Production
```

### Why This Matters for Enterprises:

| Reason | Explanation |
|--------|-------------|
| **Multiple Developers** | 10+ people cannot edit same file without Git |
| **Business Critical Data** | One wrong deployment can lose customer data |
| **Compliance (Legal)** | Banks, hospitals must prove testing was done |
| **Team Collaboration** | Structured workflow prevents conflicts |
| **History & Audit** | Know who changed what and when |
| **Rollback Ability** | Undo bad changes quickly |

### Simple Analogy:

| Without Structure | With Structure |
|------------------|----------------|
| Building a house without blueprint | House built from architect's plan |
| No inspection before moving in | Electric, plumbing, safety inspected |
| One worker changes everything | Each worker has clear role |
| No documentation for repairs | Blueprint available for future fixes |

### Final Thought:

> Testing and structured workflows may feel slow at first, but they prevent disasters. Enterprise systems cannot afford to "try and see what happens." They need confidence that every change works correctly.

---

**Date:** May 15, 2026
```

---

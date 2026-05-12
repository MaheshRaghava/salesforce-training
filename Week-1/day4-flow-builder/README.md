# Day 4: Flow Builder & Automation

## 1. What is Flow Builder?

Flow Builder is a tool in Salesforce used to create automation using clicks instead of coding. It is a visual drag-and-drop tool where you create automation using boxes and arrows - no coding required.

Flow Builder helps automate tasks like:

- Creating records automatically
- Updating records
- Sending emails or notifications
- Asking users for input through screens
- Making decisions automatically

A flow works like a flowchart. Each box does some work, and arrows show the path.

**Key Benefits:**

| Benefit | Explanation |
|---------|-------------|
| Saves Time | Users don't repeat manual work again and again |
| Improves Data Quality | Automation reduces mistakes |
| Makes Work Faster | Processes happen automatically |
| No Coding Needed | Admins can build automation using clicks |

---

## 2. Types of Flows

### Screen Flow

A Screen Flow shows screens to users like a form. It is used when user input is needed.

**What it does:**
- Asks questions
- Collects data
- Shows messages
- Creates records automatically

**Example:** A customer support form asking for Name, Phone Number, and Issue Type. After the user fills it, Salesforce automatically creates records.

**When to use:** When you need user interaction (buttons, entering information, answering questions).

---

### Record-Triggered Flow

A Record-Triggered Flow runs automatically when records are created, updated, or deleted. Users may not even notice them running.

**What it does:**
- Runs when record is created
- Runs when record is updated
- Runs when record is deleted

**Example:** When Opportunity Stage becomes "Closed Won", automatically create a Task, send an Email, and update the Account.

**When to use:** When automation should happen behind the scenes without user interaction.

---

### Other Flow Types (Quick Overview)

| Flow Type | When It Runs |
|-----------|--------------|
| Autolaunched Flow | From button click or another flow (no screens) |
| Schedule-Triggered Flow | At specific date and time (e.g., daily at 9 AM) |
| Platform Event-Triggered Flow | When Salesforce receives an event message |

---

## 3. My Automation Ideas (College Management System)

Here are **5 processes** in a College Management System that can be automated using Flow Builder:

| # | Automation Idea | How It Works | Why Automation Helps |
|---|----------------|--------------|----------------------|
| 1 | **Auto email after registration** | When a student registers for a course, Salesforce automatically sends a confirmation email with course details | Saves staff time; student gets instant confirmation |
| 2 | **Auto update remaining seats** | When a student enrolls, the "Remaining Seats" field decreases automatically | Prevents manual counting; avoids overbooking |
| 3 | **Notify faculty when course is full** | When a course reaches maximum seats, automatically send a notification to the faculty | Faculty can plan accordingly; no need to check manually |
| 4 | **Generate student ID automatically** | When a new student record is created, auto-generate a unique Student ID using auto-number format | Ensures every student has unique ID; no manual entry errors |
| 5 | **Send reminder before fee deadline** | Schedule a flow to run daily that checks for upcoming fee deadlines and sends email reminders to students | Reduces late payments; students never miss deadlines |

---

## 4. My Flow Diagram

Below is the diagram for one automation process from above:

### Process Selected: Auto Update Remaining Seats

┌─────────────────────────────────────────────────────────────────┐
│                           TRIGGER                               │
│                   New Enrollment record is created              │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                         GET RECORDS                             │
│                   Find the related Course record                │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                           DECISION                              │
│                     Is Remaining Seats > 0?                     │
└─────────────────────────────────────────────────────────────────┘
                    │                           │
                    │ Yes                       │ No
                    ▼                           ▼
┌──────────────────────────────┐    ┌──────────────────────────────┐
│       UPDATE RECORD          │    │     SEND NOTIFICATION        │
│   Decrease Remaining Seats   │    │    Course Full Alert to      │
│           by 1               │    │          Faculty             │
└──────────────────────────────┘    └──────────────────────────────┘
                    │                           │
                    └──────────────┬────────────┘
                                   ▼
                    ┌──────────────────────────────┐
                    │        FINAL ACTION          │
                    │     Show success message     │
                    └──────────────────────────────┘

---

## 5. Manual vs Automated Process

### Real-Life Process Example: Student Course Registration

| Aspect | Manual Process | Automated Process (Salesforce Flow) |
|--------|----------------|-------------------------------------|
| **How it works** | 1. Student fills paper form or sends email<br>2. Staff checks available seats manually<br>3. Staff creates enrollment record<br>4. Staff sends confirmation email<br>5. Staff updates remaining seats in Excel | 1. Student submits online form (Screen Flow)<br>2. Flow automatically checks available seats<br>3. Flow creates enrollment record<br>4. Flow sends confirmation email<br>5. Flow updates remaining seats |
| **Time taken** | 10-15 minutes per student | 30 seconds (fully automatic) |
| **Manual effort** | Staff does everything step by step | No staff intervention needed |

### Problems in Manual Process:

| Problem | Example |
|---------|---------|
| Slow processing | Students wait hours or days for confirmation |
| Human errors | Staff may enter wrong seat count or miss an email |
| Inconsistent data | Different staff members enter data differently |
| No real-time visibility | Nobody knows current seat availability without asking staff |
| High workload | Staff spends hours on repetitive data entry |

### How Salesforce Automation Improves It:

| Improvement | Explanation |
|-------------|-------------|
| **Speed** | Registration happens instantly, 24/7 |
| **Accuracy** | No manual typing errors in seat counts |
| **Real-time updates** | Remaining seats update immediately after each enrollment |
| **Consistency** | Every student gets same process every time |
| **Staff efficiency** | Staff focuses on student support, not data entry |
| **Student experience** | Students get instant confirmation and peace of mind |

---

## 6. Reflection: Why Automation Matters in Enterprise Systems

### Why Do Companies Automate Workflows?

Companies automate workflows because manual processes waste time, create mistakes, and slow down business. Automation lets computers handle repetitive tasks so humans can focus on important work.

### What Problems Happen with Manual Processes?

| Problem | Real Impact |
|---------|-------------|
| Slow work | Customers wait too long for responses |
| Human errors | Wrong data entered, missed follow-ups |
| Inconsistency | Same task done differently by different people |
| Low productivity | Employees waste time on repetitive data entry |
| No scalability | More work means hiring more people |
| Data quality issues | Bad data leads to bad business decisions |

### Why is No-Code Automation Powerful?

No-code automation (like Flow Builder) is powerful because:

- **Admins can build it** - No need to hire expensive developers
- **Faster to build** - Changes take hours, not weeks
- **Easier to maintain** - Anyone can understand and edit flows
- **Less expensive** - No coding costs or developer time

### When Should Automation Be Avoided?

Automation should be avoided when:

| Situation | Why Avoid Automation |
|-----------|---------------------|
| Process changes too frequently | Hard to keep updating automation |
| Very complex decision-making | Human judgment is better |
| Low volume of work | Automation setup takes longer than manual work |
| One-time task | Not worth building automation |
| Requires human empathy | Customer service needs human touch |

### How Does Automation Improve Consistency and Productivity?

| Benefit | How It Helps |
|---------|---------------|
| **Consistency** | Every record follows same rules every time |
| **Productivity** | Employees do more valuable work, not data entry |
| **Speed** | Tasks complete instantly, not after hours/days |
| **Accuracy** | No typos, no missed steps, no forgotten tasks |
| **Scalability** | Handle 10 records or 10,000 records with same effort |
| **Audit trail** | Every automated action is logged and trackable |

---

### Main Idea

Salesforce automation helps companies:
- Work faster
- Reduce errors
- Save time
- Improve productivity

That is why automation is one of the most powerful features in Salesforce.

---

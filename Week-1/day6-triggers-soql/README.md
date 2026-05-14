# Day 6: Triggers and SOQL

## 1. What is SOQL?

SOQL stands for **Salesforce Object Query Language**.

It is used to:
- Read data from Salesforce
- Search records from objects
- Retrieve information from Salesforce database

SOQL is similar to SQL, but it works with:
- **Objects** instead of tables
- **Fields** instead of columns
- **Records** instead of rows

### Example:
```sql
SELECT Id, Name FROM Account
```
This gets the Id and Name from all Account records.

### Key Points:
- SOQL is **read only** (only retrieves data)
- `SELECT *` is not allowed in SOQL
- Use `SELECT FIELDS(ALL)` or specify fields manually

---

## 2. What is an Apex Trigger?

An **Apex Trigger** is a piece of Apex code that runs **automatically** when records are changed in Salesforce.

### When does a trigger run?
A trigger runs when records are:
- **inserted** (new record created)
- **updated** (existing record changed)
- **deleted** (record removed)
- **undeleted** (record restored from recycle bin)

### Example:
```apex
trigger HelloWorldTrigger on Account (before insert) {
    System.debug('Hello World!');
}
```
This prints "Hello World!" every time a new Account is about to be saved.

### Why Use Triggers?
Use triggers when normal Salesforce tools are not enough:
- Automatic updates
- Complex validations
- Creating related records
- Preventing deletions
- Sending notifications

---

## 3. Differences

### Flow vs Trigger

| Flow | Trigger |
|------|---------|
| No-code / low-code tool | Requires writing Apex code |
| Best for simple automation | Best for complex business logic |
| Easy to build and maintain | More powerful and flexible |
| Cannot make callouts to external systems | Can make callouts using @future |
| Good for screen flows and user interaction | Runs entirely in background |
| Limited error handling | Full error handling with try/catch |

**When to use Flow:** Simple email notification, updating a field, screen forms

**When to use Trigger:** Complex fee calculation, integration with external payment system, advanced eligibility logic

---

### Before vs After Trigger

| Before Trigger | After Trigger |
|----------------|---------------|
| Runs **before** record is saved | Runs **after** record is saved |
| Can modify the same record directly | Cannot modify the same record (read-only) |
| No need to use update statement | Can create/update related records |
| Best for validation and field updates | Best for creating child records |
| More efficient (no extra DML) | Record Id is available |

**Example - Before Trigger:**
```apex
trigger Example on Account(before insert) {
    for(Account a : Trigger.new){
        a.Description = 'New Account';
    }
}
```

**Example - After Trigger:**
```apex
trigger Example on Account(after insert) {
    // Create related Opportunity here
    // Record Id is available
}
```

---

## 4. Your Trigger Use Cases (5 Examples)

Using the **College Management System**:

| # | Trigger Use Case | Event | Why Trigger? |
|---|-----------------|-------|---------------|
| 1 | After student registers → Send welcome email | After insert on Student | Email needs to go after record is saved with Id |
| 2 | After course becomes full → Notify faculty | After update on Course | Need to check enrollment count and send notification |
| 3 | After attendance drops below 75% → Send warning | After update on Attendance | Complex calculation across multiple records |
| 4 | Before deleting faculty → Check if assigned to courses | Before delete on Faculty | Need to prevent deletion if relationships exist |
| 5 | After fee payment → Update student status | After insert on Payment | Need to update related Student record automatically |

### Explanation of Each:

1. **Welcome Email** → When a student record is created, automatically send email with login details
2. **Course Full Alert** → When course enrollment reaches max seats, notify the professor
3. **Low Attendance Warning** → If student attendance falls below 75%, send SMS/email alert
4. **Prevent Faculty Deletion** → Block deleting a professor who is currently teaching courses
5. **Update Fee Status** → When payment is recorded, automatically mark student fee as "Paid"

---

## 5. Query Examples (English Query Ideas)

Using the **College Management System** objects:
- Student
- Course
- Faculty
- Enrollment

### English Queries:

| # | English Query | What it finds |
|---|---------------|----------------|
| 1 | Find all students in Course A | All students enrolled in "Computer Science 101" |
| 2 | Find all courses handled by Faculty X | All courses taught by "Professor Sharma" |
| 3 | Find students with attendance below 75% | Students who may need attendance warning |
| 4 | Find all courses with available seats | Courses where remaining seats > 0 |
| 5 | Find students who haven't paid fees | Students with fee status = "Pending" |
| 6 | Find all courses a specific student is taking | Student "John Doe" enrolled courses list |
| 7 | Find faculty teaching more than 3 courses | Overloaded faculty members |
| 8 | Find courses with no enrolled students | Empty courses that may need cancellation |
| 9 | Find students who completed all requirements | Graduation eligible students |
| 10 | Find top 5 courses with highest enrollment | Popular courses ranking |

### SOQL Equivalent Example:
```sql
SELECT Name, Course__r.Name
FROM Student__c
WHERE Course__r.Name = 'Computer Science 101'
```

---

## 6. Reflection: Why Enterprise Systems React Automatically to Data Changes

### Why do systems need triggers?

Enterprise systems need **event-driven behavior** because:

| Problem with Manual Process | How Automatic Triggers Help |
|----------------------------|------------------------------|
| People forget to take action | System never forgets |
| Delays between data change and response | Instant reaction |
| Human errors in repetitive tasks | Consistent every time |
| Wasted time on manual follow-ups | Frees up employees |
| Inconsistent processes across team | Same rules for everyone |

### Real-Life Example:

**Without Trigger (Manual):**
1. Student registers for course
2. Admin manually checks email
3. Admin manually sends welcome email
4. Admin manually updates course seat count
5. Admin manually notifies faculty

**With Trigger (Automatic):**
1. Student registers
2. Trigger fires automatically
3. Welcome email sent ✅
4. Seat count updated ✅
5. Faculty notified ✅

All happens in **real-time**, without human intervention.

### Why This Matters:

- **Faster response** → Students get immediate confirmation
- **No human error** → No forgotten emails or wrong updates
- **Consistency** → Every student gets same experience
- **Scalability** → Works for 1 student or 1000 students
- **Better resource use** → Staff focuses on teaching, not paperwork

### Key Takeaway:

> Enterprise systems use triggers because manual processes do not scale. When thousands of data changes happen daily, automatic reactions are the only way to keep the system accurate and responsive.

---

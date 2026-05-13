# Day 5: Apex Introduction

## 1. What is Apex?

Apex is Salesforce's programming language. It is very similar to Java and C#.

You use Apex when:
- Flow is not enough
- You need advanced logic
- You want custom automation

**Key Features of Apex:**

| Feature | Description |
|---------|-------------|
| Object-Oriented | Supports classes, objects, methods, interfaces, inheritance |
| Strongly Typed | Every variable must have a data type |
| Database Integrated | Works directly with Salesforce records |
| Multitenant-Aware | Has Governor Limits to prevent resource overload |
| Easy Testing | Supports unit testing with 75% code coverage requirement |

**Example Apex Class:**
```apex
public with sharing class HelloWorld {
    public void printMessage() {
        String msg = 'Hello World';
        System.debug(msg);
    }
}
```

**Apex Data Types:**

| Data Type | Example |
|-----------|---------|
| Integer | 10 |
| Double | 12.5 |
| String | "Hello" |
| Boolean | true |
| Date | 2026-05-13 |
| Id | 0015000000Gv7qJAAN |

**Collections in Apex:**

| Collection | Description | Example |
|------------|-------------|---------|
| List | Ordered values | `List<String> names = new List<String>();` |
| Set | Unique values only | `Set<String> cities = new Set<String>();` |
| Map | Key-value pairs | `Map<Id, Account> accountMap;` |

---

## 2. Difference: Flow vs Apex & Configuration vs Coding

### Flow vs Apex

| Flow | Apex |
|------|------|
| No-code / Low-code tool | Programming language |
| Best for simple business processes | Best for complex logic |
| Can be built by Admins | Requires Developer |
| Limited logic capabilities | Full programming power |
| Cannot make web callouts directly | Can integrate with external systems |
| Easy to maintain | Requires testing and deployment |

### Configuration vs Coding

| Configuration (No Code / Low Code) | Coding (Apex, LWC, etc.) |
|-----------------------------------|---------------------------|
| No programming language required | Requires writing code |
| Done using point-and-click tools | Done using Apex, JavaScript |
| Faster to build and deploy | Takes more time |
| Best for simple business processes | Best for complex logic, integrations |
| Can be done by Salesforce Admins | Requires Salesforce Developers |
| Examples: Flow Builder, Validation Rules | Examples: Apex Triggers, Classes |

**When to use Configuration:**
- Creating custom objects and fields
- Building screen flows
- Setting up validation rules
- Automating email alerts

**When to use Coding (Apex):**
- Complex fee calculations
- Integration with external payment systems
- Advanced eligibility logic
- Web service callouts
- Bulk data processing

---

## 3. Real Examples Where Apex Is Needed (3 Examples)

### Example 1: Complex Fee Calculation

**Scenario:** A university has a complex fee structure based on:
- Number of courses
- Student's year (freshman/senior)
- Scholarship percentage
- Late payment penalties
- Installment plans

**Why Flow is NOT enough:**
- Flow cannot handle complex nested calculations easily
- Multiple conditions and formulas become unmanageable
- Integration with external financial systems required

**Why Apex is needed:**
```apex
public Decimal calculateTotalFees(Student__c student) {
    Decimal baseFees = getCourseFees(student);
    Decimal scholarship = getScholarshipPercent(student);
    Decimal lateFee = calculateLateFee(student);
    Decimal total = baseFees - (baseFees * scholarship/100) + lateFee;
    return total;
}
```

### Example 2: External Payment Gateway Integration

**Scenario:** When a student pays fees online, Salesforce must:
- Send payment data to external payment gateway (Razorpay/PayPal)
- Receive transaction status
- Update student record based on payment confirmation

**Why Flow is NOT enough:**
- Flows cannot make real-time web service callouts directly
- Need to handle API authentication and error responses
- Need to parse JSON/XML responses

**Why Apex is needed:**
```apex
@future(callout=true)
public static void sendPaymentToGateway(Id studentId, Decimal amount) {
    HttpRequest req = new HttpRequest();
    req.setEndpoint('https://api.paymentgateway.com/pay');
    req.setMethod('POST');
    req.setBody('{"studentId":"' + studentId + '","amount":' + amount + '}');
    HttpResponse res = new Http().send(req);
    // Process response
}
```

### Example 3: Advanced Eligibility Logic

**Scenario:** A student can register for a course only if:
- Prerequisite courses completed with minimum grade C
- No outstanding fees
- Maximum credits not exceeded
- Not already enrolled in same course
- Faculty approval for special cases

**Why Flow is NOT enough:**
- Too many conditions to evaluate
- Need to query multiple related objects
- Complex decision tree with exceptions
- Performance issues with large data

**Why Apex is needed:**
```apex
public Boolean checkEligibility(Id studentId, Id courseId) {
    // Check prerequisites
    List<Course_Completion__c> completed = [SELECT Grade__c FROM Course_Completion__c 
                                              WHERE Student__c = :studentId];
    // Check fees
    Decimal pendingFees = [SELECT SUM(Amount__c) FROM Fee__c 
                           WHERE Student__c = :studentId AND Paid__c = false];
    // Check credits
    Integer totalCredits = [SELECT SUM(Credits__c) FROM Enrollment__c 
                            WHERE Student__c = :studentId];
    
    if(pendingFees > 0) return false;
    if(totalCredits >= 20) return false;
    return true;
}
```

---

## 4. Integrated System Design - College Management System

### Complete System Overview

| Component | What it does | Example |
|-----------|--------------|---------|
| **CRM** | Manages student relationships and pipeline | Lead (inquiry) → Contact (applicant) → Opportunity (application) → Customer (enrolled) |
| **Objects** | Stores data | Student, Faculty, Course, Department, Enrollment, Fee |
| **Relationships** | Links objects together | Department → Course (One-to-Many), Student ⟷ Course (Many-to-Many via Enrollment) |
| **Validation** | Prevents bad data | Email required, Age > 0, Max seats limit |
| **Flow** | Automates processes | Auto email on registration, Update remaining seats |
| **Apex** | Handles complex logic | Fee calculation, External payment integration, Eligibility checking |

### Objects in College Management System

| Object | Type | Purpose |
|--------|------|---------|
| Student | Custom | Stores student info (name, email, phone, GPA, enrollment date) |
| Faculty | Custom | Stores teacher info (name, department, hire date, salary) |
| Course | Custom | Stores course details (name, credits, max seats, description) |
| Department | Custom | Stores department info (name, head, budget) |
| Enrollment | Custom (Junction) | Links Student and Course (enrollment date, grade, status) |
| Fee | Custom | Stores fee transactions (amount, due date, paid status) |

### Relationships

| Relationship | Type | Explanation |
|--------------|------|-------------|
| Department → Course | One-to-Many | One department offers many courses |
| Faculty → Course | One-to-Many | One faculty can teach many courses |
| Student → Enrollment | One-to-Many | One student can have many enrollments |
| Course → Enrollment | One-to-Many | One course can have many students |
| Student → Fee | One-to-Many | One student can have many fee transactions |

### Validation Rules

| Rule | Formula | Prevents |
|------|---------|----------|
| Email required | `ISBLANK(Email__c)` | Records without contact info |
| Age valid | `Age__c < 0 OR Age__c > 120` | Impossible ages |
| Seats limit | `Enrolled_Count__c > Max_Seats__c` | Overfilled courses |
| Fee positive | `Amount__c <= 0` | Negative or zero fees |

### Flow Automation

| Process | Trigger | Action |
|---------|---------|--------|
| Welcome email | After Student created | Send email with login credentials |
| Remaining seats update | After Enrollment created | Update Course.Remaining_Seats__c |
| Fee reminder | Daily scheduled | Email students before deadline |
| Course full notification | When seats = 0 | Notify faculty and students |

### Apex Use Cases

| Use Case | Why Apex | Description |
|----------|----------|-------------|
| Fee calculation | Complex logic | Calculate total fees with scholarships, late fees, installments |
| Payment integration | External callout | Connect to Razorpay/PayPal API |
| Eligibility check | Multiple queries | Check prerequisites, fees, credits, duplicates |
| Bulk enrollment | Large data | Process 1000+ enrollments efficiently |
| Grade calculation | Complex formula | Auto-calculate GPA based on grades and credits |

---

## 5. Pseudocode Examples

### Example 1: Check Seat Availability Before Registration

```
IF course has remaining seats > 0 THEN
    Allow student to register
    Decrease remaining seats by 1
    Send confirmation email to student
ELSE
    Show error: "Course is full"
    Add student to waitlist
    Notify faculty that course is full
END IF
```

**Apex Equivalent:**
```apex
if(course.Remaining_Seats__c > 0) {
    Enrollment__c enrollment = new Enrollment__c(Student__c = studentId, Course__c = courseId);
    insert enrollment;
    course.Remaining_Seats__c--;
    update course;
    sendEmail(studentId, 'Registration Confirmed');
} else {
    System.debug('Course is full');
}
```

### Example 2: Fee Deadline Reminder

```
FOR each student with pending fees:
    IF due date is within 3 days THEN
        Send reminder email
        IF student has already paid THEN
            Mark fee as paid
            Remove from reminder list
        END IF
    END IF
END FOR
```

### Example 3: Attendance Based Restriction

```
IF student attendance < 75% THEN
    Show warning message
    Send notification to parent
    IF student tries to take exam THEN
        Block exam access
        Require faculty approval
    END IF
ELSE
    Allow full access to course materials
    Allow exam access
END IF
```

### Example 4: Bulk Enrollment Processing

```
List of enrollmentRequests = getRequests()
List validEnrollments = empty list
List errors = empty list

FOR each request in enrollmentRequests:
    IF student is eligible THEN
        Add to validEnrollments
    ELSE
        Add error message to errors
    END IF
END FOR

IF validEnrollments is not empty THEN
    INSERT validEnrollments
    Send success report
END IF

IF errors is not empty THEN
    Send error report to admin
END IF
```

---

## 6. Reflection: Why Enterprise Systems Eventually Need Programming

### Why is Apex needed if Salesforce already has Flows?

| Problem with Flows only | Solution with Apex |
|------------------------|-------------------|
| Cannot make external web callouts | Apex can integrate with any REST/SOAP API |
| Limited complex calculations | Apex supports full programming logic |
| Cannot handle bulk data efficiently | Apex can process millions of records |
| No exception handling | Apex has try-catch blocks |
| Cannot chain complex processes | Apex supports queues and batch jobs |

### When should developers prefer no-code solutions?

- Simple approval processes
- Basic email alerts
- Field updates based on conditions
- Screen flows for data collection
- Quick prototypes and MVPs
- When business users need to maintain the process

### What problems require custom programming?

| Problem | Why Code is Required |
|---------|---------------------|
| External API integration | Need HTTP callouts, JSON parsing, authentication |
| Complex business rules | Need multiple conditions, loops, recursive logic |
| Large data processing | Need batch Apex for governor limits |
| Real-time synchronization | Need triggers and async processing |
| Custom algorithms | Need full programming language features |

### Why is business logic important in enterprise systems?

- Ensures consistent decision making across the organization
- Automates repetitive tasks to save time
- Prevents errors that cost money and reputation
- Enforces rules that protect the business
- Provides audit trail of decisions

### Why should developers avoid unnecessary coding?

- Code requires maintenance and testing
- No-code solutions are easier to change
- Flows can be modified by admins without developers
- Code has deployment complexity
- Simpler is always better when it meets requirements

### How does programming increase flexibility?

| Without Code (Configuration) | With Code (Apex) |
|------------------------------|------------------|
| Limited to what Salesforce provides | Can build anything from scratch |
| Cannot integrate with external systems | Can connect to any API |
| Fixed logic patterns | Unlimited logic possibilities |
| Slower for complex operations | Optimized custom algorithms |
| Cannot handle edge cases | Full exception handling |

### Final Summary: Why Enterprise Systems Eventually Need Programming

1. **Complexity grows** - Simple businesses become complex enterprises
2. **Integration requirements** - No system lives in isolation
3. **Unique business rules** - Every company has special cases
4. **Scale demands** - Millions of records need efficient processing
5. **Competitive advantage** - Custom code creates unique features
6. **External dependencies** - Payment gateways, CRMs, ERPs all need integration
7. **Regulatory compliance** - Custom validation for industry rules

> **Golden Rule:** Use no-code first. When you hit limitations, use low-code (Flow). When still not enough, use Apex. Always choose the simplest solution that works.

---

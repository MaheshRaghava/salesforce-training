## 1. Component Communication

### Why Components Need to Communicate

In a modular application, components are split into small pieces. These pieces need to talk to each other to work as one complete application.

### Three Types of Component Communication

| Type | Direction | Method | Use Case |
|------|-----------|--------|----------|
| **Child → Parent** | Upward | Custom Events | Child sends data to parent |
| **Parent → Child** | Downward | `@api` property | Parent passes data to child |
| **Unrelated Components** | Any | Lightning Message Service (LMS) | Components not connected in hierarchy |

---

### 1. Child → Parent Communication (Custom Events)

**Simple Example:** Counter app where child button controls parent counter.

**Child Component (controls.js):**
```javascript
handleAdd() {
    this.dispatchEvent(
        new CustomEvent('add')
    );
}
```

**Parent Component (numerator.html):**
```html
<c-controls onadd={handleIncrement}></c-controls>
```

**Parent Component (numerator.js):**
```javascript
handleIncrement() {
    this.counter++;
}
```

**Flow:**
```
Child Button Click → dispatchEvent('add') → Parent listens (onadd) → Parent runs handleIncrement()
```

**Key Rule:** Events go UP, data goes DOWN.

---

### 2. Parent → Child Communication (@api)

**Simple Example:** Parent passes product ID to child component.

**Child Component (detail.js):**
```javascript
import { LightningElement, api } from 'lwc';

export default class Detail extends LightningElement {
    @api productId;  // Public property
}
```

**Parent Component (selector.html):**
```html
<c-detail product-id={selectedProductId}></c-detail>
```

**Flow:**
```
Parent updates selectedProductId → Child receives via @api → Child displays details
```

---

### 3. Unrelated Components Communication (Lightning Message Service - LMS)

**When to use:** Components that are not parent-child (siblings, completely separate).

**Simple Analogy:** WhatsApp group for components → One sends message, others receive.

**Step 1 - Create Message Channel (Count_Updated.messageChannel-meta.xml):**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<LightningMessageChannel xmlns="http://soap.sforce.com/2006/04/metadata">
    <masterLabel>CountUpdated</masterLabel>
    <isExposed>true</isExposed>
    <lightningMessageFields>
        <fieldName>operator</fieldName>
        <fieldName>constant</fieldName>
    </lightningMessageFields>
</LightningMessageChannel>
```

**Step 2 - Sender Component (remoteControl.js):**
```javascript
import { publish, MessageContext } from 'lightning/messageService';
import COUNT_CHANNEL from '@salesforce/messageChannel/Count_Updated__c';

handleAdd() {
    const payload = { operator: 'add', constant: 1 };
    publish(this.messageContext, COUNT_CHANNEL, payload);
}
```

**Step 3 - Receiver Component (counts.js):**
```javascript
import { subscribe, MessageContext } from 'lightning/messageService';

handleMessage(message) {
    if(message.operator == 'add') {
        this.counter += message.constant;
    }
}
```

---

### Summary Table: When to Use What

| Scenario | Method |
|----------|--------|
| Button click affects parent | Custom Event |
| Parent sends data to child | `@api` property |
| Sibling components need to share data | Lightning Message Service |
| Components on different parts of page | Lightning Message Service |

---

## 2. Dashboard Design

### Student Dashboard Component Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Student Dashboard                       │
├─────────────────┬─────────────────┬─────────────────────────┤
│                 │                 │                         │
│  ┌───────────┐  │  ┌───────────┐  │  ┌───────────────────┐  │
│  │  Profile  │  │  │  Courses  │  │  │    Attendance     │  │
│  │   Card    │  │  │   List    │  │  │      Chart        │  │
│  └───────────┘  │  └───────────┘  │  └───────────────────┘  │
│                 │                 │                         │
│  ┌───────────┐  │  ┌───────────┐  │  ┌───────────────────┐  │
│  │  Grades   │  │  │  Upcoming │  │  │  Fee Status       │  │
│  │  Summary  │  │  │  Events   │  │  │  Card             │  │
│  └───────────┘  │  └───────────┘  │  └───────────────────┘  │
│                 │                 │                         │
└─────────────────┴─────────────────┴─────────────────────────┘
```

### Component Communication in Dashboard

| Component | Communicates With | Method | Purpose |
|-----------|-------------------|--------|---------|
| Profile Card | All components | LMS | Share student ID |
| Courses List | Attendance Chart | Custom Event | Update attendance when course changes |
| Fee Status | Profile Card | `@api` | Show payment status based on student ID |

### Faculty Dashboard Component Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Faculty Dashboard                       │
├─────────────────┬─────────────────┬─────────────────────────┤
│                 │                 │                         │
│  ┌───────────┐  │  ┌───────────┐  │  ┌───────────────────┐  │
│  │  My       │  │  │  Course   │  │  │  Student List     │  │
│  │  Courses  │  │  │  Details  │  │  │  (Enrolled)       │  │
│  └───────────┘  │  └───────────┘  │  └───────────────────┘  │
│                 │                 │                         │
│  ┌───────────┐  │  ┌───────────┐  │  ┌───────────────────┐  │
│  │  Mark     │  │  │  Schedule │  │  │  Pending          │  │
│  │  Attendance│  │  │           │  │  │  Approvals        │  │
│  └───────────┘  │  └───────────┘  │  └───────────────────┘  │
│                 │                 │                         │
└─────────────────┴─────────────────┴─────────────────────────┘
```

### Communication Flow in Faculty Dashboard

```
My Courses Component (Parent)
    ├── emits "courseSelected" event
    ↓
Course Details Component
    ↓ (receives via @api)
    ↓
Student List Component (updates)
```

---

## 3. Data Flow Explanation

### Complete Data Flow: Student Registration Process

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         STUDENT REGISTRATION FLOW                        │
└─────────────────────────────────────────────────────────────────────────┘

STEP 1: UI (LWC)
┌─────────────────────────────────────────────────────────────────────────┐
│  Student fills form → Name, Email, Course, Payment Details              │
│  Clicks "Register" button                                                │
│  LWC collects data → Validates format (email valid, fields not empty)    │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
STEP 2: Frontend Validation (LWC JavaScript)
┌─────────────────────────────────────────────────────────────────────────┐
│  if(!this.email.includes('@')) → Show error "Invalid email"             │
│  if(!this.name) → Show error "Name required"                            │
│  If validation fails → Stop here, show error message                    │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓ (if valid)
STEP 3: Call Apex Method (LWC → Apex)
┌─────────────────────────────────────────────────────────────────────────┐
│  import registerStudent from '@salesforce/apex/RegistrationController.registerStudent';│
│                                                                          │
│  registerStudent({ name: this.name, email: this.email, courseId: this.courseId })│
│      .then(result => { /* success */ })                                  │
│      .catch(error => { /* failure */ });                                 │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
STEP 4: Apex Business Logic (Backend Validation)
┌─────────────────────────────────────────────────────────────────────────┐
│  @AuraEnabled                                                           │
│  public static String registerStudent(String name, String email, Id courseId) {│
│                                                                          │
│      // Check if course has available seats                             │
│      Course__c course = [SELECT Seats_Available__c FROM Course WHERE Id = :courseId];│
│      if(course.Seats_Available__c <= 0) {                               │
│          return 'Course is full';                                       │
│      }                                                                  │
│                                                                          │
│      // Check if student already registered                             │
│      // Calculate fees                                                  │
│      // Apply discounts                                                 │
│  }                                                                      │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
STEP 5: Database Operation (SOQL/DML)
┌─────────────────────────────────────────────────────────────────────────┐
│  // Insert Student record                                               │
│  Student__c newStudent = new Student__c(Name__c = name, Email__c = email);│
│  insert newStudent;                                                     │
│                                                                          │
│  // Insert Enrollment record                                            │
│  Enrollment__c enrollment = new Enrollment__c(                          │
│      Student__c = newStudent.Id,                                        │
│      Course__c = courseId                                               │
│  );                                                                     │
│  insert enrollment;                                                     │
│                                                                          │
│  // Update course seats                                                 │
│  course.Seats_Available__c -= 1;                                        │
│  update course;                                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
STEP 6: Post-Processing (Automation)
┌─────────────────────────────────────────────────────────────────────────┐
│  // Flow or Trigger executes automatically                              │
│  • Send welcome email to student                                        │
│  • Notify faculty about new enrollment                                  │
│  • Generate student ID card                                             │
│  • Update dashboard widgets                                             │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
STEP 7: Notification Back to UI
┌─────────────────────────────────────────────────────────────────────────┐
│  Apex returns success message → LWC receives in .then()                 │
│  LWC shows toast: "Registration Successful! Welcome email sent"        │
│  LWC clears form → Refreshes course list → Updates remaining seats     │
└─────────────────────────────────────────────────────────────────────────┘
```

### Data Flow Diagram (Simple)

```
UI (LWC)
   │
   ├── Frontend Validation
   │
   ▼
Apex Controller
   │
   ├── Business Logic
   │
   ▼
Database (SOQL/DML)
   │
   ├── Insert/Update/Delete
   │
   ▼
Automation (Flow/Trigger)
   │
   ├── Email / Notifications
   │
   ▼
Response to UI
```

---

## 4. Aura vs LWC

### Comparison Table

| Aspect | Aura Components | Lightning Web Components (LWC) |
|--------|----------------|-------------------------------|
| **Status** | Older framework (legacy) | Modern framework (recommended) |
| **Based On** | Proprietary Salesforce framework | Web standards (HTML/CSS/JS) |
| **Performance** | Slower (more overhead) | Faster (native browser) |
| **Learning Curve** | Steeper (Salesforce-specific) | Easier (standard web dev) |
| **Code Volume** | More lines of code | Less code |
| **Debugging** | Harder (custom framework) | Easier (browser dev tools) |
| **Component Files** | .cmp, .css, .js, .design | .html, .js, .css, .js-meta.xml |
| **Data Binding** | Two-way (`{!v.value}`) | One-way (`{value}`) |
| **Communication** | Events, Application Events | Custom Events, @api, LMS |
| **Can Contain LWC** | ✅ Yes | N/A |
| **Can Contain Aura** | N/A | ❌ No |
| **Mobile Support** | Limited | Full |
| **Salesforce Recommendation** | Maintenance only | New development |

### Syntax Comparison

| Feature | Aura | LWC |
|---------|------|-----|
| Component Tag | `<c:myComponent />` | `<c-my-component></c-my-component>` |
| Expression | `{!v.message}` | `{message}` |
| Attribute | `<aura:attribute name="value" type="String"/>` | `@api value;` |
| Handler | `<aura:handler name="init" action="{!c.doInit}"/>` | `connectedCallback() { }` |
| Iteration | `<aura:iteration items="{!v.items}" var="item">` | `<template for:each={items} for:item="item">` |
| Condition | `<aura:if isTrue="{!v.condition}">` | `<template lwc:if={condition}>` |
| Event | `component.getEvent("myEvent").fire();` | `this.dispatchEvent(new CustomEvent('myevent'));` |

### Aura Can Contain LWC (Important!)

```html
<!-- Aura Component -->
<aura:component>
    <c:myAuraComponent>
        <c-my-lwc-component></c-my-lwc-component>
    </c:myAuraComponent>
</aura:component>
```

### LWC CANNOT Contain Aura

```html
<!-- This is NOT allowed -->
<c-my-lwc>
    <c:myAura>  ❌ NOT ALLOWED
</c-my-lwc>
```

### Why Salesforce Moved from Aura to LWC

| Problem with Aura | Solution in LWC |
|------------------|-----------------|
| Proprietary framework | Uses standard web technologies |
| Slower performance | Native browser execution |
| Harder to find developers | Anyone with web dev skills can learn |
| Complex debugging | Browser DevTools work directly |
| More code required | Cleaner, simpler syntax |
| Limited mobile support | Built for mobile-first |

---

## 5. Reflection

### Why Enterprise Applications Need Modular Architecture

| Reason | Explanation |
|--------|-------------|
| **Reusability** | Same component (like "tile") can be used in 10 different places |
| **Maintainability** | Fix one component → All instances fixed automatically |
| **Team Collaboration** | 5 teams can work on 5 different components simultaneously |
| **Testing** | Test each component individually → Easier to find bugs |
| **Scalability** | Add new features by composing existing components |
| **Separation of Concerns** | Each component does ONE thing well |
| **Faster Development** | Reuse existing components instead of rebuilding |
| **Easier Debugging** | Isolate problem to specific component |

### Key Learnings from Day 9

1. **Components communicate in three ways:**
   - Child → Parent (Custom Events)
   - Parent → Child (`@api`)
   - Unrelated components (Lightning Message Service)

2. **Data flows in one direction** (down) in LWC
   - Events go UP
   - Properties go DOWN

3. **Aura is old, LWC is new**
   - Salesforce recommends LWC for all new development
   - Aura maintenance only

4. **Visualforce is even older**
   - Legacy technology
   - Still exists in many companies
   - Important to understand for interviews

5. **Modular architecture is essential for enterprise**
   - Without it, systems become spaghetti code
   - Hard to debug, hard to scale, hard to maintain

---

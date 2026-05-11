# Day 3: Data Modeling

## 1. Difference Between: App, Object, Record, Field

### Simple Explanation

- **App** = Complete software section
- **Object** = Table
- **Record** = One row
- **Field** = One column

| Term | Definition | Example |
|------|------------|---------|
| **App** | A set of objects, fields, and functionality that support a specific business function | Dreamhouse app for real estate, College Admission Tracker app |
| **Object** | A database table that stores information | Account, Contact, Property, Student |
| **Record** | A single row of data inside an object | Google (Account record), John Doe (Contact record) |
| **Field** | A column that stores a specific type of information in an object | Name, Price, Email, Phone Number |

---

## 2. Standard vs Custom Objects

| Standard Objects | Custom Objects |
|-----------------|----------------|
| Included with Salesforce by default | Created by you for your specific needs |
| Examples: Account, Contact, Lead, Opportunity | Examples: Property, Offer, Favorite, Student |
| Common business objects that most companies need | Store information unique to your company or industry |
| Cannot be deleted | Can be deleted |
| Come with prebuilt fields and page layouts | You build fields and layouts from scratch |

**Example:** Dreamhouse uses standard **Contact** object for buyers, but creates a custom **Property** object to track homes for sale.

---

## 3. My College Data Model

### Objects:

| Object | Type | Purpose |
|--------|------|---------|
| **Student** | Custom | Stores student information (name, email, phone, GPA) |
| **Faculty** | Custom | Stores teacher/professor information |
| **Course** | Custom | Stores course details (name, credits, max seats) |
| **Department** | Custom | Stores department info (Engineering, Business, etc.) |
| **Enrollment** | Custom (Junction) | Links Student and Course (tracks which student is in which course) |

### Relationships:

| Relationship | Type | Explanation |
|--------------|------|-------------|
| Department → Course | One-to-Many | One department offers many courses |
| Faculty → Course | One-to-Many | One faculty can teach many courses |
| Student → Enrollment | One-to-Many | One student can have many enrollments |
| Course → Enrollment | One-to-Many | One course can have many students enrolled |
| Student ⟷ Course | Many-to-Many | Students can take many courses, courses can have many students (using Enrollment as a junction object) |

### Diagram:

┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Department  │────<│    Course    │>────│  Enrollment  │
└──────────────┘     └──────────────┘     └──────────────┘
         │                   │                   │
         │                   │                   │
         ▼                   ▼                   ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│              │     │   Faculty    │     │   Student    │
└──────────────┘     └──────────────┘     └──────────────┘


## 4. Formula Fields

Formula fields automatically calculate values using fields within a single record. No user input needed.

### My Example (College System):

| Formula Field | Formula (Concept) | Why Automatically Calculate? |
|---------------|-------------------|------------------------------|
| **Full Name** | First Name + " " + Last Name | Prevents typing same name twice; ensures consistency |
| **Remaining Seats** | Max Seats - Enrolled Students | Real-time visibility; prevents manual counting |
| **Percentage Complete** | (Completed Credits / Total Credits) × 100 | Student can see progress instantly; no manual math |

### Explanation:

These should be calculated automatically because:
- **Reduces human error** - No typos or wrong calculations
- **Saves time** - No manual updating every time something changes
- **Always accurate** - Real-time data without extra work
- **Better user experience** - Students and admins see correct info instantly

---

## 5. Validation Rules

Validation rules prevent users from saving invalid data. They check data before saving and show an error message if something is wrong.

### My Example (College System):

| Validation Rule | Formula Concept | What Bad Data Does It Prevent? |
|----------------|-----------------|-------------------------------|
| **Email Cannot Be Empty** | ISBLANK(Email) = TRUE | Prevents student records without contact info |
| **Age Cannot Be Negative** | Age < 0 | Prevents impossible ages (someone can't be -5 years old) |
| **Course Seats Cannot Exceed Limit** | Enrolled_Students__c > Max_Seats__c | Prevents overfilling classrooms beyond capacity |

### Additional Example (from Salesforce):

**Account Number must be 8 characters:**

**Error Message:** "Account number must be 8 characters long."

### Why Validation Rules Matter:

- Block bad data **before** it enters the system
- Save time cleaning up errors later
- Ensure everyone follows the same rules
- Protect business processes from invalid information

---

## 6. Reflection: Why Structured Enterprise Data Matters

Companies cannot manage everything using Excel sheets because spreadsheets have major problems:

### Problems with Random Spreadsheets:

| Problem | Example |
|---------|---------|
| **Duplicate data** | Same customer entered 5 different ways ("John Smith", "J. Smith", "John S.") |
| **No data consistency** | One person types "CA", another types "California", another types "cali" |
| **No relationships** | Can't easily see which student is enrolled in which course |
| **Version confusion** | Someone saves "final_v2_FINAL_real.xlsx" and nobody knows which is correct |
| **No access control** | Anyone with the file can delete or change everything |
| **No validation** | Someone types "abc" in a phone number field and Excel accepts it |

### Why Structured Data in Salesforce is Better:

1. **Relationships** – Objects can be linked (Account → Contact → Opportunity)
2. **Consistency** – Picklists, validation rules, and field types enforce standards
3. **Single source of truth** – Everyone sees the same real-time data
4. **Security** – Profiles and permissions control who sees what
5. **Automation** – Formulas and validation rules work automatically
6. **Scalability** – Handle millions of records without crashing

---

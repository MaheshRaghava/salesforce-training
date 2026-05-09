# Day 2: Platform Basics

## 1. What is Salesforce Platform?

Salesforce Platform is a cloud-based application development platform used to build business applications quickly and easily.

It helps companies:
- Store customer data
- Automate business processes
- Build custom applications
- Create reports and dashboards
- Connect different departments

Salesforce provides:
- No-code tools
- Low-code tools
- Coding support using Apex and Lightning Web Components

The platform works on desktop and mobile devices.

## 2. Explain: App, Object, Tab

**App:** An app is a set of objects, fields, and other functionality (like flows or analytics) that support a specific business function. For example, the Dreamhouse app helps real estate brokers manage properties.

**Object:** An object is a database table that stores information. Standard objects come with Salesforce (like Account, Contact), and you can also create custom objects. For example, in Dreamhouse, "Property" and "Broker" are custom objects.

**Tab:** A tab is how you view and interact with an object's data in the Salesforce user interface. For example, the "Properties" tab shows a list of property records. Tabs make it easy to navigate between different objects.

## 3. Difference: Configuration vs Coding

| Configuration (No Code / Low Code) | Coding (Apex, LWC, etc.) |
|-----------------------------------|---------------------------|
| No programming language required | Requires writing code |
| Done using point-and-click tools like Lightning App Builder, Flow Builder | Done using Apex, JavaScript (LWC), or other languages |
| Faster to build and deploy | Takes more time |
| Best for simple business processes, automation, UI changes | Best for complex logic, integrations, custom behavior |
| Can be done by Salesforce Admins | Requires Salesforce Developers |

**Examples of Configuration:**
- Creating a custom object and fields using point-and-click
- Building a screen flow to capture new property details (like Dreamhouse)

**Examples of Coding:**
- Writing Apex to add business logic when a button is clicked
- Building a custom Lightning Web Component (like the map component in Dreamhouse)

## 4. My System Design (College Admission System)

**App Name:** College Admission Tracker

**Objects Inside:**
- **Student** (custom object) - stores student information like name, email, phone, GPA
- **Application** (custom object) - stores application details like major, submission date, status
- **Program** (custom object) - stores degree programs offered (e.g., Engineering, Business)

**User Interaction:**
- Admission officers open the **College Admission Tracker** app from the App Launcher
- They use **tabs** (Student tab, Application tab, Program tab) to view and manage records
- When a new student applies, the officer creates a Student record and an Application record linked to it
- The officer updates the Application status (Submitted → Review → Accepted → Enrolled)
- Reports and dashboards show how many applications are pending, accepted, or enrolled

# Day 8 - Lightning Web Components (LWC) Basics

## 1. What is LWC?

Lightning Web Components (LWC) is a modern framework from Salesforce for building user interfaces (UI) in Salesforce.

LWC uses:
- **HTML** → Structure of the component
- **JavaScript** → Logic and data
- **CSS** → Styling and design

LWC is built on **web standards**, meaning if you already know normal web development, learning LWC is easier.

### Example Component Structure:

| File | Purpose |
|------|---------|
| `component.html` | UI template |
| `component.js` | JavaScript logic |
| `component.css` | Styling (optional) |
| `component.js-meta.xml` | Configuration (makes component visible in App Builder) |

---

## 2. Why Salesforce Uses LWC

### Problems with Old Framework (Aura):

| Problem | Explanation |
|---------|-------------|
| Proprietary syntax | Harder to learn, not standard web |
| Slower performance | More framework overhead |
| More complex | More files, more code |
| Harder to debug | Less familiar to web developers |

### Benefits of LWC:

| Benefit | Explanation |
|---------|-------------|
| **Faster Performance** | Runs directly in browser, less overhead |
| **Easier Development** | Uses standard HTML, CSS, JavaScript |
| **Reusable Components** | Build once, use anywhere |
| **Modern JavaScript (ES6+)** | Classes, imports, promises |
| **Easy to Find Help** | Since it's standard web, Google/StackOverflow works |
| **Works with Aura** | Aura can contain LWC (but not other way) |

### Key Salesforce Decision:

> Salesforce moved to LWC because modern browsers now support web standards that didn't exist when Aura was created.

---

## 3. Your UI Screens

### Bike Card Component

This component displays a bike's details including:
- Name
- Description
- Category (as badge)
- Material (as badge)
- Price
- Image

### Screenshot:

-

```
┌─────────────────────────────────┐
│  Name: Electra X4               │
│  Description: A sweet bike...   │
│  [Steel] [Mountain]             │
│  Price: $2,700                  │
│                                 │
│  ┌─────────────────────────┐    │
│  │                         │    │
│  │    [Bike Image]         │    │
│  │                         │    │
│  └─────────────────────────┘    │
└─────────────────────────────────┘
```

### Bike Selector App

The Bike Selector App has multiple components working together:

```
┌─────────────────────────────────────────────────────┐
│  Available Bikes for Mahesh                         │
├─────────────────────────┬───────────────────────────┤
│  ┌─────┐ ┌─────┐ ┌─────┐ │  ┌─────────────────────┐  │
│  │Bike1│ │Bike2│ │Bike3│ │  │  Selected Bike      │  │
│  │Image│ │Image│ │Image│ │  │  Details:           │  │
│  │Name │ │Name │ │Name │ │  │  - Full Description │  │
│  └─────┘ └─────┘ └─────┘ │  │  - Price            │  │
│                           │  │  - Category         │  │
│  (Click any bike)         │  │  - Material         │  │
│                           │  │  - Large Image      │  │
│                           │  └─────────────────────┘  │
└─────────────────────────┴───────────────────────────┘
```

### Final App Page:

*Lightning App Page*

---

## 4. Component Breakdown

### Bike Selector App Component Hierarchy:

```
selector (Parent)
    ├── list (Child)
    │       └── tile (Grandchild - repeats for each bike)
    └── detail (Child)
```

### Each Component's Responsibility:

| Component | File | Responsibility |
|-----------|------|----------------|
| **tile** | `tile.html`, `tile.js` | Displays one bike image + name, sends event when clicked |
| **list** | `list.html`, `list.js` | Shows multiple tiles, listens to tile events |
| **detail** | `detail.html`, `detail.js` | Shows full bike details, receives selected bike ID |
| **selector** | `selector.html`, `selector.js` | Parent component, controls everything, shows user name |

### Component Communication Pattern:

```
Events go UP, Properties go DOWN

    tile ──(event)──> list ──(event)──> selector
                                              │
                                              │ (@api property)
                                              ▼
                                          detail
```

### Key Files Explained:

#### tile.js (Child sends event)
```javascript
handleClick() {
    const event = new CustomEvent('tileclick', {
        detail: this.product.fields.Id.value
    });
    this.dispatchEvent(event);  // Event goes UP
}
```

#### selector.html (Parent receives and passes down)
```html
<c-list onproductselected={handleProductSelected}></c-list>
<c-detail product-id={selectedProductId}></c-detail>
```

#### selector.js (Receives and updates)
```javascript
handleProductSelected(evt) {
    this.selectedProductId = evt.detail;  // Receives event
}
```

---

## 5. Frontend vs Backend Logic

| Aspect | Frontend (LWC) | Backend (Apex) |
|--------|----------------|----------------|
| **Where it runs** | Browser (user's computer) | Salesforce server |
| **Language** | JavaScript, HTML, CSS | Apex (Java-like) |
| **Purpose** | UI, user interaction, display | Data processing, business logic, database |
| **Access to data** | Uses @wire or Apex calls | Direct database access (SOQL, DML) |
| **Performance** | Fast for UI changes | Depends on server/database |
| **Security** | Limited (visible to user) | Server-side (hidden, more secure) |

### Examples in Bike App:

| Task | Frontend (LWC) | Backend (Apex) |
|------|----------------|----------------|
| Show bike name | ✅ Yes (`{name}` in HTML) | ❌ No |
| Handle button click | ✅ Yes (`onclick={method}`) | ❌ No |
| Fetch bike list from database | ❌ No (uses @wire) | ✅ Yes (Apex with SOQL) |
| Calculate complex fees | ❌ No | ✅ Yes |
| Update record in database | ❌ No (uses createRecord/updateRecord) | ✅ Yes |
| Apply validation rules | ❌ No (checks on save) | ✅ Yes |

### Simple Rule:

> **Frontend** = What user sees and clicks
> **Backend** = What happens behind the scenes

---

## 6. Reflection

### Why Modern Enterprise Systems Use Component-Based UI Architecture

| Reason | Explanation |
|--------|-------------|
| **Reusability** | Same component (like `tile`) can be used many times without rewriting code |
| **Maintainability** | Fix one component, all instances get fixed |
| **Team Collaboration** | Different teams can work on different components simultaneously |
| **Consistency** | UI looks same everywhere because same components are reused |
| **Testing** | Test each component individually, easier to find bugs |
| **Scalability** | Add new features by composing existing components, not rewriting everything |

### Key Insights from Learning LWC:

1. **Events up, properties down** is the golden rule of LWC communication
2. **LWC is faster than Aura** because it uses native browser features
3. **Separation of concerns** - HTML for structure, JS for logic, CSS for styling
4. **Web standards matter** - LWC is easier to learn for anyone with web development experience
5. **Security is built-in** but developers must still follow best practices (with sharing, CRUD checks, etc.)

### What I Learned Today:

- ✅ LWC uses standard HTML/CSS/JavaScript
- ✅ Components communicate using events (up) and properties (down)
- ✅ Each component has a single responsibility
- ✅ Salesforce provides Base Components for common UI elements
- ✅ LWC can be deployed using VS Code and Salesforce CLI
- ✅ Apex handles backend logic while LWC handles frontend UI

---

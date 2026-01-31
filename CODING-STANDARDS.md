# CODING-STANDARDS.md - Team Development Standards

**All dev agents (Morgan, Tracy, Simon) MUST follow these standards. No exceptions.**

## 📁 File Organization

### Frontend Projects
```
project/
├── public/
│   ├── index.html          # HTML only - no inline styles/scripts
│   ├── css/
│   │   ├── styles.css      # Main stylesheet
│   │   └── components/     # Component-specific CSS
│   ├── js/
│   │   ├── app.js          # Main application logic
│   │   ├── api.js          # API calls
│   │   └── components/     # Component-specific JS
│   └── assets/
│       ├── images/
│       └── fonts/
├── server.js
└── package.json
```

### ⛔ NEVER DO THIS
```html
<!-- WRONG - Inline CSS in header -->
<style>
  .card { padding: 20px; }
  .btn { background: purple; }
</style>

<!-- WRONG - Inline styles on elements -->
<div style="padding: 20px; margin: 10px;">

<!-- WRONG - Inline JavaScript -->
<script>
  function doThing() { ... }
</script>
```

### ✅ ALWAYS DO THIS
```html
<!-- RIGHT - External stylesheet -->
<link rel="stylesheet" href="/css/styles.css">

<!-- RIGHT - External scripts at end of body -->
<script src="/js/app.js"></script>
```

## 🎨 CSS Standards

### Structure
```css
/* ==========================================================================
   Component: Cards
   ========================================================================== */

.card {
  /* Box model */
  display: flex;
  padding: 1.5rem;
  margin-bottom: 1rem;
  
  /* Visual */
  background: var(--color-surface);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-sm);
  
  /* Typography */
  font-size: 0.875rem;
  
  /* Misc */
  transition: transform 0.2s ease;
}

.card:hover {
  transform: translateY(-2px);
}
```

### CSS Variables (Design Tokens)
```css
:root {
  /* Colors */
  --color-primary: #635bff;
  --color-primary-dark: #4f46e5;
  --color-surface: #ffffff;
  --color-background: #f6f9fc;
  --color-text: #1a1f36;
  --color-text-muted: #697386;
  
  /* Status */
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-error: #ef4444;
  --color-info: #3b82f6;
  
  /* Spacing */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;
  
  /* Border radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  
  /* Shadows */
  --shadow-sm: 0 1px 3px rgba(0,0,0,0.1);
  --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
  --shadow-lg: 0 10px 15px rgba(0,0,0,0.1);
}
```

## 📜 JavaScript Standards

### Module Pattern
```javascript
// api.js - Single responsibility
const API = {
  baseUrl: '/api',
  
  async get(endpoint) {
    const res = await fetch(`${this.baseUrl}${endpoint}`);
    if (!res.ok) throw new Error(`API error: ${res.status}`);
    return res.json();
  },
  
  async post(endpoint, data) {
    const res = await fetch(`${this.baseUrl}${endpoint}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });
    if (!res.ok) throw new Error(`API error: ${res.status}`);
    return res.json();
  }
};
```

### DOM Manipulation
```javascript
// app.js
document.addEventListener('DOMContentLoaded', () => {
  App.init();
});

const App = {
  init() {
    this.bindEvents();
    this.loadData();
  },
  
  bindEvents() {
    document.getElementById('btn-refresh')
      ?.addEventListener('click', () => this.refresh());
  },
  
  async loadData() {
    try {
      const data = await API.get('/tasks');
      this.render(data);
    } catch (err) {
      this.showError(err.message);
    }
  },
  
  render(data) {
    // ...
  }
};
```

## 🏗️ HTML Standards

### Semantic Structure
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Page Title</title>
  <link rel="stylesheet" href="/css/styles.css">
</head>
<body>
  <header>
    <nav><!-- Navigation --></nav>
  </header>
  
  <main>
    <section><!-- Content sections --></section>
  </main>
  
  <footer><!-- Footer --></footer>
  
  <!-- Scripts at end of body -->
  <script src="/js/api.js"></script>
  <script src="/js/app.js"></script>
</body>
</html>
```

## 🧪 Code Quality Checklist

Before submitting code, verify:

- [ ] No inline CSS (all styles in .css files)
- [ ] No inline JavaScript (all scripts in .js files)
- [ ] CSS uses variables for colors/spacing/etc
- [ ] JavaScript uses strict mode
- [ ] Error handling on all async operations
- [ ] Semantic HTML elements used appropriately
- [ ] Mobile-responsive (test at 375px width)
- [ ] Console has no errors
- [ ] Code is commented where logic is non-obvious

## 🎯 Stripe-Inspired Design System

Our default aesthetic (unless specified otherwise):

- **Colors**: Purple primary (#635bff), navy text, light gray backgrounds
- **Typography**: System font stack, 14px base
- **Spacing**: 8px grid system
- **Cards**: White with subtle shadow, 8px radius
- **Buttons**: Solid primary, ghost secondary
- **Forms**: Floating labels or top-aligned labels

## 📝 Git Commit Standards

```
feat: Add task filtering by status
fix: Resolve SSE connection timeout
refactor: Extract API calls to separate module
style: Move inline CSS to stylesheet
docs: Update README with setup instructions
```

---

**This document is LAW. QA agents will reject code that violates these standards.**

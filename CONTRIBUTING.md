# Contributing Guide

**Author:** VasilMihovCom

Guide for developers contributing to the Financial Education Portal.

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Development Setup](#development-setup)
3. [Code Style Guide](#code-style-guide)
4. [Git Workflow](#git-workflow)
5. [Testing Guidelines](#testing-guidelines)
6. [Pull Request Process](#pull-request-process)
7. [Documentation](#documentation)
8. [Code Review](#code-review)

---

## Getting Started

Thank you for considering contributing to the Financial Education Portal! This guide will help you get started.

### Ways to Contribute

- **Bug Reports** - Report issues
- **Feature Requests** - Suggest new features
- **Code Contributions** - Submit pull requests
- **Documentation** - Improve docs
- **Testing** - Test features and report issues

### Before Contributing

1. Read this guide completely
2. Check existing issues and PRs
3. Discuss major changes first
4. Follow code style guidelines
5. Test your changes thoroughly

---

## Development Setup

### Prerequisites

- Node.js 18+ and npm
- Git
- Code editor (VS Code recommended)
- Supabase account (free tier)

### Initial Setup

**1. Fork and Clone:**

```bash
# Fork the repository on GitHub
# Then clone your fork
git clone https://github.com/YOUR_USERNAME/financial-education-portal.git
cd financial-education-portal
```

**2. Install Dependencies:**

```bash
npm install
```

**3. Configure Environment:**

```bash
# Create .env file
cp .env.example .env

# Add your Supabase credentials
nano .env
```

**4. Setup Database:**

- Create Supabase project
- Run migrations in order
- Create admin user

**5. Start Development Server:**

```bash
npm run dev
```

Visit `http://localhost:5173`

### Development Tools

**Recommended VS Code Extensions:**

- ESLint
- Prettier
- TypeScript
- Tailwind CSS IntelliSense
- GitLens
- Error Lens

**VS Code Settings:**

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib"
}
```

---

## Code Style Guide

### TypeScript

**Naming Conventions:**

```typescript
// PascalCase for types, interfaces, components
interface UserProfile { }
type VisitorType = 'human' | 'bot';
function MyComponent() { }

// camelCase for variables, functions
const userName = 'John';
function getUserProfile() { }

// UPPER_SNAKE_CASE for constants
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;
```

**Type Annotations:**

```typescript
// Explicit return types for functions
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Interface over type for objects
interface User {
  id: string;
  email: string;
  name: string;
}

// Type for unions, intersections
type Status = 'active' | 'inactive' | 'pending';
```

**Best Practices:**

```typescript
// Use const for immutable values
const apiUrl = import.meta.env.VITE_SUPABASE_URL;

// Use let for mutable values (avoid var)
let counter = 0;

// Prefer async/await over promises
async function fetchData() {
  try {
    const data = await api.get('/data');
    return data;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// Use optional chaining
const username = user?.profile?.name;

// Use nullish coalescing
const displayName = name ?? 'Anonymous';
```

### React

**Component Structure:**

```typescript
import { useState, useEffect } from 'react';
import { supabase } from '../lib/supabase';
import type { Article } from '../types';

// Props interface
interface ArticleListProps {
  categoryId?: string;
  limit?: number;
}

// Component
export function ArticleList({ categoryId, limit = 10 }: ArticleListProps) {
  // State
  const [articles, setArticles] = useState<Article[]>([]);
  const [loading, setLoading] = useState(true);

  // Effects
  useEffect(() => {
    fetchArticles();
  }, [categoryId]);

  // Handlers
  async function fetchArticles() {
    // Implementation
  }

  // Render
  if (loading) {
    return <div>Loading...</div>;
  }

  return (
    <div>
      {articles.map(article => (
        <ArticleCard key={article.id} article={article} />
      ))}
    </div>
  );
}
```

**Hooks Usage:**

```typescript
// Custom hooks start with 'use'
function useArticles(categoryId: string) {
  const [articles, setArticles] = useState<Article[]>([]);

  useEffect(() => {
    // Fetch logic
  }, [categoryId]);

  return articles;
}

// Use hooks at top level
function MyComponent() {
  const articles = useArticles('123');
  const [count, setCount] = useState(0);

  // Don't use hooks in conditions or loops
}
```

### CSS/Tailwind

**Tailwind Guidelines:**

```typescript
// Group related classes
<div className="
  flex items-center justify-between
  px-4 py-2
  bg-white rounded-lg shadow
  hover:shadow-lg transition
">

// Use custom colors from config
<div className="bg-educational-primary text-white">

// Responsive design
<div className="
  grid
  grid-cols-1 md:grid-cols-2 lg:grid-cols-3
  gap-4
">
```

**Avoid Inline Styles:**

```typescript
// ❌ Bad
<div style={{ color: 'red', fontSize: '16px' }}>

// ✅ Good
<div className="text-red-500 text-base">
```

### File Organization

**File Naming:**

```
components/
├── MyComponent.tsx     # PascalCase for components
├── useMyHook.ts        # camelCase with 'use' prefix for hooks
└── myUtility.ts        # camelCase for utilities

utils/
├── analytics.ts
├── validation.ts
└── formatters.ts
```

**Import Order:**

```typescript
// 1. React and third-party
import { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';

// 2. Internal imports
import { supabase } from '../lib/supabase';
import { Article } from '../types';
import { formatDate } from '../utils/formatters';

// 3. Components
import { Header } from '../components/Header';
import { Footer } from '../components/Footer';

// 4. Styles (if any)
import './styles.css';
```

---

## Git Workflow

### Branch Naming

```bash
# Feature branches
feature/add-user-dashboard
feature/implement-analytics

# Bug fix branches
fix/login-error
fix/mobile-navigation

# Refactor branches
refactor/database-queries
refactor/component-structure

# Documentation branches
docs/api-reference
docs/update-readme
```

### Commit Messages

**Format:**

```
<type>: <subject>

<body (optional)>

<footer (optional)>
```

**Types:**

- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `style:` Code style (formatting, etc.)
- `refactor:` Code refactoring
- `test:` Adding tests
- `chore:` Maintenance tasks

**Examples:**

```bash
git commit -m "feat: add visitor detection system"

git commit -m "fix: resolve promotion display issue on mobile"

git commit -m "docs: update installation guide with Digital Ocean steps"

git commit -m "refactor: simplify database query logic"
```

**Good Commit Messages:**

```
✅ feat: add email notification system
✅ fix: resolve timezone issue in analytics
✅ docs: add API reference documentation
✅ refactor: extract validation logic to utility
```

**Bad Commit Messages:**

```
❌ update stuff
❌ fixed bug
❌ changes
❌ work in progress
```

### Workflow

**1. Create Branch:**

```bash
git checkout -b feature/my-feature
```

**2. Make Changes:**

```bash
# Edit files
# Test changes
npm run typecheck
npm run lint
npm run build
```

**3. Commit Changes:**

```bash
git add .
git commit -m "feat: add my feature"
```

**4. Push to Fork:**

```bash
git push origin feature/my-feature
```

**5. Create Pull Request:**

- Go to GitHub
- Click "New Pull Request"
- Fill in template
- Submit for review

---

## Testing Guidelines

### Manual Testing

**Before Submitting PR:**

- [ ] Test feature in browser
- [ ] Test on mobile (Chrome DevTools)
- [ ] Test in different browsers
- [ ] Verify no console errors
- [ ] Test edge cases
- [ ] Verify accessibility (keyboard nav, screen reader)

### Type Checking

```bash
# Run TypeScript compiler
npm run typecheck

# Should show no errors
```

### Linting

```bash
# Run ESLint
npm run lint

# Auto-fix issues
npm run lint -- --fix
```

### Build Testing

```bash
# Test production build
npm run build

# Preview build
npm run preview
# Visit http://localhost:4173
```

---

## Pull Request Process

### Before Submitting

1. **Update from main:**
   ```bash
   git checkout main
   git pull upstream main
   git checkout feature/my-feature
   git rebase main
   ```

2. **Run checks:**
   ```bash
   npm run typecheck
   npm run lint
   npm run build
   ```

3. **Test thoroughly**

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Code refactoring

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
- [ ] Tested locally
- [ ] TypeScript passes
- [ ] Linter passes
- [ ] Build succeeds
- [ ] Tested on mobile

## Screenshots (if applicable)
Add screenshots

## Related Issues
Fixes #123
```

### Review Process

1. **Automated Checks** - Must pass
2. **Code Review** - By maintainer
3. **Testing** - Verify changes work
4. **Approval** - Maintainer approves
5. **Merge** - Squash and merge

---

## Documentation

### Code Documentation

**Comments:**

```typescript
// Explain WHY, not WHAT
// ❌ Bad: Increment counter
counter++;

// ✅ Good: Track page views for analytics
counter++;

// Document complex logic
/**
 * Calculates visitor authenticity score based on multiple signals.
 *
 * @param signals - Detection signals from client and server
 * @returns Score from 0-100, higher means more likely human
 */
function calculateAuthenticityScore(signals: DetectionSignals): number {
  // Implementation
}
```

**README Updates:**

Update README.md when adding:
- New features
- New dependencies
- New environment variables
- New setup steps

---

## Code Review

### As a Contributor

**Respond to Feedback:**

- Be open to suggestions
- Ask questions if unclear
- Make requested changes promptly
- Be respectful and professional

**Making Changes:**

```bash
# After review feedback
git add .
git commit -m "refactor: address review comments"
git push origin feature/my-feature
```

### As a Reviewer

**What to Check:**

- Code follows style guide
- Changes are well-tested
- No obvious bugs
- Documentation updated
- Commit messages clear
- No security issues

**Providing Feedback:**

- Be constructive and kind
- Explain reasoning
- Suggest improvements
- Approve when ready

---

## Additional Guidelines

### Security

- Never commit secrets
- Validate all inputs
- Follow security best practices
- Report vulnerabilities privately

### Performance

- Optimize images
- Minimize bundle size
- Avoid unnecessary re-renders
- Use lazy loading where appropriate

### Accessibility

- Semantic HTML
- ARIA labels where needed
- Keyboard navigation
- Sufficient color contrast

---

## Getting Help

**Questions?**

- Check documentation first
- Search existing issues
- Ask in discussions
- Contact maintainers

**Resources:**

- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)
- [Supabase Docs](https://supabase.com/docs)

---

## Recognition

Contributors will be:

- Listed in CHANGELOG.md
- Mentioned in release notes
- Added to contributors list

---

**Thank you for contributing!**

**Contributing Guide by VasilMihovCom**

*Building better software together*

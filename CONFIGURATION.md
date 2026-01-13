# Configuration Guide

**Author:** VasilMihovCom

Complete configuration reference for the Financial Education Portal.

---

## Table of Contents

1. [Environment Variables](#environment-variables)
2. [Supabase Configuration](#supabase-configuration)
3. [Build Configuration](#build-configuration)
4. [Tailwind CSS Configuration](#tailwind-css-configuration)
5. [TypeScript Configuration](#typescript-configuration)
6. [ESLint Configuration](#eslint-configuration)
7. [Vite Configuration](#vite-configuration)
8. [Detection Settings](#detection-settings)
9. [Analytics Configuration](#analytics-configuration)
10. [Production Settings](#production-settings)

---

## Environment Variables

### Required Variables

Create a `.env` file in the project root:

```env
# Supabase Configuration
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your_anon_key_here
```

### Variable Reference

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `VITE_SUPABASE_URL` | Yes | Supabase project URL | `https://xxxxx.supabase.co` |
| `VITE_SUPABASE_ANON_KEY` | Yes | Supabase anonymous/public key | `eyJhbGci...` |

### Getting Supabase Credentials

1. Go to [supabase.com](https://supabase.com) and sign in
2. Select your project
3. Navigate to **Settings** → **API**
4. Copy:
   - **Project URL** → `VITE_SUPABASE_URL`
   - **anon/public key** → `VITE_SUPABASE_ANON_KEY`

### Environment File Template

Create `.env.example` for documentation:

```env
# Supabase Configuration
# Get these from https://supabase.com project settings
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your_anon_key_here
```

### Security Best Practices

- **Never commit `.env` to version control**
- Add `.env` to `.gitignore`
- Use different credentials for dev/staging/production
- Rotate keys periodically
- Store production keys securely (e.g., CI/CD secrets)

---

## Supabase Configuration

### Project Setup

1. Create project at [supabase.com](https://supabase.com)
2. Choose region closest to your users
3. Set strong database password
4. Wait for project initialization

### Database Configuration

**Enable Required Extensions:**

```sql
-- Enable UUID generation
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Enable pg_stat_statements (optional, for performance monitoring)
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

**Run Migrations:**

Migrations are located in `supabase/migrations/`. Run them in order:

1. `20251204175752_create_initial_schema.sql`
2. `20251204190515_transform_to_financial_education_portal.sql`
3. `20251205054803_transform_to_educational_portal_v2.sql`
4. `20251205162834_create_tracking_scripts_table.sql`
5. `20251214081313_create_lead_gen_system.sql`
6. `20251214103621_add_dual_iframe_and_visitor_detection.sql`
7. `20251214122218_add_enhanced_visitor_detection.sql`

**Via Supabase Dashboard:**
1. Go to **Database** → **Migrations**
2. Click **"New migration"**
3. Paste migration content
4. Click **"Run"**

### Row-Level Security (RLS)

All tables have RLS enabled. Default policies:

- **Public read** - For published content
- **Authenticated write** - For admin operations
- **Service role** - Full access for edge functions

### Edge Functions

**Deploy Visitor Detection Function:**

```bash
supabase functions deploy visitor-detection
```

Or via Dashboard:
1. Go to **Edge Functions**
2. Click **"New function"**
3. Name: `visitor-detection`
4. Paste code from `supabase/functions/visitor-detection/index.ts`
5. Deploy

**Environment Variables for Edge Functions:**

These are automatically available:
- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `SUPABASE_DB_URL`

### Authentication

**Enable Email/Password Auth:**

1. Go to **Authentication** → **Providers**
2. Enable **Email**
3. Disable email confirmation (for admin users)
4. Set password requirements

**Create Admin User:**

1. Go to **Authentication** → **Users**
2. Click **"Add user"**
3. Choose **"Create new user"**
4. Enter email and password
5. Click **"Create user"**

### Storage (Optional)

If you need file uploads:

1. Go to **Storage**
2. Create bucket (e.g., `article-images`)
3. Set policies:
   - Public read for published images
   - Authenticated write for admin
4. Enable RLS on bucket

---

## Build Configuration

### package.json Scripts

```json
{
  "scripts": {
    "dev": "vite",                          // Start dev server
    "build": "vite build",                  // Production build
    "lint": "eslint .",                     // Lint code
    "preview": "vite preview",              // Preview production build
    "typecheck": "tsc --noEmit -p tsconfig.app.json"  // Type checking
  }
}
```

### Build Output

Production build creates:

```
dist/
├── index.html              # Main HTML
├── assets/
│   ├── index-[hash].js    # Main JS bundle
│   ├── index-[hash].css   # Main CSS bundle
│   └── [chunks]/          # Code-split chunks
└── [public files]         # Static assets
```

### Build Optimization

**Automatic Optimizations:**

- Code splitting
- Tree shaking
- Minification
- Asset hashing
- Gzip compression (via server)

**Manual Optimizations:**

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'vendor': ['react', 'react-dom'],
          'supabase': ['@supabase/supabase-js'],
        }
      }
    },
    chunkSizeWarningLimit: 1000
  }
});
```

---

## Tailwind CSS Configuration

### tailwind.config.js

```javascript
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        'educational-primary': '#0369a1',
        'educational-secondary': '#0284c7',
      },
    },
  },
  plugins: [],
}
```

### Custom Theme Colors

Add custom colors to `theme.extend.colors`:

```javascript
colors: {
  'brand-primary': '#your-color',
  'brand-secondary': '#your-color',
  // ... more colors
}
```

### Custom Fonts

```javascript
fontFamily: {
  'sans': ['Inter', 'system-ui', 'sans-serif'],
  'display': ['Poppins', 'sans-serif'],
}
```

### Breakpoints

Default Tailwind breakpoints:

```javascript
screens: {
  'sm': '640px',   // Mobile landscape / Small tablet
  'md': '768px',   // Tablet
  'lg': '1024px',  // Desktop
  'xl': '1280px',  // Large desktop
  '2xl': '1536px', // Extra large desktop
}
```

---

## TypeScript Configuration

### tsconfig.json

Main TypeScript configuration:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### Type Checking

Run type checking:

```bash
npm run typecheck
```

### Custom Types

Define custom types in `src/types/index.ts`:

```typescript
export interface CustomType {
  id: string;
  name: string;
}
```

---

## ESLint Configuration

### eslint.config.js

```javascript
import js from '@eslint/js'
import globals from 'globals'
import reactHooks from 'eslint-plugin-react-hooks'
import reactRefresh from 'eslint-plugin-react-refresh'
import tseslint from 'typescript-eslint'

export default tseslint.config(
  { ignores: ['dist'] },
  {
    extends: [js.configs.recommended, ...tseslint.configs.recommended],
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      ecmaVersion: 2020,
      globals: globals.browser,
    },
    plugins: {
      'react-hooks': reactHooks,
      'react-refresh': reactRefresh,
    },
    rules: {
      ...reactHooks.configs.recommended.rules,
      'react-refresh/only-export-components': [
        'warn',
        { allowConstantExport: true },
      ],
    },
  },
)
```

### Linting

Run linter:

```bash
npm run lint
```

Auto-fix issues:

```bash
npm run lint -- --fix
```

---

## Vite Configuration

### vite.config.ts

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    open: true
  },
  build: {
    outDir: 'dist',
    sourcemap: false,
    rollupOptions: {
      output: {
        manualChunks: undefined
      }
    }
  }
})
```

### Development Server

Configure dev server:

```typescript
server: {
  port: 5173,              // Dev server port
  host: true,              // Expose to network
  open: true,              // Auto-open browser
  cors: true,              // Enable CORS
  proxy: {                 // Proxy API requests
    '/api': 'http://localhost:3000'
  }
}
```

### Build Options

```typescript
build: {
  target: 'es2015',        // Browser target
  outDir: 'dist',          // Output directory
  assetsDir: 'assets',     // Assets subdirectory
  sourcemap: false,        // Disable sourcemaps
  minify: 'esbuild',       // Minification method
  chunkSizeWarningLimit: 1000  // Chunk size warning
}
```

---

## Detection Settings

### Detection Modes

Configure per lead gen page:

**Simple Mode:**
```typescript
visitor_detection_mode: 'simple'
detection_threshold_score: 50
```
- Fast, basic detection
- User agent analysis
- ~70% accuracy

**Fingerprint Mode:**
```typescript
visitor_detection_mode: 'fingerprint'
detection_threshold_score: 50
```
- Advanced behavioral analysis
- Browser fingerprinting
- ~85% accuracy

**MaxMind Lite Mode:**
```typescript
visitor_detection_mode: 'maxmindLite'
detection_threshold_score: 60
enable_maxmind_lite: true
```
- Geo-based detection
- Requires external service
- ~90% accuracy

### Threshold Configuration

**Lenient (0-30):**
- More visitors classified as human
- Higher false positives
- Good for: Awareness campaigns

**Balanced (31-60):**
- Recommended default
- Good accuracy/recall balance
- Good for: Most use cases

**Strict (61-100):**
- Fewer visitors classified as human
- Lower false positives
- Good for: High-value conversions

### Cache Configuration

Detection results cached for 24 hours:

```sql
-- Modify cache duration
ALTER TABLE visitor_detections
ALTER COLUMN cache_expires_at
SET DEFAULT (now() + interval '12 hours');  -- Change to 12 hours
```

---

## Analytics Configuration

### Event Tracking

Configure tracking in `src/utils/leadGenAnalytics.ts`:

```typescript
export const ANALYTICS_CONFIG = {
  enableTracking: true,
  privacyMode: true,        // Hash IPs
  sessionTimeout: 30 * 60,  // 30 minutes
  sampleRate: 1.0,          // Track 100% of events
};
```

### IP Hashing

For privacy compliance:

```typescript
// Always hash IPs before storing
const ipHash = await hashIP(visitorIP);
```

### Retention Policy

Set data retention:

```sql
-- Delete analytics older than 90 days
DELETE FROM lead_gen_analytics
WHERE timestamp < now() - interval '90 days';
```

Run cleanup periodically via cron or scheduled function.

---

## Production Settings

### Environment Variables

**Production `.env`:**

```env
VITE_SUPABASE_URL=https://your-prod-project.supabase.co
VITE_SUPABASE_ANON_KEY=your_prod_anon_key
NODE_ENV=production
```

### Build for Production

```bash
# Install dependencies
npm ci

# Run type checking
npm run typecheck

# Lint code
npm run lint

# Build
npm run build

# Test build
npm run preview
```

### Performance Checklist

- [ ] Enable gzip/brotli compression
- [ ] Configure CDN (Cloudflare, etc.)
- [ ] Set proper cache headers
- [ ] Enable HTTP/2
- [ ] Minify assets
- [ ] Optimize images
- [ ] Lazy load components
- [ ] Code splitting configured

### Security Checklist

- [ ] HTTPS enabled
- [ ] Security headers configured
- [ ] RLS policies active
- [ ] Environment variables secured
- [ ] API keys rotated
- [ ] CORS configured properly
- [ ] Rate limiting enabled
- [ ] Input validation active

### Monitoring

**Recommended Tools:**

- **Supabase Dashboard** - Database and API monitoring
- **Sentry** - Error tracking
- **Google Analytics** - User analytics
- **Uptime Robot** - Availability monitoring

---

## Configuration Checklist

### Initial Setup

- [ ] Create `.env` file with Supabase credentials
- [ ] Install dependencies (`npm install`)
- [ ] Run database migrations
- [ ] Deploy edge functions
- [ ] Create admin user
- [ ] Test local development

### Pre-Deployment

- [ ] Build successfully (`npm run build`)
- [ ] No TypeScript errors (`npm run typecheck`)
- [ ] No ESLint errors (`npm run lint`)
- [ ] All tests pass
- [ ] Environment variables configured
- [ ] Database migrations applied

### Post-Deployment

- [ ] Verify site is accessible
- [ ] Test admin login
- [ ] Verify detection system working
- [ ] Check analytics tracking
- [ ] Test all major features
- [ ] Monitor error logs
- [ ] Set up regular backups

---

**Configuration Guide by VasilMihovCom**

*Complete configuration reference for all environments*

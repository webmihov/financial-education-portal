# System Architecture Documentation

**Author:** VasilMihovCom

Comprehensive architectural documentation for the Financial Education Portal & Lead Generation Platform.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [High-Level Architecture](#high-level-architecture)
3. [Frontend Architecture](#frontend-architecture)
4. [Backend Architecture](#backend-architecture)
5. [Data Flow](#data-flow)
6. [Component Hierarchy](#component-hierarchy)
7. [State Management](#state-management)
8. [Detection System Architecture](#detection-system-architecture)
9. [Performance Considerations](#performance-considerations)
10. [Scalability](#scalability)

---

## System Overview

The Financial Education Portal is a modern, full-stack web application built with:

**Frontend Stack:**
- React 18.3 (UI library)
- TypeScript 5.5 (Type safety)
- Vite 5.4 (Build tool)
- React Router 7 (Routing)
- Tailwind CSS 3.4 (Styling)

**Backend Stack:**
- Supabase (Backend-as-a-Service)
- PostgreSQL (Database)
- Deno (Edge functions runtime)
- Row-Level Security (Access control)

**Architecture Pattern:**
- JAMstack (JavaScript, APIs, Markup)
- SPA (Single Page Application)
- Serverless backend
- Real-time subscriptions

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         END USERS                               │
│  (Web Browsers: Chrome, Firefox, Safari, Edge, Mobile)         │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            │ HTTPS
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                      CDN / Web Server                           │
│  (Nginx, Vercel, Netlify, or Digital Ocean Droplet)           │
│                                                                  │
│  ┌─────────────────┐   ┌──────────────────┐                   │
│  │  Static Assets  │   │  React SPA       │                   │
│  │  (HTML/CSS/JS)  │   │  (dist/)         │                   │
│  └─────────────────┘   └──────────────────┘                   │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            │ API Calls (REST)
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                     SUPABASE PLATFORM                           │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    API Gateway                           │  │
│  │  (Authentication, Rate Limiting, CORS)                   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                            │                                     │
│  ┌─────────────────┐  ┌───▼──────────┐  ┌──────────────────┐  │
│  │  Edge Functions │  │  PostgreSQL  │  │  Realtime        │  │
│  │  (Deno)         │  │  Database    │  │  Subscriptions   │  │
│  │                 │  │              │  │                  │  │
│  │  - visitor-     │  │  - Tables    │  │  - WebSockets    │  │
│  │    detection    │  │  - RLS       │  │  - Live updates  │  │
│  │                 │  │  - Indexes   │  │                  │  │
│  └─────────────────┘  └──────────────┘  └──────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
                            │
                            │ External Services (Optional)
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                    THIRD-PARTY SERVICES                         │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐ │
│  │  Analytics   │  │  MaxMind     │  │  External Iframes    │ │
│  │  (Optional)  │  │  (Optional)  │  │  (Promotional)       │ │
│  └──────────────┘  └──────────────┘  └──────────────────────┘ │
└──────────────────────────────────────────────────────────────────┘
```

---

## Frontend Architecture

### Application Structure

```
src/
├── main.tsx                 # Application entry point
├── App.tsx                  # Root component & routing
├── index.css                # Global styles
│
├── components/              # Reusable components
│   ├── admin/               # Admin-specific components
│   │   └── GeneratorConfigModal.tsx
│   ├── landing/             # Landing page sections
│   │   ├── Header.tsx
│   │   ├── Hero.tsx
│   │   ├── Footer.tsx
│   │   ├── Education.tsx
│   │   ├── Reviews.tsx
│   │   ├── SpinGame.tsx
│   │   ├── PlatformCategories.tsx
│   │   └── WebsiteComparison.tsx
│   ├── leadgen/             # Lead gen components
│   │   ├── PromotionBanner.tsx
│   │   ├── PromotionButton.tsx
│   │   ├── PromotionInlineCard.tsx
│   │   ├── PromotionTextAd.tsx
│   │   └── PromotionContainer.tsx
│   ├── modals/              # Modal dialogs
│   │   ├── AgeVerification.tsx
│   │   └── CookieConsent.tsx
│   ├── ProtectedRoute.tsx   # Auth guard
│   ├── RiskWarningBanner.tsx
│   └── DisclaimerBlock.tsx
│
├── pages/                   # Page components
│   ├── LandingPage.tsx      # Homepage
│   ├── ArticlePage.tsx      # Article display
│   ├── LeadGenPage.tsx      # Lead gen landing
│   ├── AboutPage.tsx
│   ├── TermsPage.tsx
│   ├── PrivacyPage.tsx
│   ├── DisclaimerPage.tsx
│   ├── CookiePolicyPage.tsx
│   ├── AccessibilityPage.tsx
│   ├── EditorialStandardsPage.tsx
│   ├── LegalLayout.tsx      # Legal page wrapper
│   └── admin/               # Admin pages
│       ├── AdminLogin.tsx
│       ├── AdminLayout.tsx
│       ├── Dashboard.tsx
│       ├── ContentManagement.tsx
│       ├── PlatformCategoriesManagement.tsx
│       ├── EducationalArticlesManagement.tsx
│       ├── ReviewsManagement.tsx
│       ├── RegulatoryBodiesManagement.tsx
│       ├── PaymentMethodsManagement.tsx
│       ├── RiskWarningsManagement.tsx
│       ├── TrackingScriptsManagement.tsx
│       ├── LeadGenPagesManagement.tsx
│       ├── LeadGenPromotionsManagement.tsx
│       ├── PromotionDiagnostics.tsx
│       └── AnalyticsDashboard.tsx
│
├── hooks/                   # Custom React hooks
│   ├── useLocalStorage.ts   # Local storage hook
│   └── useVisitorProfile.ts # Visitor detection hook
│
├── lib/                     # Core libraries
│   └── supabase.ts          # Supabase client
│
├── utils/                   # Utility functions
│   ├── analytics.ts         # Analytics helpers
│   ├── articleGenerator.ts  # Content generation
│   ├── databaseValidation.ts
│   ├── ipClassification.ts  # IP utilities
│   ├── leadGenAnalytics.ts  # Lead gen tracking
│   ├── leadGenDefaults.ts   # Default values
│   ├── leadGenGenerator.ts  # Lead gen utilities
│   ├── promotionGenerator.ts # Promotion generation
│   ├── schema.ts            # Type utilities
│   ├── scriptLoader.ts      # Dynamic script loading
│   ├── sitemap.ts           # SEO utilities
│   └── visitorDetection.ts  # Detection logic
│
└── types/                   # TypeScript definitions
    └── index.ts             # All type definitions
```

### Routing Architecture

React Router configuration in `App.tsx`:

```
/ (Root)
├── /                           # Landing page
├── /guia/:slug                 # Educational articles
├── /lp/:slug                   # Lead gen pages
├── /about                      # About page
├── /terms                      # Terms of service
├── /privacy                    # Privacy policy
├── /disclaimer                 # Disclaimer
├── /cookie-policy              # Cookie policy
├── /accessibility              # Accessibility statement
├── /editorial-standards        # Editorial standards
├── /admin                      # Admin login
└── /admin/* (Protected)        # Admin panel
    ├── /admin/dashboard
    ├── /admin/categories
    ├── /admin/articles
    ├── /admin/testimonials
    ├── /admin/regulatory
    ├── /admin/payments
    ├── /admin/warnings
    ├── /admin/compliance
    ├── /admin/tracking
    ├── /admin/lead-gen
    ├── /admin/lead-gen-promotions
    └── /admin/promotion-diagnostics
```

### Component Patterns

**1. Page Components:**
- Full-page views
- Handle data fetching
- Compose smaller components
- Manage page-level state

**2. Layout Components:**
- Header, Footer
- Provide consistent structure
- Handle navigation

**3. Feature Components:**
- Self-contained features
- Own state and logic
- Reusable across pages

**4. UI Components:**
- Pure presentation
- Props-driven
- No business logic
- Highly reusable

---

## Backend Architecture

### Supabase Components

**1. PostgreSQL Database:**
- Primary data store
- ACID compliance
- Row-Level Security
- Real-time capabilities

**2. Edge Functions:**
- Serverless functions (Deno runtime)
- Server-side logic
- API endpoints
- External integrations

**3. Realtime:**
- WebSocket connections
- Live data updates
- Presence tracking
- Broadcast messaging

**4. Authentication:**
- Email/password auth
- Session management
- JWT tokens
- RLS integration

### Database Design

**Data Model:**

```
platform_categories (1) ─────< (many) educational_articles
                                       educational_testimonials

platform_categories (1) ─────< (many) lead_gen_pages (1) ─────< (many) lead_gen_promotions
                                                          (1) ─────< (many) lead_gen_analytics

visitor_detections (independent, cache table)

tracking_scripts (independent)
payment_methods_pt (independent)
regulatory_bodies (independent)
risk_warnings (independent)
```

**Key Design Decisions:**

1. **UUID Primary Keys:** Cross-platform compatibility
2. **Timestamp Fields:** Audit trail (created_at, updated_at)
3. **Soft Deletes:** Use `is_active` instead of DELETE
4. **Bilingual Content:** Separate PT/EN columns
5. **JSONB Fields:** Flexible data (detection_signals)
6. **Array Fields:** Lists without joins (bullets, target_locations)

### Edge Functions Architecture

**Visitor Detection Function:**

```
Request → CORS Check → Parse Body → Check Cache → Detect → Store → Response
```

**Flow:**

1. **OPTIONS Request:** Return CORS headers
2. **POST Request:**
   - Parse request body
   - Check cache (by IP hash)
   - If cached & valid → Return cached result
   - If not cached:
     - Analyze user agent
     - Classify IP type
     - Calculate confidence score
     - Store in cache
     - Return result
3. **Error:** Return 500 with error details

---

## Data Flow

### User Browsing Flow

```
User visits homepage
    ↓
React app loads
    ↓
Fetch categories (Supabase)
    ↓
Fetch articles (Supabase)
    ↓
Render homepage
    ↓
User clicks article
    ↓
Navigate to /guia/:slug
    ↓
Fetch article by slug
    ↓
Render article page
```

### Lead Gen Page Flow

```
User visits /lp/:slug
    ↓
Fetch lead gen page
    ↓
Start visitor detection (useVisitorProfile hook)
    ↓
Client-side detection:
  - Browser fingerprinting
  - Feature detection
  - Behavioral analysis
    ↓
Server-side detection (Edge Function):
  - IP classification
  - User agent analysis
  - Combined scoring
    ↓
Classification: Human / Bot / Uncertain
    ↓
Select iframe based on:
  - Visitor type
  - Visibility mode
  - Configuration
    ↓
Render page with appropriate iframe
    ↓
Track page view (analytics)
```

### Promotion Display Flow

```
User on page (homepage, article, category)
    ↓
Identify context:
  - Current location (homepage, article, etc.)
  - Current category (if applicable)
  - Available positions (top, middle, bottom, sidebar)
    ↓
Query promotions:
  - Match target_locations
  - Match target_category_ids
  - Filter by is_active
  - Sort by display_order
    ↓
For each position:
  - Get top matching promotion
  - Render appropriate component
    ↓
User clicks promotion
    ↓
Track click (analytics)
    ↓
Navigate to lead gen page
```

### Admin Content Management Flow

```
Admin logs in
    ↓
Check authentication (Supabase Auth)
    ↓
If authenticated → Redirect to dashboard
If not → Show login form
    ↓
Admin navigates to content section
    ↓
Fetch content from Supabase
    ↓
Display in table/list
    ↓
Admin edits content
    ↓
Validate input (client-side)
    ↓
Submit to Supabase
    ↓
RLS check (server-side)
    ↓
Update database
    ↓
Real-time subscription notifies other admins
    ↓
Refresh UI
```

---

## Component Hierarchy

### Landing Page Hierarchy

```
LandingPage
├── Header
│   ├── Logo
│   └── Navigation
├── Hero
│   ├── Headline
│   ├── Subheadline
│   └── CTA Button
├── PlatformCategories
│   └── CategoryCard[] (map)
├── Education
│   └── ArticleCard[] (map)
├── SpinGame
│   ├── SpinWheel
│   └── PrizeDisplay
├── Reviews
│   └── ReviewCard[] (map)
├── WebsiteComparison
│   └── ComparisonTable
├── RiskWarningBanner
└── Footer
    ├── Links
    ├── Legal Links
    └── Copyright
```

### Lead Gen Page Hierarchy

```
LeadGenPage
├── Header
├── HeroSection
│   ├── Title
│   ├── Subtitle
│   └── Bullets[]
├── PreviewText
├── IframeContainer (conditional)
│   └── iframe (v1 or v2)
├── ComplianceNote
├── RiskWarningBanner
└── Footer
```

### Admin Dashboard Hierarchy

```
AdminLayout
├── Sidebar
│   └── NavigationLinks[]
├── TopBar
│   ├── Title
│   └── LogoutButton
└── Outlet (child routes)
    ├── Dashboard
    │   ├── StatsCards[]
    │   └── RecentActivity
    ├── ContentManagement
    │   ├── DataTable
    │   ├── EditModal
    │   └── DeleteConfirmation
    └── ...
```

---

## State Management

### State Architecture

The application uses **local component state** with React hooks. No global state management library (Redux, MobX) is used.

**State Categories:**

1. **Server State** - Data from Supabase
2. **UI State** - Component-level state
3. **Form State** - Form inputs and validation
4. **Auth State** - Supabase auth session
5. **Cache State** - Local storage, session storage

### Server State Management

Fetching data with `useState` + `useEffect`:

```typescript
const [data, setData] = useState<Type[]>([]);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(false);

useEffect(() => {
  async function fetchData() {
    const { data, error } = await supabase
      .from('table')
      .select('*');

    if (error) setError(true);
    else setData(data || []);
    setLoading(false);
  }
  fetchData();
}, []);
```

**Alternative:** Could use React Query for caching, but current approach is simpler.

### Form State

Local form state with controlled inputs:

```typescript
const [formData, setFormData] = useState({
  field1: '',
  field2: '',
});

const handleChange = (e) => {
  setFormData({
    ...formData,
    [e.target.name]: e.target.value
  });
};
```

### Persistent State

Using `useLocalStorage` hook:

```typescript
const [cookieConsent, setCookieConsent] = useLocalStorage('cookie_consent', null);
```

Persists to `localStorage` automatically.

---

## Detection System Architecture

### Multi-Layer Detection

```
┌─────────────────────────────────────────────┐
│           Client-Side Detection             │
│  ┌───────────────────────────────────────┐  │
│  │  1. Simple Mode                       │  │
│  │     - User agent parsing              │  │
│  │     - Known bot patterns              │  │
│  └───────────────────────────────────────┘  │
│  ┌───────────────────────────────────────┐  │
│  │  2. Fingerprint Mode                  │  │
│  │     - Canvas fingerprint              │  │
│  │     - WebGL fingerprint               │  │
│  │     - Audio context                   │  │
│  │     - Font detection                  │  │
│  │     - Plugin enumeration              │  │
│  │     - Screen properties               │  │
│  │     - Headless detection              │  │
│  │     - Old browser detection           │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────┐
│          Server-Side Detection              │
│  ┌───────────────────────────────────────┐  │
│  │  Edge Function (Deno)                 │  │
│  │     - IP classification               │  │
│  │     - User agent analysis             │  │
│  │     - PTR hostname lookup             │  │
│  │     - Datacenter detection            │  │
│  │     - Mobile carrier detection        │  │
│  │     - VPN/Proxy detection             │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────┐
│            Combined Scoring                 │
│  ┌───────────────────────────────────────┐  │
│  │  Weighted Algorithm:                  │  │
│  │  - Client signals (40%)               │  │
│  │  - Server signals (40%)               │  │
│  │  - Behavioral patterns (20%)          │  │
│  │                                        │  │
│  │  Output:                               │  │
│  │  - Visitor type (human/bot/uncertain) │  │
│  │  - Confidence score (0-100)           │  │
│  │  - IP type classification             │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────┐
│              Cache Storage                  │
│  (visitor_detections table, 24h TTL)       │
└─────────────────────────────────────────────┘
```

---

## Performance Considerations

### Frontend Optimization

**1. Code Splitting:**
- Automatic route-based splitting
- Dynamic imports for heavy components
- Lazy loading for modals

**2. Asset Optimization:**
- Image lazy loading
- SVG icons (Lucide)
- Minified bundles
- Tree shaking

**3. Render Optimization:**
- Memo for expensive computations
- Callback memoization
- Virtual scrolling for long lists
- Debouncing for search/filters

**4. Network Optimization:**
- Request deduplication
- Caching strategies
- Prefetching critical data
- Connection pooling (Supabase)

### Backend Optimization

**1. Database:**
- Indexes on frequently queried columns
- Composite indexes for complex queries
- Query result caching (24h for detections)
- Connection pooling

**2. Edge Functions:**
- Minimal dependencies
- Efficient algorithms
- Early returns
- Error boundaries

**3. Real-time:**
- Selective subscriptions
- Unsubscribe on unmount
- Throttled updates

---

## Scalability

### Horizontal Scalability

**Frontend:**
- Statically built (scales infinitely)
- CDN distribution
- No server-side rendering bottleneck

**Backend (Supabase):**
- Managed horizontal scaling
- Connection pooling
- Read replicas (on higher tiers)
- Global distribution

### Vertical Scalability

**Database:**
- Upgrade Supabase plan
- More compute resources
- Larger connection pool
- Increased storage

### Scalability Limits

**Current Architecture:**
- **Frontend:** Unlimited (static files)
- **Database:** Depends on Supabase tier
  - Free: 500 MB, 50k rows
  - Pro: 8 GB, unlimited rows
  - Enterprise: Custom
- **Edge Functions:** 500k executions/month (free), 2M (pro)
- **Real-time:** 200 concurrent connections (free), 500 (pro)

### Scaling Strategies

**When to Scale:**

1. **Database approaching limits**
   - Upgrade Supabase tier
   - Archive old analytics
   - Optimize queries

2. **Edge function limits**
   - Increase caching
   - Batch requests
   - Optimize detection logic

3. **Real-time connections**
   - Reduce poll frequency
   - Selective subscriptions
   - Upgrade tier

---

**Architecture Documentation by VasilMihovCom**

*Complete system design and architectural patterns*

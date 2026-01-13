# API Reference Documentation

**Author:** VasilMihovCom

Complete technical documentation for the Financial Education Portal API, database schema, and edge functions.

---

## Table of Contents

1. [Overview](#overview)
2. [Supabase Edge Functions](#supabase-edge-functions)
3. [Database Schema](#database-schema)
4. [Frontend API](#frontend-api)
5. [Type Definitions](#type-definitions)
6. [Utility Functions](#utility-functions)
7. [Error Handling](#error-handling)
8. [Rate Limiting](#rate-limiting)

---

## Overview

The platform uses Supabase as the backend, providing:

- **PostgreSQL Database** - Relational data storage
- **Edge Functions** - Server-side logic (Deno runtime)
- **Real-time Subscriptions** - Live data updates
- **Row-Level Security** - Access control
- **Authentication** - User management

**Base URL:** `https://[project-id].supabase.co`

---

## Supabase Edge Functions

### Visitor Detection Function

**Endpoint:** `/functions/v1/visitor-detection`

**Method:** `POST`

**Description:** Server-side visitor classification based on IP analysis and user agent.

**Authentication:** Requires `Authorization` header with Supabase anon key

#### Request

```typescript
POST /functions/v1/visitor-detection
Authorization: Bearer YOUR_ANON_KEY
Content-Type: application/json

{
  "userAgent": "Mozilla/5.0...",
  "ipHash": "sha256_hash_of_ip",
  "signals": {
    "isHeadless": false,
    "hasModernApis": true,
    "behaviorScore": 75
  }
}
```

**Request Body Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `userAgent` | string | Yes | Browser user agent string |
| `ipHash` | string | No | SHA-256 hash of visitor IP |
| `signals` | object | No | Client-side detection signals |

**Signals Object:**

| Field | Type | Description |
|-------|------|-------------|
| `isHeadless` | boolean | Headless browser detected |
| `hasModernApis` | boolean | Modern browser APIs available |
| `behaviorScore` | number | Behavioral analysis score (0-100) |

#### Response

```json
{
  "visitorType": "human",
  "confidence": 85,
  "ipType": "residential",
  "detectionMethod": "server_side",
  "signals": {
    "userAgent": "Mozilla/5.0...",
    "clientIP": "present",
    "ptrHostname": "",
    "uaAnalysis": {
      "isBot": false,
      "isOldBrowser": false,
      "isMobile": true
    }
  }
}
```

**Response Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `visitorType` | string | "human", "bot", or "uncertain" |
| `confidence` | number | Confidence score (0-100) |
| `ipType` | string | IP classification (see below) |
| `detectionMethod` | string | Detection method used |
| `signals` | object | Detailed detection signals |

**IP Types:**

- `residential` - Home internet connection
- `mobile` - Mobile carrier network
- `datacenter` - Server/hosting IP
- `organization` - Business network
- `vpn` - VPN service
- `proxy` - Proxy server
- `unknown` - Unclassified

#### Error Responses

**500 Internal Server Error:**
```json
{
  "error": "Detection failed",
  "message": "Error description"
}
```

#### Detection Logic

**Bot Detection Rules:**

1. User agent contains bot patterns → Bot (90% confidence)
2. IP is from datacenter → Bot (80% confidence)
3. Headless browser detected → Bot (95% confidence)
4. Behavior score < 30 → Bot (70-100% confidence)

**Human Detection Rules:**

1. IP is residential/mobile → Human (85% confidence)
2. Behavior score > 70 → Human (70-100% confidence)
3. Normal browser features → Human (variable confidence)

**Caching:**

- Results cached for 24 hours by IP hash
- Cached results returned immediately
- Cache expires automatically

#### Usage Example

```typescript
const detectVisitor = async (userAgent: string, signals: object) => {
  const ipHash = await hashIP(visitorIP);

  const response = await fetch(
    `${supabaseUrl}/functions/v1/visitor-detection`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${supabaseAnonKey}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        userAgent,
        ipHash,
        signals,
      }),
    }
  );

  const result = await response.json();
  return result;
};
```

---

## Database Schema

### Tables Overview

| Table | Description | RLS Enabled |
|-------|-------------|-------------|
| `platform_categories` | Platform category definitions | Yes |
| `educational_articles` | Educational content | Yes |
| `educational_testimonials` | User reviews | Yes |
| `regulatory_bodies` | Regulatory organizations | Yes |
| `payment_methods_pt` | Payment method info | Yes |
| `risk_warnings` | Risk warning messages | Yes |
| `lead_gen_pages` | Lead generation pages | Yes |
| `lead_gen_promotions` | Promotion campaigns | Yes |
| `lead_gen_analytics` | Analytics events | Yes |
| `visitor_detections` | Detection cache | Yes |
| `tracking_scripts` | Third-party scripts | Yes |

### platform_categories

**Description:** Platform category definitions

```sql
CREATE TABLE platform_categories (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name_pt text NOT NULL,
  name_en text NOT NULL,
  description_pt text DEFAULT '',
  description_en text DEFAULT '',
  slug text UNIQUE NOT NULL,
  icon text DEFAULT '',
  display_order integer DEFAULT 0,
  is_active boolean DEFAULT true,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);
```

**Columns:**

| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `id` | uuid | No | Primary key |
| `name_pt` | text | No | Portuguese name |
| `name_en` | text | No | English name |
| `description_pt` | text | Yes | Portuguese description |
| `description_en` | text | Yes | English description |
| `slug` | text | No | URL-friendly identifier |
| `icon` | text | Yes | Lucide icon name |
| `display_order` | integer | Yes | Sorting order |
| `is_active` | boolean | Yes | Active status |
| `created_at` | timestamptz | Yes | Creation timestamp |
| `updated_at` | timestamptz | Yes | Last update timestamp |

**RLS Policies:**

- Public can read active categories
- Authenticated users can manage categories

### educational_articles

**Description:** Educational content articles

```sql
CREATE TABLE educational_articles (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  title text NOT NULL,
  slug text UNIQUE NOT NULL,
  excerpt text DEFAULT '',
  content text DEFAULT '',
  category_id uuid REFERENCES platform_categories(id),
  featured_image_url text DEFAULT '',
  seo_title text DEFAULT '',
  seo_description text DEFAULT '',
  keywords text[] DEFAULT '{}',
  author text DEFAULT '',
  reading_time_minutes integer DEFAULT 5,
  is_published boolean DEFAULT false,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now(),
  published_at timestamptz
);
```

**Columns:**

| Column | Type | Description |
|--------|------|-------------|
| `id` | uuid | Primary key |
| `title` | text | Article title |
| `slug` | text | URL slug |
| `excerpt` | text | Short summary |
| `content` | text | Full article content (Markdown) |
| `category_id` | uuid | Foreign key to category |
| `featured_image_url` | text | Hero image URL |
| `seo_title` | text | SEO meta title |
| `seo_description` | text | SEO meta description |
| `keywords` | text[] | SEO keywords array |
| `author` | text | Author name |
| `reading_time_minutes` | integer | Estimated reading time |
| `is_published` | boolean | Publication status |
| `created_at` | timestamptz | Creation timestamp |
| `updated_at` | timestamptz | Last update timestamp |
| `published_at` | timestamptz | Publication date |

**Indexes:**

- `slug` (unique)
- `category_id`
- `is_published`
- `published_at DESC`

### lead_gen_pages

**Description:** Lead generation landing pages

```sql
CREATE TABLE lead_gen_pages (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name text NOT NULL,
  slug text UNIQUE NOT NULL,
  hero_title text NOT NULL,
  hero_subtitle text DEFAULT '',
  bullets text[] DEFAULT '{}',
  preview_text text DEFAULT '',
  iframe_version1 text,
  iframe_version2 text,
  iframe_visibility_mode text DEFAULT 'all',
  visitor_detection_mode text DEFAULT 'simple',
  detection_threshold_score integer DEFAULT 50,
  enable_maxmind_lite boolean DEFAULT false,
  compliance_note text DEFAULT '',
  category_id uuid REFERENCES platform_categories(id),
  is_active boolean DEFAULT true,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);
```

**Columns:**

| Column | Type | Description |
|--------|------|-------------|
| `id` | uuid | Primary key |
| `name` | text | Internal page name |
| `slug` | text | URL slug (accessible at `/lp/slug`) |
| `hero_title` | text | Main headline |
| `hero_subtitle` | text | Supporting headline |
| `bullets` | text[] | Bullet point array |
| `preview_text` | text | Introduction text |
| `iframe_version1` | text | Primary iframe HTML |
| `iframe_version2` | text | Alternative iframe HTML |
| `iframe_visibility_mode` | text | "all", "humansOnly", "botsOnly" |
| `visitor_detection_mode` | text | "simple", "fingerprint", "maxmindLite" |
| `detection_threshold_score` | integer | Threshold (0-100) |
| `enable_maxmind_lite` | boolean | Enable geo detection |
| `compliance_note` | text | Legal disclaimer |
| `category_id` | uuid | Associated category |
| `is_active` | boolean | Active status |

**Constraints:**

- `iframe_visibility_mode` IN ('all', 'humansOnly', 'botsOnly')
- `visitor_detection_mode` IN ('simple', 'fingerprint', 'maxmindLite')
- `detection_threshold_score` BETWEEN 0 AND 100

### lead_gen_promotions

**Description:** Promotional content configurations

```sql
CREATE TABLE lead_gen_promotions (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_gen_page_id uuid REFERENCES lead_gen_pages(id) ON DELETE CASCADE,
  promotion_type text NOT NULL,
  promotion_title text NOT NULL,
  promotion_description text DEFAULT '',
  cta_text text DEFAULT 'Saiba Mais',
  target_locations text[] DEFAULT '{}',
  target_category_ids uuid[] DEFAULT '{}',
  placement_position text DEFAULT 'middle',
  display_order integer DEFAULT 0,
  is_active boolean DEFAULT true,
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);
```

**Columns:**

| Column | Type | Description |
|--------|------|-------------|
| `id` | uuid | Primary key |
| `lead_gen_page_id` | uuid | Target landing page |
| `promotion_type` | text | "banner", "button", "inline_card", "text_ad" |
| `promotion_title` | text | Promotion headline |
| `promotion_description` | text | Supporting text |
| `cta_text` | text | Call-to-action text |
| `target_locations` | text[] | ["homepage", "articles", "categories"] |
| `target_category_ids` | uuid[] | Specific category UUIDs |
| `placement_position` | text | "top", "middle", "bottom", "sidebar" |
| `display_order` | integer | Priority order |
| `is_active` | boolean | Active status |

**Constraints:**

- `promotion_type` IN ('banner', 'button', 'inline_card', 'text_ad')
- `placement_position` IN ('top', 'middle', 'bottom', 'sidebar')

### lead_gen_analytics

**Description:** Analytics events tracking

```sql
CREATE TABLE lead_gen_analytics (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_gen_page_id uuid REFERENCES lead_gen_pages(id),
  promotion_id uuid REFERENCES lead_gen_promotions(id),
  event_type text NOT NULL,
  ip_hash text,
  referrer text DEFAULT '',
  visitor_classification text DEFAULT 'uncertain',
  ip_type text DEFAULT 'unknown',
  has_javascript boolean DEFAULT true,
  detection_signals jsonb DEFAULT '{}',
  device_authenticity_score integer DEFAULT 50,
  is_headless_browser boolean DEFAULT false,
  is_old_browser boolean DEFAULT false,
  ptr_hostname text DEFAULT '',
  timestamp timestamptz DEFAULT now()
);
```

**Columns:**

| Column | Type | Description |
|--------|------|-------------|
| `id` | uuid | Primary key |
| `lead_gen_page_id` | uuid | Associated page |
| `promotion_id` | uuid | Associated promotion (for clicks) |
| `event_type` | text | "page_view" or "promotion_click" |
| `ip_hash` | text | SHA-256 IP hash |
| `referrer` | text | Referrer URL |
| `visitor_classification` | text | "human", "bot", "uncertain" |
| `ip_type` | text | IP classification |
| `has_javascript` | boolean | JavaScript enabled |
| `detection_signals` | jsonb | Detection data |
| `device_authenticity_score` | integer | Authenticity score (0-100) |
| `is_headless_browser` | boolean | Headless detected |
| `is_old_browser` | boolean | Old browser detected |
| `ptr_hostname` | text | Reverse DNS hostname |
| `timestamp` | timestamptz | Event time |

**Indexes:**

- `lead_gen_page_id, timestamp DESC`
- `promotion_id, timestamp DESC`
- `visitor_classification`
- `ip_type`
- `(visitor_classification, ip_type, timestamp DESC)` (composite)

### visitor_detections

**Description:** Detection result cache (24-hour TTL)

```sql
CREATE TABLE visitor_detections (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  ip_hash text NOT NULL,
  visitor_classification text DEFAULT 'uncertain',
  ip_type text DEFAULT 'unknown',
  confidence_score integer DEFAULT 50,
  detection_signals jsonb DEFAULT '{}',
  user_agent text DEFAULT '',
  has_javascript boolean DEFAULT true,
  is_headless_browser boolean DEFAULT false,
  is_old_browser boolean DEFAULT false,
  ptr_hostname text DEFAULT '',
  asn_number integer,
  asn_organization text DEFAULT '',
  country_code text DEFAULT '',
  is_mobile_carrier boolean DEFAULT false,
  detection_method text DEFAULT 'simple',
  cache_expires_at timestamptz DEFAULT (now() + interval '24 hours'),
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);
```

**Indexes:**

- `ip_hash`
- `cache_expires_at`
- `visitor_classification`
- `ip_type`
- `created_at DESC`

**Auto-cleanup Function:**

```sql
CREATE FUNCTION cleanup_expired_visitor_detections()
RETURNS void AS $$
BEGIN
  DELETE FROM visitor_detections
  WHERE cache_expires_at < now();
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## Frontend API

### Supabase Client

**Initialization:**

```typescript
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

export const supabase = createClient(supabaseUrl, supabaseAnonKey);
```

### Query Examples

**Fetch Active Categories:**

```typescript
const { data, error } = await supabase
  .from('platform_categories')
  .select('*')
  .eq('is_active', true)
  .order('display_order', { ascending: true });
```

**Fetch Article by Slug:**

```typescript
const { data, error } = await supabase
  .from('educational_articles')
  .select('*, category:platform_categories(*)')
  .eq('slug', articleSlug)
  .eq('is_published', true)
  .maybeSingle();
```

**Fetch Lead Gen Page:**

```typescript
const { data, error } = await supabase
  .from('lead_gen_pages')
  .select('*')
  .eq('slug', pageSlug)
  .eq('is_active', true)
  .maybeSingle();
```

**Track Page View:**

```typescript
const { error } = await supabase
  .from('lead_gen_analytics')
  .insert({
    lead_gen_page_id: pageId,
    event_type: 'page_view',
    ip_hash: hashedIP,
    referrer: document.referrer,
    visitor_classification: 'human',
    ip_type: 'residential',
    has_javascript: true,
    detection_signals: signals,
    device_authenticity_score: 85,
  });
```

**Real-time Subscription:**

```typescript
const subscription = supabase
  .channel('lead_gen_promotions_changes')
  .on(
    'postgres_changes',
    {
      event: '*',
      schema: 'public',
      table: 'lead_gen_promotions'
    },
    (payload) => {
      console.log('Promotion changed:', payload);
      refetchPromotions();
    }
  )
  .subscribe();

// Cleanup
subscription.unsubscribe();
```

---

## Type Definitions

### Core Types

```typescript
// src/types/index.ts

export interface PlatformCategory {
  id: string;
  name_pt: string;
  name_en: string;
  description_pt: string;
  description_en: string;
  slug: string;
  icon: string;
  display_order: number;
  is_active: boolean;
  created_at: string;
  updated_at: string;
}

export interface EducationalArticle {
  id: string;
  title: string;
  slug: string;
  excerpt: string;
  content: string;
  category_id: string;
  category?: PlatformCategory;
  featured_image_url: string;
  seo_title: string;
  seo_description: string;
  keywords: string[];
  author: string;
  reading_time_minutes: number;
  is_published: boolean;
  created_at: string;
  updated_at: string;
  published_at: string | null;
}

export interface LeadGenPage {
  id: string;
  name: string;
  slug: string;
  hero_title: string;
  hero_subtitle: string;
  bullets: string[];
  preview_text: string;
  iframe_version1: string | null;
  iframe_version2: string | null;
  iframe_visibility_mode: 'all' | 'humansOnly' | 'botsOnly';
  visitor_detection_mode: 'simple' | 'fingerprint' | 'maxmindLite';
  detection_threshold_score: number;
  enable_maxmind_lite: boolean;
  compliance_note: string;
  category_id: string | null;
  is_active: boolean;
  created_at: string;
  updated_at: string;
}

export type PromotionType = 'banner' | 'button' | 'inline_card' | 'text_ad';
export type PlacementPosition = 'top' | 'middle' | 'bottom' | 'sidebar';

export interface LeadGenPromotion {
  id: string;
  lead_gen_page_id: string;
  promotion_type: PromotionType;
  promotion_title: string;
  promotion_description: string;
  cta_text: string;
  target_locations: string[];
  target_category_ids: string[];
  placement_position: PlacementPosition;
  display_order: number;
  is_active: boolean;
  created_at: string;
  updated_at: string;
}

export type VisitorType = 'human' | 'bot' | 'unknown';
export type DetectionMode = 'simple' | 'fingerprint' | 'maxmindLite';
export type IPType = 'residential' | 'mobile' | 'datacenter' | 'organization' | 'vpn' | 'proxy' | 'unknown';

export interface VisitorProfile {
  visitorType: VisitorType;
  ipType: IPType | null;
  confidence: number;
  signals: Record<string, any>;
  isHeadlessBrowser: boolean;
  isOldBrowser: boolean;
}
```

---

## Utility Functions

### Analytics

```typescript
// src/utils/leadGenAnalytics.ts

export async function trackLeadGenPageView(
  pageId: string,
  referrer: string,
  enhancedData: {
    visitorClassification: string;
    ipType: string;
    hasJavascript: boolean;
    detectionSignals: Record<string, any>;
    deviceAuthenticityScore: number;
    isHeadlessBrowser: boolean;
    isOldBrowser: boolean;
  }
): Promise<void>

export async function trackPromotionClick(
  promotionId: string,
  pageId: string,
  enhancedData: any
): Promise<void>

export async function getPromotionStats(
  promotionId: string
): Promise<{ clicks: number; impressions: number }>

export async function getPageStats(
  pageId: string
): Promise<{ views: number; uniqueVisitors: number }>
```

### Promotion Matching

```typescript
// src/utils/promotionGenerator.ts

export function matchPromotionsForContext(
  currentLocation: string,
  currentCategoryId: string | null,
  availablePositions: PlacementPosition[]
): Promise<LeadGenPromotion[]>
```

### IP Hashing

```typescript
// src/utils/ipClassification.ts

export async function hashIP(ip: string): Promise<string> {
  const encoder = new TextEncoder();
  const data = encoder.encode(ip);
  const hashBuffer = await crypto.subtle.digest('SHA-256', data);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
}
```

---

## Error Handling

### Standard Error Responses

All errors follow this structure:

```json
{
  "error": "ErrorType",
  "message": "Human-readable description",
  "details": {}
}
```

### Common Errors

| Error | Code | Description |
|-------|------|-------------|
| NotFound | 404 | Resource not found |
| Unauthorized | 401 | Authentication required |
| Forbidden | 403 | Insufficient permissions |
| ValidationError | 400 | Invalid input data |
| ServerError | 500 | Internal server error |

---

## Rate Limiting

### Edge Function Limits

- **Rate:** 100 requests/minute per IP
- **Burst:** 20 requests/second
- **Response:** 429 Too Many Requests

### Supabase Database Limits

- **Free Tier:** 500 MB database, 2 GB bandwidth
- **Pro Tier:** 8 GB database, 50 GB bandwidth

---

**API Reference by VasilMihovCom**

*Complete technical documentation for developers*

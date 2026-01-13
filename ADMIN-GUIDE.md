# Administrator Guide: Financial Education Portal

**Author:** VasilMihovCom

Complete guide for administrators managing the Financial Education Portal platform.

---

## Table of Contents

1. [Admin Panel Overview](#admin-panel-overview)
2. [Authentication](#authentication)
3. [Dashboard](#dashboard)
4. [Content Management](#content-management)
5. [Lead Generation System](#lead-generation-system)
6. [Analytics and Monitoring](#analytics-and-monitoring)
7. [Promotion Management](#promotion-management)
8. [Tracking Scripts](#tracking-scripts)
9. [Common Tasks](#common-tasks)
10. [Best Practices](#best-practices)
11. [Troubleshooting](#troubleshooting)

---

## Admin Panel Overview

### Accessing the Admin Panel

1. Navigate to `/admin` in your browser
2. Enter admin credentials
3. Access the admin dashboard

### Admin Panel Structure

The admin interface is organized into sections:

- **Dashboard** - Overview and quick stats
- **Categories** - Manage platform categories
- **Articles** - Educational content management
- **Testimonials** - User reviews and testimonials
- **Regulatory** - Regulatory bodies information
- **Payments** - Payment methods management
- **Warnings** - Risk warnings management
- **Compliance** - Compliance content
- **Tracking** - Tracking scripts management
- **Lead Gen** - Lead generation pages
- **Promotions** - Promotion campaigns
- **Diagnostics** - Promotion debugging tools

### Navigation

Use the sidebar menu to navigate between sections. The current section is highlighted for easy reference.

---

## Authentication

### First Time Login

1. Go to `/admin`
2. Enter your Supabase-created email and password
3. Click "Sign In"
4. You'll be redirected to the dashboard

### Creating Admin Users

Admin users must be created in Supabase:

1. Go to your Supabase project dashboard
2. Navigate to **Authentication** → **Users**
3. Click **"Add user"**
4. Choose **"Create new user"**
5. Enter email and password
6. User can now access `/admin`

### Password Reset

If you forget your password:

1. Use Supabase password reset functionality
2. Go to Supabase Dashboard → Authentication → Users
3. Find user and click "Reset Password"
4. Follow email instructions

### Security Best Practices

- Use strong, unique passwords
- Never share admin credentials
- Log out when finished
- Use 2FA if enabled on Supabase
- Regularly rotate passwords

---

## Dashboard

### Overview Section

The dashboard provides:

- **Quick Stats** - Key metrics at a glance
- **Recent Activity** - Latest changes and events
- **System Status** - Health indicators
- **Quick Actions** - Common tasks

### Quick Stats

Monitor key metrics:

- Total platform categories
- Published articles count
- Active lead gen pages
- Active promotions
- Total page views
- Conversion rates

### Recent Activity

View recent:

- Content updates
- New promotions
- User actions
- System events

---

## Content Management

### Platform Categories Management

**Location:** `/admin/categories`

**Creating a Category:**

1. Click **"Add Category"** button
2. Fill in the form:
   - **Name (PT)** - Portuguese name
   - **Name (EN)** - English name
   - **Description (PT)** - Portuguese description
   - **Description (EN)** - English description
   - **Slug** - URL-friendly identifier (auto-generated)
   - **Icon** - Icon identifier (from Lucide React)
   - **Display Order** - Sorting order (lower = first)
   - **Active** - Enable/disable category
3. Click **"Save"**

**Editing a Category:**

1. Find category in list
2. Click **Edit** icon
3. Update fields
4. Click **"Save"**

**Deleting a Category:**

1. Click **Delete** icon next to category
2. Confirm deletion
3. Note: This may affect related content

**Best Practices:**

- Use clear, descriptive names
- Keep slugs simple and SEO-friendly
- Set logical display orders
- Test icon names before saving
- Deactivate rather than delete when possible

### Educational Articles Management

**Location:** `/admin/articles`

**Creating an Article:**

1. Click **"Add Article"** button
2. Complete the form:
   - **Title** - Article headline
   - **Slug** - URL path (e.g., `my-article`)
   - **Excerpt** - Short summary (150-200 chars)
   - **Content** - Full article content (Markdown supported)
   - **Category** - Select platform category
   - **Featured Image URL** - Hero image (optional)
   - **SEO Title** - Browser tab title
   - **SEO Description** - Meta description
   - **Keywords** - Comma-separated keywords
   - **Author** - Author name
   - **Reading Time** - Estimated minutes
   - **Published** - Publish status
3. Click **"Save"**

**Content Formatting:**

Articles support Markdown:

```markdown
# Heading 1
## Heading 2
### Heading 3

**Bold text**
*Italic text*

- Bullet point 1
- Bullet point 2

1. Numbered item
2. Numbered item

[Link text](https://example.com)
```

**SEO Optimization:**

- **Title:** 50-60 characters
- **Description:** 150-160 characters
- **Keywords:** 5-10 relevant terms
- **Slug:** Short, descriptive, keyword-rich

**Best Practices:**

- Write for readability (short paragraphs)
- Include risk warnings where appropriate
- Update regularly to keep content fresh
- Use high-quality images
- Proofread before publishing

### Testimonials/Reviews Management

**Location:** `/admin/testimonials`

**Adding a Testimonial:**

1. Click **"Add Testimonial"**
2. Fill in fields:
   - **Author Name** - Reviewer name
   - **Platform Category** - Related category
   - **Rating** - 1-5 stars
   - **Review Text (PT)** - Portuguese review
   - **Review Text (EN)** - English review
   - **Date** - Review date
   - **Verified** - Mark as verified
   - **Active** - Enable/disable
3. Save

**Managing Reviews:**

- Edit existing reviews
- Mark as verified/unverified
- Delete inappropriate content
- Sort by date or rating
- Filter by category

### Regulatory Bodies Management

**Location:** `/admin/regulatory`

Manage information about regulatory organizations:

1. Add regulatory body with name and description
2. Include jurisdiction and contact info
3. Link to official websites
4. Add logos (optional)
5. Set active status

### Payment Methods Management

**Location:** `/admin/payments`

Manage payment method information:

1. Add payment method names
2. Include descriptions in Portuguese
3. Add features and benefits
4. Include supported platforms
5. Add fees information
6. Set active status

### Risk Warnings Management

**Location:** `/admin/warnings`

Manage risk warnings displayed site-wide:

**Creating a Warning:**

1. Click **"Add Warning"**
2. Fill in:
   - **Title** - Warning headline
   - **Message (PT)** - Portuguese warning text
   - **Message (EN)** - English warning text
   - **Severity** - Low/Medium/High
   - **Display Location** - Where to show
   - **Active** - Enable/disable
3. Save

**Best Practices:**

- Keep warnings clear and concise
- Use appropriate severity levels
- Update for regulatory changes
- Test display on all pages

---

## Lead Generation System

### Lead Gen Pages Management

**Location:** `/admin/lead-gen`

**Creating a Lead Gen Page:**

1. Click **"Add Page"**
2. Complete form:
   - **Name** - Internal identifier
   - **Slug** - URL path (accessible at `/lp/slug`)
   - **Hero Title** - Main headline
   - **Hero Subtitle** - Supporting text
   - **Bullets** - Key benefit points (one per line)
   - **Preview Text** - Introduction paragraph
   - **Compliance Note** - Required disclaimers
   - **Platform Category** - Associated category
   - **Iframe Version 1** - Primary iframe code
   - **Iframe Version 2** - Alternative iframe code
   - **Iframe Visibility Mode:**
     - **All** - Show to everyone
     - **Humans Only** - Show version 1 to humans, version 2 to bots
     - **Bots Only** - Show only to detected bots
   - **Visitor Detection Mode:**
     - **Simple** - User agent based
     - **Fingerprint** - Behavioral analysis
     - **MaxMind Lite** - Geo-based detection
   - **Detection Threshold** - Confidence score (0-100)
   - **Enable MaxMind** - Use geo-detection
   - **Active** - Enable/disable page
3. Click **"Save"**

**Iframe Configuration:**

Paste complete iframe embed code:

```html
<iframe
  src="https://example.com/offer"
  width="100%"
  height="600"
  frameborder="0"
></iframe>
```

**Visitor Detection Settings:**

- **Simple Mode:** Fast, basic bot detection
- **Fingerprint Mode:** Advanced behavioral analysis
- **MaxMind Lite:** IP-based geo-location detection

**Threshold Score:**

- **0-30:** Very lenient (more visitors classified as human)
- **31-60:** Balanced (recommended)
- **61-100:** Strict (fewer visitors classified as human)

**Best Practices:**

- Test both iframe versions before activating
- Use "Humans Only" mode for compliance
- Set reasonable detection thresholds
- Include comprehensive compliance notes
- Monitor analytics after activation

### Dual Iframe Strategy

The platform supports A/B testing with two iframe versions:

**Use Cases:**

1. **Humans Only Mode:**
   - Version 1: Real promotional content
   - Version 2: Static educational content

2. **Testing:**
   - Version 1: Variant A
   - Version 2: Variant B

3. **Compliance:**
   - Version 1: Full offer details
   - Version 2: Simplified information

**Configuration Example:**

```
Mode: Humans Only
Version 1: <iframe src="real-offer.com">
Version 2: <iframe src="educational-content.com">
Detection: Fingerprint
Threshold: 50
```

This ensures bots and crawlers see educational content while humans see the actual promotional offer.

---

## Promotion Management

### Lead Gen Promotions

**Location:** `/admin/lead-gen-promotions`

**Promotion Types:**

1. **Banner** - Full-width promotional banner
2. **Button** - Call-to-action button
3. **Inline Card** - Card-style promotion
4. **Text Ad** - Text-based advertisement

**Creating a Promotion:**

1. Click **"Add Promotion"**
2. Fill in form:
   - **Lead Gen Page** - Select target page
   - **Promotion Type** - Choose type
   - **Title** - Promotion headline
   - **Description** - Supporting text
   - **CTA Text** - Button/link text
   - **Placement Position:**
     - **Top** - Above main content
     - **Middle** - Within content
     - **Bottom** - Below content
     - **Sidebar** - Side column
   - **Target Locations:**
     - **Homepage** - Main landing page
     - **Articles** - Educational articles
     - **Category Pages** - Category listings
   - **Target Categories** - Specific categories
   - **Display Order** - Sorting priority
   - **Active** - Enable/disable
3. Click **"Save"**

**Position Guidelines by Type:**

- **Banner:** Top or bottom
- **Button:** Any position
- **Inline Card:** Middle or sidebar
- **Text Ad:** Middle or bottom

**Targeting Rules:**

Promotions can be targeted by:

- **Location:** Where they appear (homepage, articles, etc.)
- **Category:** Which platform categories
- **Order:** Priority when multiple match

**Example Targeting:**

```
Target Locations: Homepage, Articles
Target Categories: Banking, Fintech
Position: Middle
Order: 1
```

This shows the promotion on the homepage and article pages for Banking and Fintech categories, positioned in the middle of content, with highest priority.

### Bulk Promotion Generation

**Auto-Generate Feature:**

1. Navigate to promotions page
2. Click **"Generate Promotions"**
3. Configure generator:
   - **Distribution Strategy:**
     - Even across all pages
     - Weighted by category
     - Custom distribution
   - **Promotion Types** - Select which types
   - **Positions** - Select placement positions
   - **Targeting** - Set location/category rules
4. Click **"Generate"**
5. Review generated promotions
6. Activate selected promotions

**Configuration Modal:**

The generator modal allows:

- Setting default CTA text
- Choosing promotion mix
- Defining placement strategy
- Bulk activation

**Best Practices:**

- Generate in batches by category
- Review before activating all
- Test different positions
- Monitor performance metrics

---

## Analytics and Monitoring

### Promotion Diagnostics

**Location:** `/admin/promotion-diagnostics`

**Features:**

1. **Real-time Monitoring**
   - View active promotions
   - See current targeting rules
   - Check configuration status

2. **Performance Metrics**
   - Click-through rates
   - Impression counts
   - Conversion tracking

3. **Visitor Classification**
   - Human vs. bot ratio
   - IP type distribution
   - Detection accuracy

4. **Debugging Tools**
   - Test promotion matching
   - Validate targeting rules
   - Preview promotions

**Using Diagnostics:**

1. Select a promotion from list
2. View detailed metrics:
   - Total impressions
   - Click count
   - CTR percentage
   - Visitor breakdown
3. Check targeting matches
4. Test different scenarios

**Key Metrics:**

- **CTR (Click-Through Rate):** Clicks ÷ Impressions × 100
- **Human Ratio:** Human visitors ÷ Total visitors
- **Residential IP %:** Residential IPs ÷ Total IPs

**Ideal Metrics:**

- CTR: 2-5% (varies by type)
- Human Ratio: >70%
- Residential IP: >60%

### Visitor Detection Analytics

Monitor detection system performance:

**Visitor Classification:**

- **Human:** Legitimate visitors
- **Bot:** Automated crawlers
- **Uncertain:** Unclear classification

**IP Types:**

- **Residential:** Home internet
- **Mobile:** Mobile carriers
- **Datacenter:** Server IPs
- **VPN/Proxy:** Anonymization services
- **Organization:** Business networks

**Detection Signals:**

View detailed signals:

- User agent analysis
- JavaScript execution
- Browser fingerprint
- IP reputation
- Behavioral patterns

**Interpreting Results:**

Good indicators:
- High residential IP percentage
- Diverse user agents
- JavaScript enabled
- Normal browser fingerprints

Bad indicators:
- High datacenter IP percentage
- Suspicious user agents
- JavaScript disabled
- Headless browser detection

---

## Tracking Scripts

**Location:** `/admin/tracking`

Manage third-party tracking and analytics scripts.

**Adding a Tracking Script:**

1. Click **"Add Script"**
2. Fill in form:
   - **Name** - Identifier (e.g., "Google Analytics")
   - **Script Code** - Complete script tag
   - **Cookie Category:**
     - **Necessary** - Always loaded
     - **Analytics** - User must consent
     - **Marketing** - User must consent
     - **Preferences** - User must consent
   - **Position:**
     - **Head** - In `<head>` section
     - **Body Start** - At top of `<body>`
     - **Body End** - Before `</body>`
   - **Active** - Enable/disable
3. Click **"Save"**

**Script Code Example:**

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=GA-XXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA-XXXXXX');
</script>
```

**Cookie Categories Explained:**

- **Necessary:** Required for site function (always loaded)
- **Analytics:** Site improvement (requires consent)
- **Marketing:** Advertising and tracking (requires consent)
- **Preferences:** User experience (requires consent)

**Best Practices:**

- Only add necessary scripts
- Test scripts in development first
- Use appropriate cookie categories
- Document each script's purpose
- Remove unused scripts

---

## Common Tasks

### Publishing a New Article

1. Go to `/admin/articles`
2. Click **"Add Article"**
3. Write content with proper formatting
4. Add SEO metadata
5. Select category
6. Set to published
7. Save and verify on frontend

### Creating a New Lead Gen Campaign

1. Create lead gen page (`/admin/lead-gen`)
2. Configure iframe(s) and detection
3. Create promotions (`/admin/lead-gen-promotions`)
4. Set targeting rules
5. Activate page and promotions
6. Monitor in diagnostics

### Updating Risk Warnings

1. Go to `/admin/warnings`
2. Edit existing warning or create new
3. Update text for both languages
4. Verify display locations
5. Save and check frontend

### Reviewing Analytics

1. Go to `/admin/promotion-diagnostics`
2. Check key metrics
3. Identify underperforming promotions
4. Adjust targeting or content
5. Monitor improvements

---

## Best Practices

### Content Management

- Update content regularly
- Maintain consistent tone and style
- Include proper risk warnings
- Test on mobile devices
- Proofread thoroughly

### Lead Generation

- Always include compliance notes
- Test detection settings
- Monitor visitor classification
- Review iframe content regularly
- Keep targeting rules simple

### Promotions

- Don't over-promote on single page
- Vary promotion types
- Test different positions
- Monitor CTR regularly
- Pause underperforming promotions

### Security

- Log out when finished
- Never share credentials
- Use strong passwords
- Review user permissions
- Monitor admin activity logs

### Performance

- Optimize images before uploading
- Keep tracking scripts minimal
- Monitor page load times
- Cache where appropriate
- Regular database maintenance

---

## Troubleshooting

### Promotion Not Displaying

**Check:**

1. Promotion is active
2. Lead gen page is active
3. Targeting rules match current page
4. Display order is appropriate
5. No conflicting rules

### Low CTR on Promotions

**Solutions:**

1. Improve CTA text
2. Change placement position
3. Adjust targeting to better audience
4. Test different promotion types
5. Update promotional content

### Detection System Issues

**Troubleshooting:**

1. Check detection threshold setting
2. Verify edge function is deployed
3. Review detection signals in diagnostics
4. Test with different browsers
5. Check Supabase logs

### Content Not Updating

**Steps:**

1. Verify changes were saved
2. Clear browser cache
3. Check if content is published
4. Verify slug/URL is correct
5. Check for JavaScript errors

### Analytics Not Recording

**Check:**

1. Tracking scripts are active
2. Cookie consent is given (if required)
3. Supabase connection is working
4. No ad blockers interfering
5. Check browser console for errors

---

## Support Resources

- [API Reference](./API-REFERENCE.md) - Technical documentation
- [Troubleshooting Guide](./TROUBLESHOOTING.md) - Common issues
- [Security Guide](./SECURITY.md) - Security best practices
- Supabase Dashboard - Database and logs
- Browser DevTools - Debugging

---

## Admin Checklist

### Daily Tasks

- [ ] Review new analytics data
- [ ] Check for errors in diagnostics
- [ ] Monitor visitor classification rates
- [ ] Respond to any alerts

### Weekly Tasks

- [ ] Review promotion performance
- [ ] Update underperforming content
- [ ] Check for content updates needed
- [ ] Review and approve new content

### Monthly Tasks

- [ ] Comprehensive analytics review
- [ ] Content audit and updates
- [ ] Security review
- [ ] Performance optimization
- [ ] Backup verification

---

**Administrator Guide by VasilMihovCom**

*Empowering administrators to manage effective financial education platforms*

---

For technical support or questions, consult the full documentation suite or contact system administrators.

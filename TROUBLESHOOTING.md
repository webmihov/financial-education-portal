# Troubleshooting Guide

**Author:** VasilMihovCom

Complete troubleshooting guide for common issues in the Financial Education Portal.

---

## Table of Contents

1. [Installation Issues](#installation-issues)
2. [Build and Compilation Errors](#build-and-compilation-errors)
3. [Runtime Errors](#runtime-errors)
4. [Database Issues](#database-issues)
5. [Authentication Problems](#authentication-problems)
6. [Detection System Issues](#detection-system-issues)
7. [Promotion Display Problems](#promotion-display-problems)
8. [Performance Issues](#performance-issues)
9. [Deployment Issues](#deployment-issues)
10. [Debug Tools and Techniques](#debug-tools-and-techniques)

---

## Installation Issues

### npm install fails

**Symptom:** Errors during `npm install`

**Causes & Solutions:**

**1. Node version mismatch**
```bash
# Check Node version
node --version

# Should be 18.x or higher
# Install correct version:
# Using nvm:
nvm install 18
nvm use 18
```

**2. Package lock conflicts**
```bash
# Remove lock file and node_modules
rm package-lock.json
rm -rf node_modules

# Clean npm cache
npm cache clean --force

# Reinstall
npm install
```

**3. Permission errors**
```bash
# On Unix/Mac, avoid sudo npm install
# Instead, fix npm permissions:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH

# Then retry
npm install
```

### Missing .env file

**Symptom:** `VITE_SUPABASE_URL is not defined`

**Solution:**

1. Create `.env` file in project root
2. Add Supabase credentials:
```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your_anon_key
```
3. Restart dev server

---

## Build and Compilation Errors

### TypeScript errors

**Symptom:** `error TS2304: Cannot find name...`

**Solutions:**

**1. Check type imports**
```typescript
// Ensure imports are correct
import type { TypeName } from './types';
// Or
import { TypeName } from './types';
```

**2. Install missing @types packages**
```bash
npm install --save-dev @types/react @types/react-dom
```

**3. Run typecheck**
```bash
npm run typecheck
```

### Vite build fails

**Symptom:** Build errors during `npm run build`

**Solutions:**

**1. Clear Vite cache**
```bash
rm -rf node_modules/.vite
npm run build
```

**2. Check for dynamic imports**
```typescript
// Ensure dynamic imports are properly formatted
const Component = lazy(() => import('./Component'));
```

**3. Verify environment variables**
```bash
# Make sure all VITE_ prefixed vars are present
cat .env
```

### ESLint errors

**Symptom:** Linting failures

**Solution:**

```bash
# Auto-fix issues
npm run lint -- --fix

# Or disable for specific line
// eslint-disable-next-line rule-name
```

---

## Runtime Errors

### Blank page / White screen

**Symptoms:** Page loads but shows nothing

**Debug Steps:**

**1. Check browser console**
- Press F12
- Look for errors in Console tab
- Note the error message and file

**2. Common causes:**

**React rendering error:**
```javascript
// Check App.tsx for errors
// Verify all components return valid JSX
```

**Routing issue:**
```javascript
// Check React Router configuration
// Verify BrowserRouter is wrapping routes
```

**3. Verify build**
```bash
# Rebuild
npm run build

# Test preview
npm run preview
```

### Components not rendering

**Symptom:** Specific components don't appear

**Debug:**

**1. Check component imports**
```typescript
// Correct:
import { Component } from './Component';

// Incorrect:
import Component from './Component'; // if using named export
```

**2. Check conditional rendering**
```typescript
// Verify conditions
{condition && <Component />}

// Add debug logging
console.log('Condition:', condition);
```

**3. Check props**
```typescript
// Verify required props are passed
<Component requiredProp={value} />
```

### "Failed to fetch" errors

**Symptom:** Network requests failing

**Solutions:**

**1. Check Supabase credentials**
```bash
# Verify .env file
cat .env

# Test connection
curl https://your-project.supabase.co
```

**2. Check CORS**
```javascript
// Supabase automatically handles CORS
// But verify you're using correct URL
const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
```

**3. Check network tab**
- F12 → Network tab
- Look for failed requests
- Check status code and response

---

## Database Issues

### Cannot connect to database

**Symptom:** All queries fail

**Solutions:**

**1. Verify Supabase credentials**
```bash
# Check Project URL and Anon Key in Supabase Dashboard
# Settings → API
```

**2. Check project status**
- Go to Supabase Dashboard
- Verify project is not paused
- Check for maintenance notices

**3. Test connection**
```typescript
const { data, error } = await supabase
  .from('platform_categories')
  .select('count');

console.log('Connection test:', { data, error });
```

### RLS policy errors

**Symptom:** "row-level security policy" error

**Solutions:**

**1. Check if you're authenticated**
```typescript
const { data: { user } } = await supabase.auth.getUser();
console.log('User:', user);
```

**2. Review RLS policies**
```sql
-- In Supabase Dashboard → Database → Tables
-- Click table → Policies tab
-- Verify policies allow your operation
```

**3. Use service role for admin operations**
```typescript
// Only in Edge Functions, never in client!
const supabase = createClient(url, serviceRoleKey);
```

### Migration failures

**Symptom:** Migrations don't apply

**Solutions:**

**1. Check migration order**
- Run migrations in chronological order
- Check existing migrations:
```sql
SELECT * FROM supabase_migrations.schema_migrations;
```

**2. Check for syntax errors**
- Review migration SQL
- Test in SQL editor first

**3. Manual rollback if needed**
```sql
-- Revert problematic changes
DROP TABLE IF EXISTS table_name;
```

---

## Authentication Problems

### Cannot login to admin panel

**Symptom:** Login fails

**Solutions:**

**1. Verify user exists**
- Supabase Dashboard → Authentication → Users
- Check if admin user exists

**2. Check credentials**
- Verify email and password
- Reset password if forgotten

**3. Check authentication config**
```typescript
// Verify auth client is initialized
import { supabase } from './lib/supabase';

const { data, error } = await supabase.auth.signInWithPassword({
  email,
  password
});
console.log('Login result:', { data, error });
```

### Session expires immediately

**Symptom:** Logged out right after login

**Solutions:**

**1. Check browser localStorage**
```javascript
// In browser console
localStorage.getItem('supabase.auth.token');
```

**2. Check cookie settings**
- Ensure browser allows cookies
- Check for ad blockers interfering

**3. Verify JWT expiration**
- Supabase Dashboard → Authentication → Settings
- Check "JWT expiry limit"

---

## Detection System Issues

### Visitor detection not working

**Symptom:** All visitors classified as "uncertain"

**Debug:**

**1. Check edge function deployment**
```bash
# Verify function exists
supabase functions list

# Check logs
supabase functions logs visitor-detection
```

**2. Test edge function directly**
```bash
curl -X POST \
  https://your-project.supabase.co/functions/v1/visitor-detection \
  -H "Authorization: Bearer YOUR_ANON_KEY" \
  -H "Content-Type: application/json" \
  -d '{"userAgent": "Mozilla/5.0..."}'
```

**3. Check client-side detection**
```typescript
// In browser console
// Look for detection signals
console.log('Visitor profile:', visitorProfile);
```

### Detection cache not working

**Symptom:** Same visitor detected multiple times

**Solutions:**

**1. Check cache table**
```sql
SELECT * FROM visitor_detections
ORDER BY created_at DESC
LIMIT 10;
```

**2. Verify IP hashing**
```typescript
// Ensure IP is being hashed
const ipHash = await hashIP(clientIP);
console.log('IP hash:', ipHash);
```

**3. Check expiration**
```sql
-- Verify cache_expires_at is in future
SELECT ip_hash, cache_expires_at,
       cache_expires_at > now() as is_valid
FROM visitor_detections;
```

### High bot classification rate

**Symptom:** Too many visitors marked as bots

**Solutions:**

**1. Lower threshold**
```typescript
// In lead gen page configuration
detection_threshold_score: 40 // Lower = more lenient
```

**2. Check detection signals**
```sql
SELECT visitor_classification, detection_signals
FROM lead_gen_analytics
WHERE visitor_classification = 'bot'
LIMIT 10;
```

**3. Review detection logic**
- Check for false positives
- Adjust scoring algorithm if needed

---

## Promotion Display Problems

### Promotions not showing

**Symptom:** Expected promotions don't appear

**Debug Steps:**

**1. Check promotion is active**
```sql
SELECT * FROM lead_gen_promotions
WHERE id = 'promotion_id';
-- Verify is_active = true
```

**2. Check lead gen page is active**
```sql
SELECT * FROM lead_gen_pages
WHERE id = 'page_id';
-- Verify is_active = true
```

**3. Check targeting rules**
```sql
SELECT
  target_locations,
  target_category_ids,
  placement_position
FROM lead_gen_promotions
WHERE id = 'promotion_id';

-- Verify they match current context
```

**4. Use diagnostics page**
- Go to `/admin/promotion-diagnostics`
- Test promotion matching
- Review targeting configuration

### Wrong iframe showing

**Symptom:** Incorrect iframe version displayed

**Debug:**

**1. Check visitor classification**
```typescript
console.log('Visitor type:', visitorProfile.visitorType);
console.log('Visibility mode:', page.iframe_visibility_mode);
```

**2. Check iframe selection logic**
```typescript
// In browser console
console.log('Iframe selection:', iframeSelection);
```

**3. Verify iframe configuration**
```sql
SELECT
  iframe_visibility_mode,
  iframe_version1 IS NOT NULL as has_v1,
  iframe_version2 IS NOT NULL as has_v2
FROM lead_gen_pages
WHERE slug = 'your-slug';
```

---

## Performance Issues

### Slow page loads

**Symptoms:** Pages take long to load

**Solutions:**

**1. Check network waterfall**
- F12 → Network tab
- Look for slow requests
- Identify bottlenecks

**2. Optimize images**
```html
<!-- Use lazy loading -->
<img src="image.jpg" loading="lazy" />

<!-- Use appropriate formats -->
<!-- WebP for browsers that support it -->
```

**3. Check database queries**
```sql
-- In Supabase Dashboard → Database → Performance
-- Look for slow queries
-- Add indexes if needed
```

**4. Enable caching**
```nginx
# In Nginx config
location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

### High memory usage

**Symptom:** Browser tab uses excessive memory

**Solutions:**

**1. Check for memory leaks**
```typescript
// Ensure useEffect cleanup
useEffect(() => {
  const subscription = supabase
    .channel('changes')
    .subscribe();

  return () => {
    subscription.unsubscribe(); // Important!
  };
}, []);
```

**2. Limit rendered items**
```typescript
// Use pagination or virtual scrolling
// Don't render 1000+ items at once
const displayedItems = items.slice(0, 50);
```

**3. Profile with React DevTools**
- Install React DevTools extension
- Use Profiler tab
- Identify expensive renders

---

## Deployment Issues

### Build succeeds but site doesn't work

**Symptom:** Production build has errors

**Solutions:**

**1. Test production build locally**
```bash
npm run build
npm run preview
# Visit http://localhost:4173
```

**2. Check environment variables**
```bash
# Verify they're set in production
# For Vercel/Netlify, add in dashboard
# For Digital Ocean, check .env file
```

**3. Check paths**
```typescript
// Use relative paths
import Component from './Component'; // Good
import Component from 'src/Component'; // Bad
```

### Nginx 404 errors on refresh

**Symptom:** Refreshing any page except home shows 404

**Solution:**

Update Nginx config:

```nginx
location / {
    try_files $uri $uri/ /index.html;
}
```

Reload Nginx:
```bash
sudo systemctl reload nginx
```

### SSL certificate issues

**Symptom:** HTTPS not working or warnings

**Solutions:**

**1. Check certificate status**
```bash
sudo certbot certificates
```

**2. Renew certificate**
```bash
sudo certbot renew
```

**3. Verify Nginx SSL config**
```nginx
listen 443 ssl;
ssl_certificate /path/to/cert.pem;
ssl_certificate_key /path/to/privkey.pem;
```

---

## Debug Tools and Techniques

### Browser DevTools

**Console Tab:**
- View console.log output
- See errors and warnings
- Run JavaScript code

**Network Tab:**
- Monitor all requests
- Check response times
- View request/response data
- Check status codes

**Application Tab:**
- View localStorage
- Check cookies
- Inspect session storage

**React DevTools:**
- Inspect component tree
- View props and state
- Profile performance

### Supabase Dashboard

**Database:**
- Run SQL queries
- View table data
- Check RLS policies
- Monitor performance

**Edge Functions:**
- View logs
- Test functions
- Check deployments

**Authentication:**
- View users
- Check sessions
- Review policies

### Logging Best Practices

**Development:**
```typescript
// Detailed logging
console.log('State:', state);
console.log('Props:', props);
console.error('Error:', error);
```

**Production:**
```typescript
// Use error tracking service (e.g., Sentry)
// Log to server, not console
if (import.meta.env.PROD) {
  logToServer(error);
} else {
  console.error(error);
}
```

### Common Debug Commands

```bash
# Check running processes
ps aux | grep node

# Check port usage
lsof -i :5173

# Check Nginx status
sudo systemctl status nginx

# Check Nginx logs
sudo tail -f /var/log/nginx/error.log

# Check disk space
df -h

# Check memory usage
free -m

# Test network connectivity
curl -I https://your-site.com
```

---

## Getting Additional Help

If issues persist:

1. **Check Documentation**
   - Review relevant guide (USER-GUIDE, ADMIN-GUIDE, etc.)
   - Check API Reference for technical details

2. **Search Known Issues**
   - Check GitHub Issues
   - Search Stack Overflow
   - Review Supabase docs

3. **Collect Debug Information**
   - Browser console errors
   - Network request details
   - Server logs
   - Steps to reproduce

4. **Contact Support**
   - Provide debug information
   - Include environment details
   - Describe expected vs actual behavior

---

**Troubleshooting Guide by VasilMihovCom**

*Solutions for common issues and debugging techniques*

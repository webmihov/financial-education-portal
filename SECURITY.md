# Security Guide

**Author:** VasilMihovCom

Comprehensive security documentation and best practices for the Financial Education Portal.

---

## Table of Contents

1. [Security Overview](#security-overview)
2. [Application Security](#application-security)
3. [Database Security](#database-security)
4. [Authentication Security](#authentication-security)
5. [API Security](#api-security)
6. [Frontend Security](#frontend-security)
7. [Infrastructure Security](#infrastructure-security)
8. [Data Privacy](#data-privacy)
9. [Security Checklist](#security-checklist)
10. [Incident Response](#incident-response)

---

## Security Overview

The Financial Education Portal implements multiple layers of security:

- **Defense in Depth** - Multiple security layers
- **Least Privilege** - Minimal access rights
- **Secure by Default** - Security enabled by default
- **Privacy First** - User data protection
- **Compliance** - GDPR and regulatory adherence

**Security Principles:**

1. Never trust user input
2. Encrypt sensitive data
3. Use strong authentication
4. Implement proper access control
5. Log security events
6. Keep dependencies updated
7. Regular security audits

---

## Application Security

### Environment Variables

**Critical Security Rules:**

**NEVER commit secrets to version control:**

```bash
# .gitignore should include:
.env
.env.local
.env.production
*.key
*.pem
```

**Secure Storage:**

- Development: `.env` file (gitignored)
- Production: Environment variables in hosting platform
- CI/CD: Encrypted secrets in pipeline

**Required Security:**

```env
# .env file should have restricted permissions
chmod 600 .env

# Never expose in client code
# ❌ Bad:
const apiKey = "hardcoded-key";

# ✅ Good:
const apiKey = import.meta.env.VITE_API_KEY;
```

### Input Validation

**Client-Side Validation:**

```typescript
// Validate all user inputs
function validateEmail(email: string): boolean {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}

// Sanitize HTML input
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(dirty);
```

**Server-Side Validation:**

```typescript
// Always validate on server (Edge Functions)
if (!userAgent || typeof userAgent !== 'string') {
  throw new Error('Invalid user agent');
}

// Validate against schema
const schema = {
  type: 'object',
  properties: {
    userAgent: { type: 'string', maxLength: 500 }
  },
  required: ['userAgent']
};
```

### XSS Prevention

**React Auto-Escaping:**

```typescript
// React automatically escapes
const userInput = "<script>alert('xss')</script>";
return <div>{userInput}</div>; // Safe, rendered as text
```

**Dangerous Operations:**

```typescript
// ⚠️ Only use with trusted content
<div dangerouslySetInnerHTML={{ __html: trustedHTML }} />

// ✅ Sanitize first
import DOMPurify from 'dompurify';
const safe = DOMPurify.sanitize(untrustedHTML);
<div dangerouslySetInnerHTML={{ __html: safe }} />
```

### CSRF Protection

**Supabase Automatic Protection:**

Supabase Auth includes CSRF tokens automatically.

**Additional Measures:**

```typescript
// Use Supabase client for all mutations
// It handles CSRF automatically
const { data, error } = await supabase
  .from('table')
  .insert(data);
```

### Content Security Policy (CSP)

**Nginx Header Configuration:**

```nginx
add_header Content-Security-Policy "
    default-src 'self';
    script-src 'self' 'unsafe-inline' https://cdn.trusted.com;
    style-src 'self' 'unsafe-inline';
    img-src 'self' data: https:;
    font-src 'self';
    connect-src 'self' https://*.supabase.co;
    frame-src 'self' https://trusted-iframes.com;
" always;
```

---

## Database Security

### Row-Level Security (RLS)

**Enable RLS on All Tables:**

```sql
-- Enable RLS
ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY "policy_name"
  ON table_name
  FOR SELECT
  TO authenticated
  USING (auth.uid() = user_id);
```

**RLS Best Practices:**

1. **Enable RLS First:** Before inserting data
2. **Restrictive by Default:** Deny all, then allow specific
3. **Use auth.uid():** Never current_user
4. **Test Policies:** Verify with different users
5. **Separate Policies:** One per operation (SELECT, INSERT, UPDATE, DELETE)

**Example Secure Policies:**

```sql
-- Admins can do everything
CREATE POLICY "Admins full access"
  ON content
  TO authenticated
  USING (
    auth.uid() IN (
      SELECT id FROM admin_users
    )
  );

-- Users can only read published content
CREATE POLICY "Users read published"
  ON content
  FOR SELECT
  TO anon, authenticated
  USING (is_published = true);
```

### SQL Injection Prevention

**Supabase Parameterized Queries:**

```typescript
// ✅ Safe - parameterized
const { data } = await supabase
  .from('users')
  .select('*')
  .eq('email', userEmail);

// ❌ Dangerous - string concatenation (Don't do this!)
const query = `SELECT * FROM users WHERE email = '${userEmail}'`;
```

**Edge Functions:**

```typescript
// Use Supabase client (automatically parameterized)
const { data, error } = await supabase
  .from('table')
  .select('*')
  .eq('column', value);
```

### Database Credentials

**Protect Connection Strings:**

```bash
# Never expose database URL
# Use environment variables
SUPABASE_DB_URL=postgresql://...

# Never commit to git
# Add to .gitignore
```

**Service Role Key:**

```typescript
// ⚠️ ONLY use in Edge Functions
// NEVER in client code
const supabase = createClient(
  url,
  process.env.SUPABASE_SERVICE_ROLE_KEY // Server-only!
);
```

### Data Encryption

**At Rest:**
- Supabase encrypts all data at rest
- Database backups are encrypted
- No additional configuration needed

**In Transit:**
- All connections use TLS/SSL
- HTTPS enforced
- Certificate validation

---

## Authentication Security

### Password Security

**Requirements:**

```typescript
// Supabase default password requirements:
// - Minimum 6 characters
// - Recommend: 12+ characters
// - Include uppercase, lowercase, numbers, symbols
```

**Best Practices:**

1. **Never Store Plaintext Passwords**
2. **Use Supabase Auth** (bcrypt hashing)
3. **Implement Rate Limiting**
4. **Require Strong Passwords**
5. **Enable 2FA** (when available)

### Session Management

**Secure Sessions:**

```typescript
// Sessions automatically managed by Supabase
// JWT tokens stored securely
// Auto-refresh before expiration

// Check session
const { data: { session } } = await supabase.auth.getSession();

// Sign out properly
await supabase.auth.signOut();
```

**Session Configuration:**

```sql
-- In Supabase Dashboard → Authentication → Settings
-- JWT expiry: 3600 seconds (1 hour) recommended
-- Refresh token expiry: 604800 seconds (7 days)
```

### Admin Access

**Secure Admin Panel:**

```typescript
// Protected routes
<Route path="/admin/*" element={
  <ProtectedRoute>
    <AdminLayout />
  </ProtectedRoute>
} />

// ProtectedRoute component
function ProtectedRoute({ children }) {
  const [session, setSession] = useState(null);

  useEffect(() => {
    supabase.auth.getSession().then(({ data: { session } }) => {
      setSession(session);
    });
  }, []);

  if (!session) {
    return <Navigate to="/admin" />;
  }

  return children;
}
```

**Audit Logging:**

```sql
-- Track admin actions
CREATE TABLE audit_logs (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id uuid REFERENCES auth.users(id),
  action text NOT NULL,
  table_name text,
  record_id uuid,
  old_data jsonb,
  new_data jsonb,
  timestamp timestamptz DEFAULT now()
);
```

---

## API Security

### Edge Function Security

**CORS Configuration:**

```typescript
// Always include CORS headers
const corsHeaders = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type, Authorization, X-Client-Info, Apikey',
};

// Handle OPTIONS requests
if (req.method === 'OPTIONS') {
  return new Response(null, {
    status: 200,
    headers: corsHeaders,
  });
}
```

**Authentication:**

```typescript
// Verify request is authenticated
const authHeader = req.headers.get('Authorization');
if (!authHeader) {
  return new Response('Unauthorized', { status: 401 });
}

// Use service role key for privileged operations
const supabase = createClient(
  Deno.env.get('SUPABASE_URL')!,
  Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
);
```

### Rate Limiting

**Supabase Built-in:**

- Automatic rate limiting on API
- 100 requests/second default
- Configurable per project

**Custom Rate Limiting:**

```sql
-- Track requests in database
CREATE TABLE rate_limits (
  ip_hash text PRIMARY KEY,
  request_count integer DEFAULT 1,
  window_start timestamptz DEFAULT now()
);

-- Check and update
CREATE FUNCTION check_rate_limit(ip text)
RETURNS boolean AS $$
DECLARE
  current_count integer;
BEGIN
  -- Get current count for this IP in last minute
  SELECT request_count INTO current_count
  FROM rate_limits
  WHERE ip_hash = ip
    AND window_start > now() - interval '1 minute';

  -- If over limit, deny
  IF current_count >= 60 THEN
    RETURN false;
  END IF;

  -- Otherwise, increment and allow
  INSERT INTO rate_limits (ip_hash, request_count)
  VALUES (ip, 1)
  ON CONFLICT (ip_hash) DO UPDATE
  SET request_count = rate_limits.request_count + 1;

  RETURN true;
END;
$$ LANGUAGE plpgsql;
```

---

## Frontend Security

### Secure Headers

**Nginx Security Headers:**

```nginx
# In /etc/nginx/sites-available/your-site

# Prevent clickjacking
add_header X-Frame-Options "SAMEORIGIN" always;

# Prevent MIME sniffing
add_header X-Content-Type-Options "nosniff" always;

# Enable XSS protection
add_header X-XSS-Protection "1; mode=block" always;

# Referrer policy
add_header Referrer-Policy "strict-origin-when-cross-origin" always;

# Permissions policy
add_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always;

# HSTS (after testing)
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

### Dependency Security

**Regular Updates:**

```bash
# Check for vulnerabilities
npm audit

# Fix automatically (if possible)
npm audit fix

# Update dependencies
npm update

# Major version updates
npm outdated
npm install package@latest
```

**Automated Security:**

- Enable Dependabot on GitHub
- Review security advisories
- Test updates in staging first

### Secure Cookies

**Cookie Configuration:**

```typescript
// Supabase handles cookie security
// But if setting custom cookies:
document.cookie = "name=value; Secure; HttpOnly; SameSite=Strict";
```

---

## Infrastructure Security

### Server Hardening

**Firewall Configuration:**

```bash
# Enable UFW
sudo ufw enable

# Allow SSH
sudo ufw allow OpenSSH

# Allow HTTP/HTTPS only
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Deny all other incoming
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

**SSH Security:**

```bash
# Disable root login
sudo nano /etc/ssh/sshd_config
# Set: PermitRootLogin no

# Disable password authentication
# Use SSH keys only
# Set: PasswordAuthentication no

# Restart SSH
sudo systemctl restart sshd
```

**Keep System Updated:**

```bash
# Regular updates
sudo apt update
sudo apt upgrade -y

# Auto-updates (optional)
sudo apt install unattended-upgrades
```

### SSL/TLS Configuration

**Strong SSL Configuration:**

```nginx
# Use modern TLS only
ssl_protocols TLSv1.2 TLSv1.3;

# Strong ciphers
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';

# Prefer server ciphers
ssl_prefer_server_ciphers on;

# SSL session cache
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

**Certificate Management:**

```bash
# Auto-renew with certbot
sudo certbot renew --dry-run

# Set up auto-renewal cron
sudo systemctl enable certbot.timer
```

### Backup Security

**Secure Backups:**

```bash
# Encrypt backups
tar czf - /data | gpg -e -r your@email.com > backup.tar.gz.gpg

# Store off-site
# Use Supabase automatic backups (Pro tier)
# Or S3 with encryption
```

---

## Data Privacy

### GDPR Compliance

**Data Collection:**

- Only collect necessary data
- Get explicit consent (cookie banner)
- Provide privacy policy
- Allow data access requests
- Allow data deletion requests

**IP Address Privacy:**

```typescript
// Hash IPs before storing
async function hashIP(ip: string): Promise<string> {
  const encoder = new TextEncoder();
  const data = encoder.encode(ip);
  const hashBuffer = await crypto.subtle.digest('SHA-256', data);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
}

// Store hash, not raw IP
const ipHash = await hashIP(clientIP);
```

**Data Retention:**

```sql
-- Delete old analytics (90 days)
DELETE FROM lead_gen_analytics
WHERE timestamp < now() - interval '90 days';

-- Run periodically via cron
```

### Cookie Consent

**Implementation:**

```typescript
// Show cookie banner
// Get user consent
// Only load tracking scripts after consent

if (cookieConsent.analytics) {
  // Load analytics scripts
}

if (cookieConsent.marketing) {
  // Load marketing scripts
}
```

---

## Security Checklist

### Development

- [ ] Never commit secrets to git
- [ ] Use environment variables
- [ ] Validate all inputs
- [ ] Sanitize HTML output
- [ ] Enable TypeScript strict mode
- [ ] Run linter regularly
- [ ] Review dependencies
- [ ] Test authentication flows

### Deployment

- [ ] HTTPS enabled
- [ ] Security headers configured
- [ ] RLS enabled on all tables
- [ ] Strong passwords enforced
- [ ] Rate limiting active
- [ ] Backups configured
- [ ] Monitoring enabled
- [ ] Error tracking setup

### Ongoing

- [ ] Regular dependency updates
- [ ] Security audit quarterly
- [ ] Review access logs
- [ ] Test backup restoration
- [ ] Rotate API keys (if needed)
- [ ] Update SSL certificates
- [ ] Review RLS policies
- [ ] Check for vulnerabilities

---

## Incident Response

### Security Incident Plan

**1. Detection:**
- Monitor error logs
- Review suspicious activity
- User reports
- Automated alerts

**2. Containment:**
- Identify affected systems
- Isolate if necessary
- Block malicious IPs
- Revoke compromised keys

**3. Investigation:**
- Analyze logs
- Identify exploit
- Assess damage
- Document findings

**4. Remediation:**
- Patch vulnerability
- Update dependencies
- Change credentials
- Deploy fixes

**5. Recovery:**
- Restore from backups (if needed)
- Verify system integrity
- Monitor for recurrence

**6. Post-Incident:**
- Document incident
- Update procedures
- Implement preventive measures
- Notify stakeholders (if required)

### Contact Information

**Security Issues:**
- Report vulnerabilities privately
- Email: security@yourdomain.com
- Use responsible disclosure

**Emergency Response:**
- Supabase: support@supabase.io
- Hosting provider support
- Security team contact

---

## Additional Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Supabase Security](https://supabase.com/docs/guides/platform/security)
- [MDN Web Security](https://developer.mozilla.org/en-US/docs/Web/Security)
- [GDPR Guidelines](https://gdpr.eu/)

---

**Security Guide by VasilMihovCom**

*Comprehensive security documentation and best practices*

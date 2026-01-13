# Financial Education Portal & Lead Generation Platform

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                                                                           ║
║   ███████╗██╗███╗   ██╗ █████╗ ███╗   ██╗ ██████╗██╗ █████╗ ██╗         ║
║   ██╔════╝██║████╗  ██║██╔══██╗████╗  ██║██╔════╝██║██╔══██╗██║         ║
║   █████╗  ██║██╔██╗ ██║███████║██╔██╗ ██║██║     ██║███████║██║         ║
║   ██╔══╝  ██║██║╚██╗██║██╔══██║██║╚██╗██║██║     ██║██╔══██║██║         ║
║   ██║     ██║██║ ╚████║██║  ██║██║ ╚████║╚██████╗██║██║  ██║███████╗    ║
║   ╚═╝     ╚═╝╚═╝  ╚═══╝╚═╝  ╚═╝╚═╝  ╚═══╝ ╚═════╝╚═╝╚═╝  ╚═╝╚══════╝    ║
║                                                                           ║
║           ███████╗██████╗ ██╗   ██╗ ██████╗ █████╗ ████████╗██╗ ██████╗ ║
║           ██╔════╝██╔══██╗██║   ██║██╔════╝██╔══██╗╚══██╔══╝██║██╔═══██╗║
║           █████╗  ██║  ██║██║   ██║██║     ███████║   ██║   ██║██║   ██║║
║           ██╔══╝  ██║  ██║██║   ██║██║     ██╔══██║   ██║   ██║██║   ██║║
║           ███████╗██████╔╝╚██████╔╝╚██████╗██║  ██║   ██║   ██║╚██████╔╝║
║           ╚══════╝╚═════╝  ╚═════╝  ╚═════╝╚═╝  ╚═╝   ╚═╝   ╚═╝ ╚═════╝ ║
║                                                                           ║
║                          PORTAL & LEAD GEN                                ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

**Author:** VasilMihovCom
**Version:** 1.0.0
**License:** MIT

---

## Overview

A comprehensive **Financial Education Portal** combined with an advanced **Lead Generation System** designed for compliance with Google Ads policies and regulatory requirements. This platform provides educational content about financial services, banking, fintech, and gaming platforms while incorporating sophisticated visitor detection and lead generation capabilities.

### What This Software Does

This web application enables users to:

1. **Access Educational Content** - Browse comprehensive guides about financial platforms, regulatory bodies, payment methods, and consumer protection
2. **Learn About Financial Services** - Understand different platform categories including banking, fintech, gaming (SRIJ), and more
3. **View Compliance Information** - Access risk warnings, regulatory information, and editorial standards
4. **Interact with Lead Generation Pages** - Engage with compliant, Google Ads-approved landing pages
5. **Experience Smart Content Delivery** - Benefit from advanced bot detection and human visitor optimization

### For Administrators

The platform includes a full-featured admin panel where you can:

- Manage educational articles and content
- Configure lead generation campaigns
- Monitor visitor analytics and engagement
- Track bot vs. human visitor patterns
- Manage compliance and regulatory information
- Configure promotion targeting and display rules

---

## Key Features

- **Educational Content Platform** - Multi-category organization with bilingual support (Portuguese/English)
- **Advanced Visitor Detection** - Sophisticated bot detection using fingerprinting, IP analysis, and behavioral scoring
- **Lead Generation System** - Google Ads compliant landing pages with dynamic content delivery
- **Analytics Dashboard** - Comprehensive tracking of visitor behavior, conversions, and engagement
- **Compliance-First Design** - GDPR, cookie consent, and regulatory compliance built-in
- **Admin Panel** - Full content management and campaign configuration
- **Real-time Updates** - Supabase-powered real-time data synchronization
- **Responsive Design** - Mobile-first, accessible UI with Tailwind CSS

---

## Technology Stack

![React](https://img.shields.io/badge/React-18.3-61DAFB?style=flat&logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.5-3178C6?style=flat&logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-5.4-646CFF?style=flat&logo=vite&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-Latest-3ECF8E?style=flat&logo=supabase&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind-3.4-06B6D4?style=flat&logo=tailwindcss&logoColor=white)

**Frontend:**
- React 18.3 with TypeScript
- React Router 7 for routing
- Vite for build tooling
- Tailwind CSS for styling
- Lucide React for icons

**Backend:**
- Supabase (PostgreSQL database)
- Supabase Edge Functions (Deno runtime)
- Row-Level Security (RLS) for data protection
- Real-time subscriptions

**Analytics & Detection:**
- Client-side fingerprinting
- Server-side IP classification
- Behavioral analysis
- Device authenticity scoring

---

## Quick Start

### Prerequisites

- Node.js 18+ and npm
- Supabase account (free tier available)
- Git

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/financial-education-portal.git
cd financial-education-portal

# Install dependencies
npm install

# Copy environment variables
cp .env.example .env

# Configure your Supabase credentials in .env
# VITE_SUPABASE_URL=your_supabase_url
# VITE_SUPABASE_ANON_KEY=your_anon_key

# Run database migrations (via Supabase Dashboard)

# Start development server
npm run dev
```

The application will be available at `http://localhost:5173`

### Build for Production

```bash
# Create production build
npm run build

# Preview production build
npm run preview

# Type checking
npm run typecheck

# Lint code
npm run lint
```

---

## Documentation

Comprehensive documentation is available:

- **[INSTALL.md](./INSTALL.md)** - Complete Digital Ocean deployment guide from GitHub
- **[USER-GUIDE.md](./USER-GUIDE.md)** - End-user documentation and features
- **[ADMIN-GUIDE.md](./ADMIN-GUIDE.md)** - Administrator panel documentation
- **[FEATURES.md](./FEATURES.md)** - Complete feature documentation
- **[API-REFERENCE.md](./API-REFERENCE.md)** - Technical API and database documentation
- **[CONFIGURATION.md](./CONFIGURATION.md)** - Environment and configuration guide
- **[ARCHITECTURE.md](./ARCHITECTURE.md)** - System architecture and design
- **[TROUBLESHOOTING.md](./TROUBLESHOOTING.md)** - Common issues and solutions
- **[SECURITY.md](./SECURITY.md)** - Security best practices
- **[CONTRIBUTING.md](./CONTRIBUTING.md)** - Development and contribution guide
- **[CHANGELOG.md](./CHANGELOG.md)** - Version history and updates

---

## Project Structure

```
financial-education-portal/
├── public/                      # Static assets
│   ├── deposithunter.png       # Logo
│   ├── robots.txt              # SEO
│   └── sitemap.xml             # SEO
├── src/
│   ├── components/             # React components
│   │   ├── admin/              # Admin-specific components
│   │   ├── landing/            # Landing page sections
│   │   ├── leadgen/            # Lead gen promotions
│   │   └── modals/             # Modal dialogs
│   ├── hooks/                  # Custom React hooks
│   ├── lib/                    # Core libraries
│   │   └── supabase.ts         # Supabase client
│   ├── pages/                  # Page components
│   │   └── admin/              # Admin panel pages
│   ├── types/                  # TypeScript definitions
│   ├── utils/                  # Utility functions
│   ├── App.tsx                 # Main app component
│   └── main.tsx                # Entry point
├── supabase/
│   ├── migrations/             # Database migrations
│   └── functions/              # Edge functions
├── docs/                       # Additional documentation
└── package.json                # Dependencies
```

---

## Environment Variables

Create a `.env` file with the following variables:

```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your_anon_key_here
```

See [CONFIGURATION.md](./CONFIGURATION.md) for detailed configuration options.

---

## Core Functionality

### For End Users

1. **Browse Educational Content**
   - Access articles about financial platforms
   - Learn about regulatory bodies and compliance
   - Understand risk warnings and consumer protection
   - View payment method guides

2. **Navigate Landing Pages**
   - Visit internal lead generation pages at `/lp/:slug`
   - Experience optimized content based on visitor type
   - Interact with compliant promotional content

3. **Privacy & Compliance**
   - Manage cookie preferences
   - Review privacy policy and terms
   - Access accessibility features
   - View editorial standards

### For Administrators

1. **Content Management**
   - Create and edit educational articles
   - Manage platform categories
   - Configure regulatory bodies
   - Update risk warnings

2. **Lead Generation**
   - Create landing pages with custom slugs
   - Configure visitor detection settings
   - Set up A/B testing with dual iframes
   - Define targeting rules

3. **Analytics & Monitoring**
   - View visitor statistics
   - Track bot vs. human traffic
   - Monitor conversion rates
   - Analyze detection accuracy

4. **Promotions**
   - Create promotional banners
   - Configure text ads
   - Set targeting rules
   - Track performance

---

## Key Technologies Explained

### Visitor Detection System

The platform includes an advanced visitor detection system that:

- **Identifies bots vs. humans** using multiple detection methods
- **Classifies IP addresses** (residential, mobile, datacenter, VPN)
- **Scores device authenticity** (0-100 scale)
- **Detects headless browsers** and automation tools
- **Caches results** for 24 hours for performance

This ensures compliance with Google Ads policies by preventing invalid traffic.

### Lead Generation System

Lead generation pages (`/lp/:slug`) feature:

- **Dynamic iframe delivery** based on visitor type
- **A/B testing support** with two iframe versions
- **Flexible visibility modes** (all, humans only, bots only)
- **Comprehensive analytics** with visitor classification
- **Real-time updates** without page refresh

### Database Architecture

Built on Supabase with PostgreSQL:

- **Row-Level Security (RLS)** for data protection
- **Real-time subscriptions** for live updates
- **Edge Functions** for server-side logic
- **Automatic migrations** for schema management
- **Privacy-focused** with IP hashing

---

## Security Features

- Environment variable protection (never committed)
- Row-Level Security on all database tables
- Input sanitization and validation
- CSRF protection
- XSS prevention
- Cookie consent management
- GDPR compliance
- IP address hashing for privacy
- Secure admin authentication

See [SECURITY.md](./SECURITY.md) for complete security documentation.

---

## Deployment

### Quick Deploy to Digital Ocean

See [INSTALL.md](./INSTALL.md) for complete step-by-step instructions on deploying from GitHub to a Digital Ocean droplet, including:

- Server setup and configuration
- Nginx reverse proxy setup
- SSL certificate installation
- PM2 process management
- Automatic updates via Git

### Alternative Hosting

The application can be deployed to:

- Vercel (recommended for static sites)
- Netlify
- AWS Amplify
- Any Node.js hosting platform

---

## Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+
- Mobile browsers (iOS Safari, Chrome Mobile)

---

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](./CONTRIBUTING.md) for:

- Development setup
- Code style guidelines
- Pull request process
- Testing requirements
- Documentation standards

---

## Support

### Documentation

- Review the comprehensive guides in the `/docs` folder
- Check [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) for common issues
- Consult [API-REFERENCE.md](./API-REFERENCE.md) for technical details

### Community

- Report bugs via GitHub Issues
- Request features via GitHub Discussions
- Review security guidelines in [SECURITY.md](./SECURITY.md)

---

## License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

---

## Acknowledgments

- Built with [Supabase](https://supabase.com) for backend infrastructure
- UI components styled with [Tailwind CSS](https://tailwindcss.com)
- Icons from [Lucide](https://lucide.dev)
- Powered by [Vite](https://vitejs.dev) build tool

---

## Author

**VasilMihovCom**

For professional web development services, custom implementations, or enterprise support, please contact the author.

---

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for a detailed history of changes and updates.

---

**Built with care for educational purposes and compliance with regulatory requirements.**

```
╔════════════════════════════════════════╗
║  Making Financial Education Accessible ║
╚════════════════════════════════════════╝
```

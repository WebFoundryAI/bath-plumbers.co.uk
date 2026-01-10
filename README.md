# Local Plumber Website

A professional, SEO-optimized static website for local plumbing services. Built with pure HTML/CSS for maximum performance and GitHub Pages compatibility.

## 📁 Project Structure

```
/
├── pages/              # HTML pages (services, areas, about, contact, etc.)
├── images/             # Site images, logos, and graphics
├── data/               # JSON data files for dynamic content
├── seo/                # SEO assets (meta templates, keywords)
├── schemas/            # JSON-LD schema markup templates
├── assets/
│   └── css/           # Stylesheets
│       └── style.css  # Main stylesheet
├── index.html          # Homepage
├── sitemap.xml         # XML sitemap for search engines
├── robots.txt          # Search engine crawling rules
└── README.md           # This file
```

## 🚀 Quick Start

### Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/WebFoundryAI/bath-plumbers.co.uk.git
   cd bath-plumbers.co.uk
   ```

2. Open in browser:
   - Simply open `index.html` in your browser
   - Or use a local server:
     ```bash
     # Python 3
     python -m http.server 8000

     # Node.js (npx)
     npx serve .
     ```

3. Access at `http://localhost:8000`

### Deploy to GitHub Pages

1. Go to repository **Settings** > **Pages**
2. Under "Source", select **Deploy from a branch**
3. Select **main** branch and **/ (root)** folder
4. Click **Save**
5. Site will be live at: `https://webfoundryai.github.io/bath-plumbers.co.uk/`

### Custom Domain Setup

1. In repository **Settings** > **Pages**
2. Enter your custom domain (e.g., `yourdomain.co.uk`)
3. Check "Enforce HTTPS"
4. Add DNS records at your registrar:
   ```
   Type: CNAME
   Name: www
   Value: webfoundryai.github.io

   Type: A
   Name: @
   Values: 185.199.108.153
           185.199.109.153
           185.199.110.153
           185.199.111.153
   ```

## 📝 Customization Checklist

Before deploying, update the following:

### Required Updates

- [ ] **index.html**
  - Update `<title>` tag with business name
  - Update `<meta name="description">` with unique description
  - Update canonical URL in `<link rel="canonical">`
  - Update Open Graph meta tags
  - Add phone number to Schema.org JSON-LD
  - Add business address to Schema.org JSON-LD
  - Update all CTA phone links

- [ ] **sitemap.xml**
  - Replace `example.com` with actual domain
  - Update `<lastmod>` dates

- [ ] **robots.txt**
  - Replace `example.com` with actual domain

- [ ] **All pages in /pages/**
  - Update meta descriptions
  - Add location-specific content
  - Update contact information

### Recommended Updates

- [ ] Add favicon to `/images/favicon.ico`
- [ ] Add Open Graph image to `/images/og-image.jpg` (1200x630px)
- [ ] Add business photos to `/images/`
- [ ] Update color scheme in `assets/css/style.css` (CSS variables)
- [ ] Add Google Analytics tracking code
- [ ] Add schema markup for services in `/schemas/`

## 🎨 Customizing Styles

Edit CSS variables in `assets/css/style.css`:

```css
:root {
    --primary-color: #0066cc;      /* Main brand color */
    --primary-dark: #004999;       /* Darker shade */
    --secondary-color: #ff6600;    /* CTA/accent color */
    --text-color: #333333;         /* Body text */
    --text-light: #666666;         /* Secondary text */
    --background-light: #f5f5f5;   /* Light backgrounds */
}
```

## 📊 SEO Features

- Semantic HTML5 structure
- Schema.org LocalBusiness markup
- Open Graph meta tags
- XML sitemap
- Robots.txt configuration
- Mobile-responsive design
- Fast loading (no frameworks)
- Clean URL structure

## 📱 Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)
- Mobile browsers (iOS Safari, Chrome for Android)

## 📄 License

This project is proprietary. All rights reserved.

---

**Need help?** Contact the WebFoundryAI team for support.

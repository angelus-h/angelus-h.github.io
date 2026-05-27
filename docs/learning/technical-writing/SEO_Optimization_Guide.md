# SEO Optimization Guide

**Complete guide to Search Engine Optimization for technical documentation sites**

---

## What is SEO?

**SEO (Search Engine Optimization)** is the practice of improving your website's visibility in search engine results (Google, Bing, DuckDuckGo).

**Why it matters:**
- **Discoverability** - People can find your content when searching for solutions
- **Traffic** - More organic visitors without paid advertising
- **Authority** - Higher rankings establish credibility
- **Impact** - Your knowledge reaches more people who need it

**How search engines work:**
1. **Crawling** - Bots discover and read your pages (following links, sitemaps)
2. **Indexing** - Pages are stored in the search engine's database
3. **Ranking** - Algorithms determine which results to show for each query

---

## SEO Categories

### 1. Technical SEO
Infrastructure and backend optimization that helps search engines crawl and understand your site.

### 2. On-Page SEO
Content and HTML optimization on individual pages.

### 3. Off-Page SEO
External factors like backlinks and social signals that indicate authority.

---

## Technical SEO

### robots.txt

**Purpose:** Tells search engines which parts of your site to crawl.

**Location:** `/robots.txt` (root of your domain)

**Basic structure:**
```txt
# Allow all crawlers
User-agent: *
Allow: /

# Sitemap location
Sitemap: https://yourdomain.com/sitemap.xml

# Optional: Crawl delay (prevents aggressive crawling)
Crawl-delay: 1
```

**Common directives:**
- `User-agent: *` - Applies to all search engines
- `Allow: /` - Allow crawling all pages
- `Disallow: /admin/` - Block specific paths
- `Sitemap:` - Location of your sitemap

**Best practices:**
- Allow search engines to crawl public content
- Block admin panels, private sections
- Include sitemap URL
- Don't block CSS/JS files (Google needs them to render pages)

---

### Sitemap

**Purpose:** XML file listing all your URLs for search engines to discover.

**MkDocs:** Automatically generates `sitemap.xml` during build.

**Example structure:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
<url>
<loc>https://yourdomain.com/</loc>
<lastmod>2026-05-25</lastmod>
<changefreq>weekly</changefreq>
<priority>1.0</priority>
</url>
<url>
<loc>https://yourdomain.com/guide/</loc>
<lastmod>2026-05-24</lastmod>
<changefreq>monthly</changefreq>
<priority>0.8</priority>
</url>
</urlset>
```

**Submission:**
1. Build your site (MkDocs generates sitemap automatically)
2. Submit to Google Search Console: Sitemaps → Add sitemap
3. URL: `https://yourdomain.com/sitemap.xml`

**Best practices:**
- Keep sitemap updated (MkDocs does this automatically)
- Submit to Google Search Console
- Include all public pages
- Don't include 404 pages or redirects

---

### Meta Tags

**Purpose:** HTML tags in `<head>` that describe your page to search engines.

#### Essential Meta Tags

**1. Meta Description (most important)**
```html
<meta name="description" content="Concise summary of page content (50-160 characters)">
```

**Why it matters:**
- Appears in Google search results below the title
- Affects click-through rate (CTR)
- Should be unique for each page

**Good description:**
- 50-160 characters (longer gets truncated)
- Includes primary keyword naturally
- Compelling, actionable language
- Accurately summarizes content

**Example:**
```markdown
---
description: "Complete guide to Kubernetes monitoring with Prometheus, Grafana, and AlertManager for production SRE workflows."
---
```

**2. Meta Keywords (legacy, less important)**
```html
<meta name="keywords" content="SEO, DevOps, Kubernetes, Python">
```

**Status:** Google ignores this, but other search engines may use it.

**3. Meta Robots**
```html
<meta name="robots" content="index, follow">
```

**Directives:**
- `index` - Allow indexing this page
- `noindex` - Don't index this page
- `follow` - Follow links on this page
- `nofollow` - Don't follow links on this page

**4. Canonical URL**
```html
<link rel="canonical" href="https://yourdomain.com/original-page/">
```

**Purpose:** Prevents duplicate content issues when the same content exists at multiple URLs.

---

### Structured Data (Schema.org)

**Purpose:** JSON-LD markup that helps search engines understand your content semantically.

**Benefits:**
- Rich snippets in search results (star ratings, images, breadcrumbs)
- Knowledge Graph integration
- Better understanding of content type

#### WebSite Schema

**Purpose:** Describes your website as a whole.

```html
<script type="application/ld+json">
{
"@context": "https://schema.org",
"@type": "WebSite",
"name": "Your Site Name",
"url": "https://yourdomain.com",
"description": "Site description",
"author": {
"@type": "Person",
"name": "Your Name",
"url": "https://github.com/yourname"
},
"potentialAction": {
"@type": "SearchAction",
"target": "https://yourdomain.com/?q={search_term_string}",
"query-input": "required name=search_term_string"
}
}
</script>
```

#### TechArticle Schema

**Purpose:** Describes technical articles/documentation.

```html
<script type="application/ld+json">
{
"@context": "https://schema.org",
"@type": "TechArticle",
"headline": "Article Title",
"url": "https://yourdomain.com/article/",
"datePublished": "2026-05-25",
"dateModified": "2026-05-25",
"author": {
"@type": "Person",
"name": "Your Name"
},
"publisher": {
"@type": "Person",
"name": "Your Name"
},
"description": "Article description"
}
</script>
```

#### Other Schema Types

- `HowTo` - Step-by-step guides
- `FAQPage` - Frequently asked questions
- `BreadcrumbList` - Navigation breadcrumbs
- `Organization` - Company/organization info

**Testing:**
- Google Rich Results Test: https://search.google.com/test/rich-results
- Schema.org Validator: https://validator.schema.org/

---

### Open Graph (Social Media)

**Purpose:** Controls how your pages appear when shared on Facebook, LinkedIn, Discord.

**Essential tags:**
```html
<meta property="og:site_name" content="Site Name">
<meta property="og:type" content="website">
<meta property="og:url" content="https://yourdomain.com/page/">
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Page description">
<meta property="og:image" content="https://yourdomain.com/image.jpg">
<meta property="og:locale" content="en_US">
```

**Image recommendations:**
- Minimum: 1200×630 pixels
- Aspect ratio: 1.91:1
- Format: JPG or PNG
- File size: Under 8 MB

**Testing:**
- Facebook Sharing Debugger: https://developers.facebook.com/tools/debug/
- LinkedIn Post Inspector: https://www.linkedin.com/post-inspector/

---

### Twitter Cards

**Purpose:** Rich previews when sharing on Twitter/X.

**Essential tags:**
```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@yourusername">
<meta name="twitter:creator" content="@yourusername">
<meta name="twitter:title" content="Page Title">
<meta name="twitter:description" content="Page description">
<meta name="twitter:image" content="https://yourdomain.com/image.jpg">
```

**Card types:**
- `summary` - Title, description, thumbnail
- `summary_large_image` - Title, description, large image
- `app` - Mobile app info
- `player` - Video/audio player

**Testing:**
- Twitter Card Validator: https://cards-dev.twitter.com/validator

---

## On-Page SEO

### Title Tags

**Purpose:** The clickable headline in search results.

**Format:**
```html
<title>Page Title - Site Name</title>
```

**Best practices:**
- 50-60 characters (longer gets truncated)
- Include primary keyword near the beginning
- Make it compelling (users decide whether to click)
- Unique for each page
- Front-load important words

**Examples:**

** Bad:**
```
Home | My Site
```

** Good:**
```
Kubernetes Monitoring Guide - Complete Prometheus Setup | Site Name
```

**MkDocs:** Automatically generates titles from page headings and site name.

---

### Headings (H1-H6)

**Purpose:** Structure content hierarchically for both users and search engines.

**Best practices:**
- One `<h1>` per page (the main heading)
- Use `<h2>` for major sections
- Use `<h3>` for subsections
- Don't skip heading levels (H1 → H3 without H2)
- Include keywords naturally

**Example structure:**
```markdown
# Main Topic (H1)

## Section 1 (H2)

### Subsection 1.1 (H3)

### Subsection 1.2 (H3)

## Section 2 (H2)
```

**Why it matters:**
- Google uses headings to understand page structure
- Featured snippets often pull from H2/H3 sections
- Improves accessibility (screen readers)

---

### Content Optimization

#### Keyword Research

**Purpose:** Find what people are actually searching for.

**Tools:**
- Google Search Console (see what you already rank for)
- Google Autocomplete (type into search bar, see suggestions)
- "People also ask" boxes
- Related searches at bottom of Google results
- Keyword research tools (Ubersuggest, Ahrefs, SEMrush)

**Strategy:**
1. Identify your topic (e.g., "Kubernetes monitoring")
2. Find related searches (e.g., "Prometheus Kubernetes tutorial")
3. Check search volume and difficulty
4. Target long-tail keywords (more specific = easier to rank)

**Long-tail keywords:**
- "Python" (too broad, impossible to rank)
- "Python SRE interview questions" (specific, achievable)

#### Content Quality

**What Google rewards:**
- **Comprehensive** - Covers topic thoroughly
- **Original** - Unique insights, not rehashed content
- **Accurate** - Factually correct, authoritative
- **Well-written** - Clear, organized, easy to read
- **Updated** - Fresh content, maintained over time

**E-A-T principle (Google's quality framework):**
- **Expertise** - Author knows the subject
- **Authoritativeness** - Site is recognized in the field
- **Trustworthiness** - Accurate, transparent, secure (HTTPS)

#### Keyword Placement

**Strategic locations:**
1. **Title tag** (most important)
2. **H1 heading**
3. **First paragraph** (first 100 words)
4. **H2/H3 subheadings**
5. **Image alt text**
6. **Meta description**
7. **URL slug**

**Density:**
- Natural language (don't "keyword stuff")
- 1-2% density is fine (if article is 1000 words, keyword appears 10-20 times)
- Use variations and synonyms

**Example:**
```markdown
# Kubernetes Monitoring with Prometheus (keyword in H1)

Learn how to implement Kubernetes monitoring using Prometheus... (keyword in first paragraph)

## Setting Up Prometheus on Kubernetes (variation in H2)

![Prometheus architecture diagram](prometheus-architecture.png) (keyword in alt text)
```

---

### URL Structure

**Best practices:**
- Short, descriptive URLs
- Include primary keyword
- Use hyphens (not underscores) to separate words
- Lowercase only
- Avoid special characters, dates, numbers

**Examples:**

** Bad:**
```
https://site.com/page?id=12345
https://site.com/2026/05/25/my-blog-post-about-kubernetes-monitoring-and-prometheus-setup
```

** Good:**
```
https://site.com/kubernetes-monitoring-prometheus/
https://site.com/guides/prometheus-setup/
```

**MkDocs:** URL structure follows your file organization automatically.

---

### Internal Linking

**Purpose:** Help users and search engines discover related content.

**Benefits:**
- Distributes "link equity" (ranking power) across your site
- Helps Google understand site structure
- Keeps users engaged (lower bounce rate)
- Establishes topical authority

**Best practices:**
- Link to related pages naturally in content
- Use descriptive anchor text (not "click here")
- Link to both newer and older content
- Create hub pages that link to related articles

**Example:**

** Bad:**
```markdown
For more information, click [here](link).
```

** Good:**
```markdown
Learn more about [Prometheus configuration](../prometheus-config/) and [AlertManager setup](../alertmanager/).
```

---

### Images

#### Alt Text

**Purpose:** Describes images for screen readers and search engines.

**Format:**
```markdown
![Descriptive alt text including keyword](image.png)
```

**Best practices:**
- Describe the image content accurately
- Include keyword naturally (if relevant)
- Keep it concise (125 characters or less)
- Don't start with "image of" or "picture of"

**Examples:**

** Bad:**
```markdown
![img123](diagram.png)
![Click here](screenshot.png)
```

** Good:**
```markdown
![Prometheus architecture with Grafana dashboard](prometheus-architecture.png)
![Kubernetes pod deployment workflow diagram](k8s-deployment.svg)
```

#### Image Optimization

**Performance:**
- Compress images (use tools like TinyPNG, ImageOptim)
- Use appropriate formats (WebP for photos, SVG for diagrams)
- Responsive images (serve different sizes for mobile/desktop)
- Lazy loading (load images as user scrolls)

**File naming:**
- Descriptive filenames with keywords
- Use hyphens (not underscores)
- Lowercase only

**Examples:**

** Bad:**
```
IMG_1234.jpg
Screenshot_2026_05_25.png
```

** Good:**
```
prometheus-grafana-dashboard.jpg
kubernetes-architecture-diagram.svg
```

---

## Off-Page SEO

### Backlinks

**What they are:** Links from other websites to your content.

**Why they matter:**
- Google's original ranking algorithm was built on backlinks
- Quality backlinks = votes of confidence
- Drive referral traffic
- Establish authority in your field

**Quality factors:**
- **Domain authority** - Links from high-authority sites (university, government, major publications)
- **Relevance** - Links from related topics/industries
- **Anchor text** - The clickable text (should be descriptive)
- **Follow vs. nofollow** - `rel="nofollow"` links don't pass ranking power

**How to get backlinks:**

1. **Create valuable content** - The best backlink strategy is content worth linking to
2. **Guest posting** - Write articles for other sites in your niche
3. **Broken link building** - Find broken links on other sites, offer your content as replacement
4. **Community participation** - Reddit, Stack Overflow, dev.to (where appropriate, not spammy)
5. **Social sharing** - LinkedIn, Twitter/X posts can lead to organic backlinks
6. **Open source contributions** - GitHub repos, documentation, tool mentions

**Red flags (avoid):**
- Link farms, paid link schemes
- Irrelevant directory submissions
- Comment spam
- Automated link building

---

### Social Signals

**What they are:** Social media engagement (shares, likes, mentions).

**Impact on SEO:**
- Not a direct ranking factor (Google denies this)
- Indirect benefits: visibility → backlinks → rankings
- Builds brand awareness and authority

**Strategy:**
- Share content on LinkedIn (professional audience)
- Reddit posts in relevant communities (r/devops, r/sre, r/kubernetes)
- Twitter/X threads with key insights
- Dev.to cross-posting

---

## SEO Tools

### Google Search Console

**Purpose:** Monitor how Google sees your site.

**Setup:**
1. Add property: https://yourdomain.com
2. Verify ownership (HTML file upload method for GitHub Pages)
3. Submit sitemap: `https://yourdomain.com/sitemap.xml`

**Key features:**
- **Performance** - Impressions, clicks, CTR, average position
- **Coverage** - Which pages are indexed, crawl errors
- **URL Inspection** - Check how Google sees specific pages
- **Sitemaps** - Submit and monitor sitemap status
- **Manual Actions** - Penalties or issues flagged by Google

**Best practices:**
- Check weekly for errors
- Request indexing for new/updated pages
- Monitor search queries (see what you rank for)
- Fix coverage issues (4xx errors, redirect loops)

---

### Google Analytics

**Purpose:** Track visitor behavior and traffic sources.

**Key metrics:**
- **Users** - Unique visitors
- **Sessions** - Visits to your site
- **Pageviews** - Total pages viewed
- **Bounce rate** - % who leave after one page
- **Average session duration** - How long users stay
- **Traffic sources** - Organic search, direct, referral, social

**Setup:**
1. Create Google Analytics 4 property
2. Get Measurement ID (G-XXXXXXXXXX)
3. Add to MkDocs config:

```yaml
extra:
analytics:
provider: google
property: G-XXXXXXXXXX
```

**Privacy considerations:**
- GA4 uses cookies (requires consent in EU)
- Consider privacy-friendly alternatives (Plausible, Fathom, Cloudflare Analytics)

---

### Other Tools

**Keyword Research:**
- Ubersuggest (free tier available)
- Google Keyword Planner (free with Google Ads account)
- Answer The Public (question-based keywords)

**Site Audits:**
- Screaming Frog SEO Spider (desktop tool for crawling)
- Lighthouse (built into Chrome DevTools - performance, SEO, accessibility)
- PageSpeed Insights (Google's speed test)

**Backlink Analysis:**
- Ahrefs (paid, industry standard)
- SEMrush (paid, comprehensive)
- Moz Link Explorer (free tier available)

**Structured Data Testing:**
- Google Rich Results Test
- Schema.org Validator

---

## MkDocs-Specific Implementation

### SEO Plugins

#### 1. mkdocs-meta-descriptions-plugin

**Installation:**
```bash
pip install mkdocs-meta-descriptions-plugin
```

**Configuration:**
```yaml
plugins:
- meta-descriptions:
export_csv: false
quiet: false
enable_checks: true
min_length: 50
max_length: 160
```

**What it does:**
- Automatically generates meta descriptions from first paragraph
- Warns if descriptions are too short/long
- Allows manual override via frontmatter

**Manual override:**
```markdown
---
description: "Custom meta description for this specific page (50-160 characters)."
---

# Page Title
```

---

### Custom Templates (Overrides)

**Create overrides directory:**
```bash
mkdir -p docs/overrides
```

**Enable in mkdocs.yml:**
```yaml
theme:
name: material
custom_dir: docs/overrides
```

**Create `docs/overrides/main.html`:**
```html
{% extends "base.html" %}

{% block extrahead %}
<!-- SEO Meta Tags -->
<meta name="keywords" content="Your, Keywords, Here">
<meta name="robots" content="index, follow">
<meta name="author" content="Your Name">

<!-- Open Graph Tags -->
{% if page %}
<meta property="og:url" content="{{ page.canonical_url }}">
<meta property="og:title" content="{{ page.title }} - {{ config.site_name }}">
<meta property="og:description" content="{{ config.site_description }}">
<meta property="og:image" content="{{ config.site_url }}assets/image.jpg">
{% endif %}

<!-- Twitter Cards -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="{{ page.title }} - {{ config.site_name }}">
<meta name="twitter:description" content="{{ config.site_description }}">
<meta name="twitter:image" content="{{ config.site_url }}assets/image.jpg">

<!-- Structured Data (JSON-LD) -->
<script type="application/ld+json">
{
"@context": "https://schema.org",
"@type": "WebSite",
"name": "{{ config.site_name }}",
"url": "{{ config.site_url }}",
"description": "{{ config.site_description }}"
}
</script>
{% endblock %}
```

---

### robots.txt

**Create `docs/robots.txt`:**
```txt
User-agent: *
Allow: /

Sitemap: https://yourdomain.com/sitemap.xml
```

**MkDocs automatically includes this in the build.**

---

### Sitemap Configuration

**MkDocs generates sitemap.xml automatically.**

**Optional configuration in mkdocs.yml:**
```yaml
plugins:
- search
- sitemap:
changefreq: weekly
```

**Sitemap priorities:**
- Home page: 1.0
- Main sections: 0.8
- Individual pages: 0.6

---

## Best Practices

### Do's

**Write for humans first, search engines second**
- Quality content attracts backlinks naturally
- User engagement signals (time on page, bounce rate) affect rankings

**Update content regularly**
- Google favors fresh content
- Update dates in frontmatter when you revise pages

**Use descriptive, keyword-rich URLs**
- `/kubernetes-monitoring/` not `/page123/`

**Optimize page speed**
- Compress images
- Minimize CSS/JS
- Use CDN for assets

**Make your site mobile-friendly**
- Responsive design (Material for MkDocs handles this)
- Google uses mobile-first indexing

**Use HTTPS**
- GitHub Pages provides HTTPS automatically
- Google prefers secure sites

**Create comprehensive content**
- Long-form guides (2000+ words) rank better
- Cover topics thoroughly

**Build internal links**
- Link related pages naturally
- Create hub pages linking to related content

---

### Don'ts

**Don't keyword stuff**
- Natural language always
- Google penalizes over-optimization

**Don't duplicate content**
- Same content at multiple URLs hurts rankings
- Use canonical tags if unavoidable

**Don't buy links**
- Violates Google's guidelines
- Risk of manual penalty

**Don't hide text or links**
- White text on white background = spam
- Google penalizes cloaking

**Don't use spammy tactics**
- Comment spam
- Link farms
- Automated content generation

**Don't ignore mobile users**
- 60%+ of searches are mobile
- Google prioritizes mobile experience

**Don't neglect page speed**
- Slow sites rank lower
- Users abandon slow pages

---

## Common Mistakes

### 1. Missing or Duplicate Titles/Descriptions

**Problem:** Every page has the same title or no meta description.

**Fix:**
- Unique title for each page
- Use frontmatter to set custom descriptions:

```markdown
---
title: Unique Page Title
description: Unique description for this specific page.
---
```

---

### 2. Thin Content

**Problem:** Pages with minimal content (< 300 words).

**Fix:**
- Expand pages to 500-2000+ words
- Cover topics comprehensively
- Add examples, code snippets, diagrams

---

### 3. Broken Links

**Problem:** Links to 404 pages or external sites that no longer exist.

**Fix:**
- Use MkDocs link checking:
```bash
mkdocs build --strict # Fails build on broken internal links
```
- Check external links periodically
- Fix or remove broken links

---

### 4. No Internal Linking

**Problem:** Pages exist in isolation with no connections.

**Fix:**
- Add related links sections
- Link naturally in content
- Create hub/index pages linking to related content

---

### 5. Ignoring Analytics

**Problem:** No tracking means you don't know what's working.

**Fix:**
- Set up Google Search Console (minimum)
- Monitor which pages get traffic
- See what search queries bring users
- Double down on what works

---

## Timeline & Expectations

### Realistic SEO Timeline

| Timeframe | What to Expect |
|-----------|----------------|
| **Week 1** | Submit sitemap, request indexing |
| **Week 2-3** | First pages indexed (check with `site:yourdomain.com`) |
| **Month 1** | Basic indexing complete (50-70% of pages) |
| **Month 2-3** | Rankings improve for long-tail keywords |
| **Month 4-6** | Noticeable organic traffic increase |
| **Month 6-12** | Established rankings, steady traffic growth |
| **Year 1+** | Authority builds, rankings stabilize |

### Factors Affecting Timeline

**Faster indexing:**
- Submitting sitemap to Search Console
- Building backlinks early
- Regular content updates
- Social sharing

**Slower indexing:**
- Brand new domain (no authority)
- No backlinks
- Infrequent updates
- Technical issues (crawl errors, slow speed)

---

## Measuring Success

### Key Metrics

**1. Organic Traffic**
- Users from search engines (Google, Bing)
- Track in Google Analytics: Acquisition → All Traffic → Channels → Organic Search

**2. Keyword Rankings**
- Position in search results for target keywords
- Track in Google Search Console: Performance → Queries

**3. Impressions & Clicks**
- How often you appear in search results (impressions)
- How often users click (clicks)
- CTR (Click-Through Rate) = Clicks ÷ Impressions

**4. Indexing Coverage**
- How many pages are indexed
- Track in Google Search Console: Coverage → Valid

**5. Backlinks**
- Number and quality of sites linking to you
- Track with Ahrefs, SEMrush, or Google Search Console (Links report)

**6. Page Speed**
- Core Web Vitals (LCP, FID, CLS)
- Track with PageSpeed Insights, Lighthouse

---

## Continuous Improvement

### Monthly Tasks

- [ ] Check Google Search Console for errors
- [ ] Review top-performing pages (double down on what works)
- [ ] Find and fix broken links
- [ ] Update outdated content
- [ ] Monitor keyword rankings
- [ ] Review backlink profile

### Quarterly Tasks

- [ ] Content audit (remove or improve low-quality pages)
- [ ] Competitive analysis (what are others ranking for?)
- [ ] Technical SEO audit (page speed, mobile-friendliness)
- [ ] Backlink outreach campaign
- [ ] Update meta descriptions for top pages

### Annual Tasks

- [ ] Full site audit (comprehensive technical review)
- [ ] Content strategy refresh
- [ ] Analyze year-over-year growth
- [ ] Set new SEO goals for next year

---

## Resources

### Official Documentation

- **Google Search Central:** https://developers.google.com/search
- **Google Search Console Help:** https://support.google.com/webmasters
- **Schema.org:** https://schema.org/
- **Open Graph Protocol:** https://ogp.me/
- **Twitter Cards:** https://developer.twitter.com/en/docs/twitter-for-websites/cards

### Tools

- **Google Search Console:** https://search.google.com/search-console
- **Google Analytics:** https://analytics.google.com/
- **PageSpeed Insights:** https://pagespeed.web.dev/
- **Rich Results Test:** https://search.google.com/test/rich-results
- **Mobile-Friendly Test:** https://search.google.com/test/mobile-friendly

### Learning Resources

- **Google SEO Starter Guide:** https://developers.google.com/search/docs/fundamentals/seo-starter-guide
- **Moz Beginner's Guide to SEO:** https://moz.com/beginners-guide-to-seo
- **Ahrefs Blog:** https://ahrefs.com/blog/
- **Search Engine Journal:** https://www.searchenginejournal.com/

---

## Checklist: SEO Optimization for New Site

### Pre-Launch

- [ ] Set up `robots.txt` (allow crawling)
- [ ] Verify sitemap.xml is generated
- [ ] Add meta descriptions to all pages
- [ ] Optimize page titles (unique, keyword-rich)
- [ ] Add structured data (JSON-LD)
- [ ] Implement Open Graph tags
- [ ] Add Twitter Card tags
- [ ] Optimize images (alt text, compression, descriptive filenames)
- [ ] Create internal linking structure
- [ ] Test mobile responsiveness
- [ ] Check page speed (Lighthouse)
- [ ] Enable HTTPS

### Post-Launch (Week 1)

- [ ] Submit to Google Search Console
- [ ] Verify ownership
- [ ] Submit sitemap
- [ ] Request indexing for key pages
- [ ] Set up Google Analytics (optional)
- [ ] Share on social media (LinkedIn, Twitter/X)
- [ ] Post on relevant communities (Reddit, dev.to)

### Ongoing Maintenance

- [ ] Weekly: Check Search Console for errors
- [ ] Monthly: Review analytics, update content
- [ ] Quarterly: Content audit, competitive analysis
- [ ] Annually: Full SEO audit

---

*This guide covers the fundamentals of SEO specifically for technical documentation sites built with MkDocs. Focus on creating high-quality content first, then optimize for search engines. SEO is a long-term investment that compounds over time.*

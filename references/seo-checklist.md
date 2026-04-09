# SEO Checklist for Webflow Sites

## On-Page Essentials

### Meta Tags
- [ ] Unique meta title on every page (under 60 characters, include primary keyword)
- [ ] Unique meta description on every page (under 160 characters, include CTA or value prop)
- [ ] Do not duplicate meta titles or descriptions across pages

### URL Structure
- [ ] Clean URL slugs: lowercase, hyphenated, no dates or IDs
- [ ] Keep slugs short and descriptive: `/tjenester/webflow-utvikling` not `/tjenester/vare-webflow-utviklingstjenester-2026`
- [ ] Never change published URLs without setting up 301 redirects
- [ ] Remove trailing slashes for consistency

### Heading Hierarchy
- [ ] Exactly one H1 per page (should contain primary keyword)
- [ ] Logical H2-H6 structure (never skip levels, e.g., H1 > H3)
- [ ] Headings describe content structure, not just visual size
- [ ] Use heading-style utility classes to decouple visual size from semantic level

### Images
- [ ] Alt text on every image (descriptive, not keyword-stuffed)
- [ ] Decorative images: set alt to empty string, not missing
- [ ] File names descriptive before upload: `team-christopher.webp` not `IMG_4382.png`
- [ ] Use Webflow responsive images (srcset) for performance

### Internal Linking
- [ ] All pages reachable within 3 clicks from homepage
- [ ] Use descriptive anchor text (not "click here" or "read more")
- [ ] Link to related content naturally within body text
- [ ] Ensure navigation covers all key pages

## Technical SEO

### Sitemap
- [ ] Enable auto-generated sitemap in Webflow project settings
- [ ] Verify sitemap is accessible at `/sitemap.xml`
- [ ] Exclude utility pages (style guide, password-protected pages) from sitemap
- [ ] Submit sitemap to Google Search Console and Bing Webmaster Tools

### Robots.txt
- [ ] Enable search engine crawlers in Webflow settings
- [ ] Enable AI bot crawling (GPTBot, ClaudeBot, PerplexityBot) for AEO/GEO visibility
- [ ] Block staging/development subdomains from crawling
- [ ] Do not block CSS or JavaScript files

### Canonical URLs
- [ ] Set canonical URL on all pages (Webflow does this by default for published pages)
- [ ] On CMS template pages, ensure canonical points to the correct URL
- [ ] If content exists on multiple URLs, canonical should point to the preferred version

### 301 Redirects
- [ ] Set up 301 redirects for all changed URLs
- [ ] Redirect old domain to new domain if migrating
- [ ] Redirect HTTP to HTTPS (Webflow handles this automatically)
- [ ] Check for redirect chains (redirect A > B > C should be A > C)

### Page Speed
- [ ] Optimize images (WebP, proper sizing)
- [ ] Minimize custom JavaScript
- [ ] Defer non-critical scripts
- [ ] Limit web fonts to 2 families, 3-4 weights
- [ ] Test with Google PageSpeed Insights, aim for 90+ on mobile

## Structured Data (Schema Markup)

Add schema markup via custom code embed in page head or before-body:

### Common Schema Types
- [ ] **LocalBusiness**: For company/agency pages (name, address, phone, hours)
- [ ] **Organization**: For about pages (name, logo, social profiles, founding date)
- [ ] **Article / BlogPosting**: For blog posts (headline, author, datePublished, image)
- [ ] **Product**: For product pages (name, price, availability, reviews)
- [ ] **FAQPage**: For FAQ sections (question/answer pairs)
- [ ] **BreadcrumbList**: For navigation breadcrumbs
- [ ] **WebSite**: For homepage (name, URL, search action if applicable)

### Schema Best Practices
- Use JSON-LD format (recommended by Google)
- Validate with Google Rich Results Test
- Keep data accurate and matching visible page content
- Update schema when content changes

## Social Sharing (Open Graph)

- [ ] OG title set on all pages (can differ from meta title)
- [ ] OG description set on all pages
- [ ] OG image set on all pages (1200x630px recommended)
- [ ] OG type set (website for pages, article for blog posts)
- [ ] Twitter card tags set (summary_large_image for most pages)
- [ ] Test with Facebook Sharing Debugger and Twitter Card Validator

## Answer Engine Optimization (AEO)

Optimize for AI-powered search engines (ChatGPT, Perplexity, Claude) that cite web sources.

### Content Structure for AI Citation
- [ ] Lead with clear, direct answers to likely questions
- [ ] Use question-format headings where natural ("Hva koster Webflow utvikling?")
- [ ] Provide specific, factual data (numbers, dates, prices) that AI can cite
- [ ] Structure content in scannable chunks (short paragraphs, clear headers)
- [ ] Include summary/key takeaway sections

### Technical AEO
- [ ] Allow AI bot crawling in robots.txt (GPTBot, ClaudeBot, PerplexityBot)
- [ ] Use schema markup to give AI engines structured data
- [ ] Ensure fast page load (AI crawlers have timeouts)
- [ ] Keep content accessible without JavaScript rendering

## Generative Engine Optimization (GEO)

Optimize for visibility in AI-generated responses and summaries.

### GEO Best Practices
- [ ] Establish topical authority: cover your niche thoroughly, not superficially
- [ ] Build entity recognition: consistent company name, people names, product names across pages
- [ ] Use structured data extensively (helps AI engines understand content relationships)
- [ ] Create comprehensive, original content (AI engines prefer authoritative sources)
- [ ] Include citations and references to credible sources
- [ ] Maintain content freshness (update published dates, add new information)
- [ ] Build genuine backlinks and mentions (AI engines use these as trust signals)

### Norwegian Market Considerations
- [ ] Create content in Norwegian bokmål for local search intent
- [ ] Include location-specific terms (Trondheim, Norge) naturally
- [ ] Register with Google Business Profile for local visibility
- [ ] Consider both Norwegian and English search queries if targeting international

## Pre-Launch SEO Review

Run through this before every site launch:
1. All pages have unique meta title + description
2. Heading hierarchy is clean on every page
3. All images have alt text
4. Sitemap is enabled and submitted
5. Robots.txt allows crawling (including AI bots)
6. 301 redirects are set for any migrated URLs
7. Schema markup is validated
8. OG tags are set and tested
9. Google Search Console is connected
10. Google Analytics / Plausible is installed

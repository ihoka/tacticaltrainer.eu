# Blog Post Image Headers Specification

**Date:** 2026-01-20
**Status:** Draft
**Author:** Claude

---

## 1. Overview

Add featured image headers to blog posts that serve dual purposes:
1. Visual header displayed at the top of blog posts
2. Social network share images (Open Graph, Twitter Cards)

This enhancement improves visual appeal, increases social media engagement, and provides consistent branding across shared content.

---

## 2. Goals

- Display prominent header images on individual blog posts
- Provide optimized images for social media sharing (Facebook, Twitter, LinkedIn)
- Maintain fast page load performance with responsive images
- Support fallback to default images when post-specific images aren't provided
- Integrate with existing SEO infrastructure (jekyll-seo-tag, structured data)

---

## 3. Image Specifications

### 3.1 Dimensions

| Usage | Dimensions | Aspect Ratio | Notes |
|-------|-----------|--------------|-------|
| **Social Share (OG/Twitter)** | 1200 x 630 px | 1.91:1 | Optimal for Facebook, LinkedIn, Twitter |
| **Post Header (Desktop)** | 1200 x 630 px | 1.91:1 | Full-width on desktop |
| **Post Header (Mobile)** | 640 x 336 px | 1.91:1 | Responsive version |
| **Post Card Thumbnail** | 400 x 210 px | 1.91:1 | Home page cards (optional) |

### 3.2 File Formats

- **Primary:** WebP (for modern browsers, smaller file size)
- **Fallback:** JPEG (for older browsers, social crawlers)
- **Recommended:** Provide both formats using `<picture>` element

### 3.3 File Size Guidelines

| Format | Target Size | Maximum |
|--------|------------|---------|
| WebP | < 80 KB | 150 KB |
| JPEG | < 120 KB | 200 KB |

### 3.4 Visual Guidelines

- **Safe Zone:** Keep important content (text, logos) within center 1000 x 500 px area
- **Text Overlay:** Minimal text; social platforms may reject images with >20% text
- **Brand Colors:** Use tactical theme palette (dark backgrounds, gold accents)
- **Contrast:** Ensure readability when overlaid with post title

---

## 4. Directory Structure

```
assets/
└── images/
    ├── posts/                    # Post-specific header images
    │   ├── 2024-01-15-hic-training.jpg
    │   ├── 2024-01-15-hic-training.webp
    │   └── ...
    ├── default-post-header.jpg   # Default fallback header
    ├── default-post-header.webp
    └── default-og-image.jpg      # Existing OG fallback
```

### Naming Convention

Post images should match the post filename for easy association:

```
Post: _posts/2024-01-15-hic-training-guide.md
Image: assets/images/posts/2024-01-15-hic-training-guide.jpg
```

---

## 5. Front Matter Changes

### 5.1 New/Updated Fields

```yaml
---
layout: post
title: "HIC Training Guide"
date: 2024-01-15 10:00:00 -0500
categories: [Training, HIC]

# Image Configuration (updated)
image:
  path: /assets/images/posts/2024-01-15-hic-training-guide.jpg
  alt: "Athlete performing high-intensity conditioning exercises"
  width: 1200
  height: 630

# OR simple format (backward compatible)
image: /assets/images/posts/2024-01-15-hic-training-guide.jpg
image_alt: "Athlete performing high-intensity conditioning exercises"
---
```

### 5.2 Field Definitions

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `image` | String or Object | No | Path to header image or image config object |
| `image.path` | String | Yes (if object) | Path to the image file |
| `image.alt` | String | Recommended | Alt text for accessibility |
| `image.width` | Integer | Optional | Image width in pixels |
| `image.height` | Integer | Optional | Image height in pixels |
| `image_alt` | String | Optional | Alt text (simple format) |

### 5.3 Default Values (_config.yml)

```yaml
defaults:
  - scope:
      path: ""
      type: "posts"
    values:
      image:
        path: /assets/images/default-post-header.jpg
        alt: "Tactical Training - Train Hard, Stay Ready"
        width: 1200
        height: 630
```

---

## 6. Layout Changes

### 6.1 Post Layout (_layouts/post.html)

Add featured image section after breadcrumbs, before post header:

```html
{%- if page.image -%}
<div class="post-featured-image">
  {%- assign img_path = page.image.path | default: page.image -%}
  {%- assign img_alt = page.image.alt | default: page.image_alt | default: page.title -%}

  <picture>
    {%- assign webp_path = img_path | replace: '.jpg', '.webp' | replace: '.jpeg', '.webp' | replace: '.png', '.webp' -%}
    <source srcset="{{ webp_path | relative_url }}" type="image/webp">
    <img
      src="{{ img_path | relative_url }}"
      alt="{{ img_alt | escape }}"
      width="1200"
      height="630"
      loading="eager"
      decoding="async"
      class="post-header-image"
    >
  </picture>
</div>
{%- endif -%}
```

### 6.2 Home Layout (_layouts/home.html) - Optional Enhancement

Add thumbnail to post cards:

```html
<article class="post-card">
  {%- if post.image -%}
  <div class="post-card-image">
    {%- assign img_path = post.image.path | default: post.image -%}
    <img
      src="{{ img_path | relative_url }}"
      alt="{{ post.title | escape }}"
      loading="lazy"
      width="400"
      height="210"
    >
  </div>
  {%- endif -%}
  <!-- existing post card content -->
</article>
```

---

## 7. CSS Styling

### 7.1 Post Featured Image

```css
/* Post Featured Image Header */
.post-featured-image {
  margin: 0 calc(-1 * var(--spacing-lg));
  margin-bottom: var(--spacing-xl);
  overflow: hidden;
  border-radius: var(--border-radius);
  background-color: var(--tactical-surface);
}

@media (min-width: 1024px) {
  .post-featured-image {
    margin: 0 calc(-1 * var(--spacing-xl));
    margin-bottom: var(--spacing-xl);
  }
}

.post-header-image {
  width: 100%;
  height: auto;
  display: block;
  aspect-ratio: 1200 / 630;
  object-fit: cover;
}

/* Loading placeholder */
.post-featured-image img {
  background: linear-gradient(
    135deg,
    var(--tactical-surface) 0%,
    var(--tactical-bg) 100%
  );
}
```

### 7.2 Post Card Thumbnail (Optional)

```css
/* Post Card with Image */
.post-card-image {
  margin: calc(-1 * var(--spacing-md));
  margin-bottom: var(--spacing-md);
  overflow: hidden;
  border-radius: var(--border-radius) var(--border-radius) 0 0;
}

.post-card-image img {
  width: 100%;
  height: auto;
  aspect-ratio: 400 / 210;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.post-card:hover .post-card-image img {
  transform: scale(1.05);
}
```

---

## 8. Social Meta Tags Enhancement

### 8.1 Update _includes/social-meta.html

```html
{%- assign page_image = page.image.path | default: page.image | default: site.default_image -%}
{%- assign page_image_alt = page.image.alt | default: page.image_alt | default: page.title -%}
{%- assign page_image_width = page.image.width | default: 1200 -%}
{%- assign page_image_height = page.image.height | default: 630 -%}

<!-- Open Graph Image -->
<meta property="og:image" content="{{ page_image | absolute_url }}">
<meta property="og:image:width" content="{{ page_image_width }}">
<meta property="og:image:height" content="{{ page_image_height }}">
<meta property="og:image:alt" content="{{ page_image_alt | escape }}">
<meta property="og:image:type" content="image/jpeg">

<!-- Twitter Card Image -->
<meta name="twitter:image" content="{{ page_image | absolute_url }}">
<meta name="twitter:image:alt" content="{{ page_image_alt | escape }}">
```

### 8.2 Update _includes/structured-data.html

Ensure Article schema includes full image object:

```json
"image": {
  "@type": "ImageObject",
  "url": "{{ page_image | absolute_url }}",
  "width": {{ page_image_width }},
  "height": {{ page_image_height }}
}
```

---

## 9. Default Images

### 9.1 Required Default Images

| File | Purpose | Dimensions |
|------|---------|------------|
| `assets/images/default-post-header.jpg` | Fallback for posts without custom image | 1200 x 630 |
| `assets/images/default-post-header.webp` | WebP version of above | 1200 x 630 |
| `assets/images/default-og-image.jpg` | Site-wide OG fallback (existing) | 1200 x 630 |

### 9.2 Default Image Design

- Dark tactical background (matches site theme)
- Tacticaltrainer.eu logo/branding
- "Train Hard. Stay Ready." tagline
- Subtle military/fitness imagery (silhouettes, patterns)

---

## 10. Implementation Steps

### Phase 1: Core Infrastructure

1. Create `assets/images/posts/` directory
2. Create default header images (jpg + webp)
3. Update `_config.yml` with new default values
4. Update `_includes/social-meta.html` with image dimensions

### Phase 2: Post Layout

5. Add featured image section to `_layouts/post.html`
6. Add CSS styles to `assets/css/style.css`
7. Test with existing posts using default image

### Phase 3: Content Migration

8. Create custom header images for existing posts
9. Update front matter in existing posts
10. Verify social share previews

### Phase 4: Home Page Enhancement (Optional)

11. Add thumbnails to post cards in `_layouts/home.html`
12. Add thumbnail CSS styles
13. Test responsive behavior

---

## 11. Testing Checklist

### Visual Testing

- [ ] Header image displays correctly on desktop (1024px+)
- [ ] Header image displays correctly on tablet (640-1024px)
- [ ] Header image displays correctly on mobile (<640px)
- [ ] Aspect ratio maintained during resize
- [ ] WebP loads in modern browsers
- [ ] JPEG fallback loads in older browsers
- [ ] Default image displays when no custom image specified

### Social Preview Testing

- [ ] Facebook Sharing Debugger shows correct image
- [ ] Twitter Card Validator shows correct image
- [ ] LinkedIn Post Inspector shows correct image
- [ ] Image dimensions reported correctly in meta tags

### Performance Testing

- [ ] Images lazy-load correctly (home page)
- [ ] Hero image loads eagerly (post page)
- [ ] Lighthouse performance score maintained (>90)
- [ ] WebP serves smaller file sizes

### Accessibility Testing

- [ ] All images have descriptive alt text
- [ ] Color contrast meets WCAG guidelines
- [ ] Images don't prevent content access when failing to load

---

## 12. Social Platform Requirements Reference

| Platform | Recommended Size | Min Size | Aspect Ratio |
|----------|-----------------|----------|--------------|
| Facebook | 1200 x 630 | 600 x 315 | 1.91:1 |
| Twitter | 1200 x 628 | 300 x 157 | 1.91:1 |
| LinkedIn | 1200 x 627 | 200 x 200 | 1.91:1 |
| Pinterest | 1000 x 1500 | 600 x 900 | 2:3 |
| WhatsApp | 300 x 200 | 300 x 200 | 3:2 |

**Note:** The 1200 x 630 specification covers all major platforms optimally.

---

## 13. Future Enhancements

- **Auto-generation:** Script to generate header images from post titles using templates
- **Responsive srcset:** Multiple image sizes for bandwidth optimization
- **Image CDN:** Integration with image CDN for automatic optimization
- **Blur placeholders:** Low-quality image placeholders (LQIP) during load
- **Category-specific defaults:** Different default images per category

---

## 14. Files to Modify

| File | Changes |
|------|---------|
| `_config.yml` | Update default image configuration |
| `_layouts/post.html` | Add featured image section |
| `_layouts/home.html` | Add post card thumbnails (optional) |
| `_includes/social-meta.html` | Add image dimension meta tags |
| `_includes/structured-data.html` | Update Article image schema |
| `assets/css/style.css` | Add featured image styles |

---

## 15. Success Metrics

- All blog posts display header images
- Social shares show custom/default images (no broken images)
- Page load time increase < 200ms
- Lighthouse performance score remains > 90
- Social engagement increase (track via analytics)

---

*End of Specification*

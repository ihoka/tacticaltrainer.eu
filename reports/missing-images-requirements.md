# Missing Images Requirements

**Date:** 2026-01-19
**Priority:** HIGH - Required for SEO/Schema validation

## Overview

The blog references several images in structured data and post front matter that don't currently exist. These missing images are causing:
- Broken Open Graph tags (Facebook/LinkedIn previews fail)
- Invalid structured data (Google Rich Results Test fails)
- Broken Twitter Cards
- Poor social sharing experience

## Required Images

### 1. Logo (Critical for Organization Schema)

**Path:** `/assets/images/logo.png`

**Specifications:**
- **Minimum Size:** 600x60px (Google requirement)
- **Recommended Size:** 600x60px to 1200x120px
- **Format:** PNG with transparent background preferred
- **Content:** "Tactical Training" logo/wordmark
- **Usage:** Organization schema, header, footer

**Schema Requirement:**
```json
"logo": {
  "@type": "ImageObject",
  "url": "https://tacticaltrainer.eu/assets/images/logo.png"
}
```

---

### 2. Default Post Image (Fallback for Posts)

**Path:** `/assets/images/default-post-image.jpg`

**Specifications:**
- **Size:** 1200x630px (Open Graph standard)
- **Format:** JPG or PNG
- **Content:** Tactical/military themed background with "Tactical Training Blog" text overlay
- **Style:** Dark theme, military aesthetic
- **File Size:** < 300KB

**Usage:**
- Fallback when posts don't specify a custom image
- Open Graph image for social sharing
- Twitter Card image

**Example Content Ideas:**
- Tactical gear/equipment silhouette
- Military-style training facility
- Abstract camouflage pattern
- Dark tactical texture with branding

---

### 3. Default Open Graph Image (Social Sharing)

**Path:** `/assets/images/default-og-image.jpg`

**Specifications:**
- **Size:** 1200x630px (Facebook/LinkedIn recommended)
- **Format:** JPG
- **Content:** "Tactical Training Blog" with tagline
- **Text Overlay:** "Evidence-Based Training for Tactical Athletes"
- **Style:** Professional, high-contrast, readable on mobile

**Usage:**
- _config.yml default_image setting
- Sitewide fallback for social sharing
- Used when no post-specific image is available

**Design Guidelines:**
- Large, bold text (readable at small sizes)
- 20% safe zone around edges (avoid text cutoff)
- High contrast for readability
- Tactical color scheme (dark greens, golds, blacks)

---

### 4. Post-Specific Images (Existing Posts)

The following posts reference images that don't exist:

#### Post: "Max Strength Training"
**Referenced:** `/assets/images/max-strength.jpg`
**Recommended Content:** Athlete performing back squat or deadlift
**Size:** 1200x630px

#### Post: "However Beautiful the Strategy..."
**Referenced:** `/assets/images/strategy-results.jpg`
**Recommended Content:** Training data/results visualization or tactical planning imagery
**Size:** 1200x630px

#### Post: "Tracking Tactical Barbell with COROS"
**Referenced:** `/assets/images/tactical-training-hero.jpg`
**Recommended Content:** COROS watch display showing workout metrics
**Size:** 1200x630px

---

## Quick Fix: Temporary Placeholder

If you can't create proper images immediately, create simple placeholder images to unblock SEO:

### Temporary Logo (600x60px)
- Solid dark background
- "Tactical Training" in white, bold, sans-serif
- Export as PNG

### Temporary Default Images (1200x630px)
- Dark olive/green background (#2d3e2d)
- White text: "Tactical Training Blog"
- Subtext: "Evidence-Based Training"
- Export as JPG, < 200KB

**Tools:**
- Free: Canva (canva.com)
- Professional: Figma, Adobe Photoshop, Sketch
- Quick: ImageMagick command-line tool

---

## Implementation Checklist

After creating images:

- [ ] Place `logo.png` in `/assets/images/`
- [ ] Place `default-post-image.jpg` in `/assets/images/`
- [ ] Place `default-og-image.jpg` in `/assets/images/`
- [ ] Create post-specific images for existing posts
- [ ] Validate structured data with [Google Rich Results Test](https://search.google.com/test/rich-results)
- [ ] Test social sharing on Facebook, Twitter, LinkedIn
- [ ] Verify Open Graph tags with [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [ ] Test Twitter Cards with [Twitter Card Validator](https://cards-dev.twitter.com/validator)

---

## Expected Impact

Once images are added:

**Week 1:**
- Structured data validation passes
- Rich Results eligibility restored
- Social sharing works correctly

**Week 2-4:**
- Improved click-through rates from social media
- Better brand recognition in search results
- Potential eligibility for Google Discover

---

## Notes

- All images should follow the tactical/military dark aesthetic established in the CSS
- Use tactical color palette: dark greens (#2d3e2d), gold accents (#d4af37), blacks
- Maintain professional appearance suitable for first responders and military professionals
- Optimize all images for web (compress, strip metadata)
- Consider using WebP format for better compression (with JPG fallback)

---

**Next Steps:**
1. Create/commission the 3 critical default images (logo, default-post-image, default-og-image)
2. Deploy images to `/assets/images/` directory
3. Rebuild site and verify with testing tools
4. Create post-specific images as time permits

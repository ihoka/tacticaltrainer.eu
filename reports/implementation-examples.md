# SEO/AEO Implementation Examples

## 1. Updated Front Matter for Blog Posts

### Example: HIC Training Post

```yaml
---
layout: post
title: "HIC Training Guide: 5 High-Intensity Protocols for Tactical Athletes (2025)"
description: "Master high-intensity conditioning with 5 proven HIC protocols. Build work capacity, mental toughness, and operational readiness with science-backed tactical fitness training."
date: 2024-12-29 08:00:00 -0500
last_modified_at: 2025-01-02 10:00:00 -0500
categories: [HIC, Training]
author: Tactical Training Team
tags: [hic, high intensity conditioning, tactical fitness, military workout, work capacity, conditioning protocol]
reading_time: 5
image: /blog/assets/images/hic-training-hero.jpg
excerpt: "Discover how High Intensity Conditioning (HIC) builds the work capacity and mental toughness required for tactical operations. Includes 5 battle-tested protocols."

# Structured Data Enhancement
faq:
  - question: "What is HIC training?"
    answer: "HIC (High Intensity Conditioning) is a 10-30 minute workout protocol using 80-95% maximum effort with minimal rest, designed to build work capacity for tactical professionals."
  - question: "How often should I do HIC workouts?"
    answer: "Most tactical athletes benefit from 2-3 HIC sessions per week, with at least one rest day between sessions for recovery."
  - question: "Can beginners do HIC training?"
    answer: "Yes, HIC can be scaled by reducing rounds, using lighter weights, or incorporating longer rest periods between exercises."

# Schema.org HowTo for workout
howto:
  duration: 20
  equipment: ["Kettlebell (24kg/16kg)", "Pull-up bar", "24-inch box", "Timer"]
  steps:
    - name: "Warm-up"
      text: "5 minutes of dynamic stretching and light cardio"
    - name: "Round 1-5"
      text: "Complete 20 kettlebell swings, 15 push-ups, 10 box jumps, 200m run"
    - name: "Cool-down"
      text: "5 minutes of static stretching and breathing exercises"
---
```

## 2. Structured Data Include File

Create `_includes/structured-data.html`:

```html
<!-- Organization Schema -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Tactical Training",
  "url": "https://tacticaltrainer.eu",
  "logo": "https://tacticaltrainer.eu/blog/assets/images/logo.png",
  "description": "{{ site.description | escape }}",
  "sameAs": [
    "https://github.com/ihoka/tacticaltraining.eu"
  ],
  "contactPoint": {
    "@type": "ContactPoint",
    "email": "kent@tacticaltrainer.eu",
    "contactType": "customer service",
    "areaServed": "Worldwide",
    "availableLanguage": "English"
  }
}
</script>

<!-- BreadcrumbList Schema -->
{% if page.url != "/" %}
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Home",
      "item": "{{ site.url }}{{ site.baseurl }}"
    }
    {% if page.categories %}
    ,{
      "@type": "ListItem",
      "position": 2,
      "name": "{{ page.categories | first }}",
      "item": "{{ site.url }}{{ site.baseurl }}/category/{{ page.categories | first | downcase }}"
    }
    {% endif %}
    {% if page.title %}
    ,{
      "@type": "ListItem",
      "position": {% if page.categories %}3{% else %}2{% endif %},
      "name": "{{ page.title | escape }}",
      "item": "{{ site.url }}{{ page.url }}"
    }
    {% endif %}
  ]
}
</script>
{% endif %}

<!-- Article/BlogPosting Schema -->
{% if page.layout == "post" %}
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "{{ site.url }}{{ page.url }}"
  },
  "headline": "{{ page.title | escape }}",
  "description": "{{ page.description | default: page.excerpt | strip_html | truncate: 160 | escape }}",
  "image": {
    "@type": "ImageObject",
    "url": "{{ page.image | default: '/blog/assets/images/default-hero.jpg' | absolute_url }}",
    "width": 1200,
    "height": 630
  },
  "author": {
    "@type": "Person",
    "name": "{{ page.author | default: site.author.name }}",
    "url": "{{ site.url }}/about"
  },
  "publisher": {
    "@type": "Organization",
    "name": "{{ site.title }}",
    "logo": {
      "@type": "ImageObject",
      "url": "{{ site.url }}/blog/assets/images/logo.png",
      "width": 600,
      "height": 60
    }
  },
  "datePublished": "{{ page.date | date_to_xmlschema }}",
  "dateModified": "{{ page.last_modified_at | default: page.date | date_to_xmlschema }}",
  "keywords": "{{ page.tags | join: ', ' }}",
  "articleSection": "{{ page.categories | join: ', ' }}",
  "wordCount": "{{ content | number_of_words }}",
  "speakable": {
    "@type": "SpeakableSpecification",
    "cssSelector": [".post-content p:first-of-type", "h2"]
  }
}
</script>

<!-- FAQ Schema if FAQs exist -->
{% if page.faq %}
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {% for item in page.faq %}
    {
      "@type": "Question",
      "name": "{{ item.question | escape }}",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "{{ item.answer | escape }}"
      }
    }{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ]
}
</script>
{% endif %}
{% endif %}
```

## 3. Enhanced Default Layout

Update `_layouts/default.html`:

```html
<!DOCTYPE html>
<html lang="{{ page.lang | default: site.lang | default: 'en' }}">
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <!-- Canonical URL -->
  <link rel="canonical" href="{{ page.url | replace:'index.html','' | absolute_url }}">

  <!-- Alternate URLs for different formats -->
  <link rel="alternate" type="application/rss+xml" title="{{ site.title }}" href="{{ '/feed.xml' | relative_url }}">

  <!-- Preconnect for performance -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="dns-prefetch" href="https://www.google-analytics.com">

  {%- seo -%}

  <!-- Enhanced Meta Tags -->
  <meta name="author" content="{{ page.author | default: site.author.name }}">
  <meta name="robots" content="index, follow, max-snippet:-1, max-image-preview:large, max-video-preview:-1">

  <!-- Open Graph -->
  <meta property="og:locale" content="en_US">
  <meta property="og:type" content="{% if page.layout == 'post' %}article{% else %}website{% endif %}">
  <meta property="og:title" content="{{ page.title | default: site.title | escape }}">
  <meta property="og:description" content="{{ page.description | default: page.excerpt | default: site.description | strip_html | truncate: 160 | escape }}">
  <meta property="og:url" content="{{ page.url | absolute_url }}">
  <meta property="og:site_name" content="{{ site.title }}">
  <meta property="og:image" content="{{ page.image | default: '/blog/assets/images/og-default.jpg' | absolute_url }}">
  <meta property="og:image:width" content="1200">
  <meta property="og:image:height" content="630">

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="{{ page.title | default: site.title | escape }}">
  <meta name="twitter:description" content="{{ page.description | default: page.excerpt | default: site.description | strip_html | truncate: 160 | escape }}">
  <meta name="twitter:image" content="{{ page.image | default: '/blog/assets/images/twitter-default.jpg' | absolute_url }}">

  <!-- Structured Data -->
  {%- include structured-data.html -%}

  <!-- CSS -->
  <link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">

  <!-- Favicon -->
  <link rel="icon" type="image/png" sizes="32x32" href="{{ '/favicon-32x32.png' | relative_url }}">
  <link rel="icon" type="image/png" sizes="16x16" href="{{ '/favicon-16x16.png' | relative_url }}">

  {%- feed_meta -%}
</head>
<body>
  {%- include header.html -%}

  <!-- Breadcrumbs for SEO -->
  {% unless page.url == "/" %}
  <nav class="breadcrumbs" aria-label="Breadcrumb">
    <ol itemscope itemtype="https://schema.org/BreadcrumbList">
      <li itemprop="itemListElement" itemscope itemtype="https://schema.org/ListItem">
        <a itemprop="item" href="{{ site.baseurl }}/">
          <span itemprop="name">Home</span>
        </a>
        <meta itemprop="position" content="1" />
      </li>
      {% if page.categories %}
      <li itemprop="itemListElement" itemscope itemtype="https://schema.org/ListItem">
        <a itemprop="item" href="{{ site.baseurl }}/category/{{ page.categories | first | downcase }}/">
          <span itemprop="name">{{ page.categories | first }}</span>
        </a>
        <meta itemprop="position" content="2" />
      </li>
      {% endif %}
      <li itemprop="itemListElement" itemscope itemtype="https://schema.org/ListItem">
        <span itemprop="name">{{ page.title | truncate: 50 }}</span>
        <meta itemprop="position" content="{% if page.categories %}3{% else %}2{% endif %}" />
      </li>
    </ol>
  </nav>
  {% endunless %}

  <main class="page-content" aria-label="Content">
    <div class="wrapper">
      {{ content }}
    </div>
  </main>

  {%- include footer.html -%}

  <!-- Performance Monitoring -->
  <script>
    // Core Web Vitals tracking
    if ('web-vital' in window) {
      window.addEventListener('load', function() {
        // Track CLS, FID, LCP
      });
    }
  </script>
</body>
</html>
```

## 4. Content Template with AEO Optimization

### Blog Post Template with Answer Boxes

```markdown
---
layout: post
title: "Complete Guide to LISS Cardio for Tactical Athletes: Zone 2 Training Explained"
description: "Learn how Low Intensity Steady State (LISS) cardio builds aerobic capacity for tactical athletes. Evidence-based Zone 2 training protocols for military and first responders."
# ... other front matter ...
---

<!-- Direct Answer Box for AI Engines -->
<div class="answer-box" itemscope itemtype="https://schema.org/Answer">
  <p class="question" itemprop="name"><strong>Quick Answer: What is LISS cardio?</strong></p>
  <p itemprop="text">LISS (Low Intensity Steady State) cardio is aerobic exercise performed at 60-70% maximum heart rate for 30-60 minutes, building endurance and recovery capacity crucial for tactical operations.</p>
</div>

## Introduction

{{ Introduction paragraph optimized for featured snippet - 40-60 words }}

## What is LISS Training? {#what-is-liss}

<!-- Optimized for paragraph featured snippet -->
LISS training involves sustained cardiovascular exercise at a conversational pace, typically maintaining a heart rate between 120-150 BPM for most tactical athletes. This Zone 2 training builds aerobic base, improves recovery between high-intensity efforts, and enhances fat oxidation without accumulated fatigue.

## Benefits of LISS for Tactical Athletes {#benefits}

<!-- Optimized for list featured snippet -->
Key benefits include:
- **Enhanced aerobic capacity** - Improves oxygen utilization
- **Faster recovery** - Between training sessions and operations
- **Improved fat metabolism** - Better energy efficiency
- **Reduced injury risk** - Low-impact conditioning option
- **Mental resilience** - Builds patience and discipline

## LISS vs HIC Training Comparison {#comparison}

<!-- Optimized for table featured snippet -->
| Aspect | LISS | HIC |
|--------|------|-----|
| Duration | 30-60 minutes | 10-30 minutes |
| Intensity | 60-70% max HR | 80-95% max HR |
| Energy System | Aerobic | Anaerobic/Aerobic |
| Recovery Time | 24 hours | 48-72 hours |
| Frequency | 3-5x per week | 2-3x per week |

## Sample LISS Workouts {#workouts}

### Workout 1: Zone 2 Run
<!-- Structured for HowTo schema -->
1. **Warm-up** (5 min): Easy walk/jog
2. **Main Set** (30-45 min): Maintain 130-140 BPM
3. **Cool-down** (5 min): Walk and stretch

### Workout 2: Ruck March
1. **Load**: 35-45 lbs (adjust to fitness)
2. **Duration**: 45-60 minutes
3. **Pace**: 15-minute miles
4. **Terrain**: Mixed elevation preferred

## Frequently Asked Questions {#faq}

<div class="faq-section">

### How do I calculate my Zone 2 heart rate?
Use the formula: (220 - age) × 0.6-0.7. For a 30-year-old, Zone 2 is approximately 114-133 BPM.

### Can I do LISS and HIC on the same day?
While possible, it's not optimal. Separate these sessions by at least 6-8 hours if training twice daily.

### What's the best LISS exercise for tactical athletes?
Rucking closely mimics operational demands, but running, cycling, and swimming all build effective aerobic capacity.

</div>

## Key Takeaways

<!-- Summary box for AI engines -->
<div class="key-points">
✓ LISS builds essential aerobic base for tactical performance
✓ Perform 3-5 sessions weekly at 60-70% max heart rate
✓ Complement HIC training, don't replace it
✓ Track heart rate to ensure proper intensity
</div>

## Related Posts
- [HIC Training Protocols](/blog/hic-training-guide/)
- [Recovery Methods for Tactical Athletes](/blog/recovery-protocols/)
- [Strength Training for Operations](/blog/strength-endurance/)

---

*Ready to optimize your aerobic base? Download our [Free LISS Training Template](/resources/liss-template.pdf) and start building elite endurance.*
```

## 5. robots.txt

```
# Tactical Training Blog Robots.txt
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /tmp/
Disallow: /temp/

# Specific bot rules
User-agent: GPTBot
Allow: /

User-agent: ChatGPT-User
Allow: /

User-agent: CCBot
Allow: /

User-agent: PerplexityBot
Allow: /

User-agent: Claude-Web
Allow: /

# Sitemap location
Sitemap: https://tacticaltrainer.eu/blog/sitemap.xml

# Crawl delay (in seconds)
Crawl-delay: 1
```

## 6. Custom 404 Page

Create `404.html`:

```html
---
layout: default
title: "404 - Page Not Found"
description: "The tactical training resource you're looking for isn't here. Find our best content and training guides."
permalink: /404.html
---

<div class="error-404">
  <h1>Mission Not Found</h1>
  <p class="lead">The page you're looking for has been redeployed to a different location.</p>

  <div class="suggestions">
    <h2>Popular Training Resources:</h2>
    <ul>
      <li><a href="{{ '/blog' | relative_url }}">Latest Training Articles</a></li>
      <li><a href="{{ '/category/hic' | relative_url }}">HIC Training Guides</a></li>
      <li><a href="{{ '/category/recovery' | relative_url }}">Recovery Protocols</a></li>
      <li><a href="{{ '/about' | relative_url }}">About Tactical Training</a></li>
    </ul>
  </div>

  <div class="search-suggestion">
    <h3>Try searching for what you need:</h3>
    <form action="{{ '/search' | relative_url }}" method="get">
      <input type="text" name="q" placeholder="Search tactical training..." />
      <button type="submit">Search</button>
    </form>
  </div>

  <p class="back-link">
    <a href="{{ '/' | relative_url }}">&larr; Return to Base</a>
  </p>
</div>

<style>
.error-404 {
  text-align: center;
  padding: var(--spacing-3xl) 0;
}
.error-404 h1 {
  font-size: 3rem;
  color: var(--tactical-accent);
}
.suggestions {
  margin: var(--spacing-2xl) 0;
}
.suggestions ul {
  list-style: none;
  padding: 0;
}
.suggestions li {
  margin: var(--spacing-sm) 0;
}
</style>
```

These implementation examples provide ready-to-use code that follows SEO and AEO best practices while maintaining the tactical training theme and branding.
# Tactical Training Blog

A Jekyll-based static blog focused on tactical fitness, training protocols, and performance optimization for military professionals, first responders, and serious athletes.

## Overview

This blog provides evidence-based training methodologies designed for operational readiness and peak physical performance. Content covers:

- **HIC** (High Intensity Conditioning) - Short, intense workouts
- **LISS** (Low Intensity Steady State) - Aerobic base building
- **SE** (Strength Endurance) - Hybrid training protocols
- **Nutrition** - Tactical fueling strategies
- **Recovery** - Rest and regeneration techniques
- **Mindset** - Mental performance optimization

## Tech Stack

- **Framework**: Jekyll 4.x
- **Theme**: Minima 2.5 with custom tactical/military dark styling
- **Plugins**: jekyll-feed, jekyll-seo-tag, jekyll-sitemap
- **Styling**: Custom CSS with CSS variables for theming

## Quick Start

### Prerequisites

- Ruby (3.0+ recommended)
- Bundler gem (`gem install bundler`)

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd blog

# Install dependencies
bundle install

# Start the development server
bundle exec jekyll serve
```

Visit `http://localhost:4000/blog` to view the site.

### Development Commands

```bash
# Serve with live reload
bundle exec jekyll serve

# Serve with drafts visible
bundle exec jekyll serve --drafts

# Build for production
JEKYLL_ENV=production bundle exec jekyll build

# Clean build artifacts
bundle exec jekyll clean

# Check for issues
bundle exec jekyll doctor
```

## Creating Content

### New Blog Post

Create a new file in `_posts/` with the naming convention:

```
YYYY-MM-DD-title-with-dashes.md
```

Example: `2024-12-30-understanding-hic-training.md`

### Post Front Matter

```yaml
---
layout: post
title: "Your Post Title"
date: 2024-12-30 09:00:00 -0500
categories: [Training, HIC]
author: Tactical Training Team
tags: [conditioning, fitness, protocols]
reading_time: 5
---

Your content here...
```

### Categories

Use these standardized categories:
- `Training` - General methodology
- `HIC` - High Intensity Conditioning
- `LISS` - Low Intensity Steady State
- `Strength` - Strength training
- `Nutrition` - Diet and fueling
- `Recovery` - Rest and regeneration
- `Mindset` - Mental performance

## Project Structure

```
blog/
├── _config.yml         # Site configuration
├── _includes/          # Reusable HTML partials
│   ├── header.html
│   └── footer.html
├── _layouts/           # Page templates
│   ├── default.html
│   ├── home.html
│   ├── page.html
│   └── post.html
├── _posts/             # Blog posts (Markdown)
├── assets/
│   └── css/
│       └── style.css   # Custom theme CSS
├── reports/            # Project documentation
├── about.md            # About page
├── index.html          # Home page
├── Gemfile             # Ruby dependencies
└── AGENTS.md           # AI assistant guidance
```

## Deployment

The site builds to `../public/blog` for integration with the parent Rails application:

```bash
JEKYLL_ENV=production bundle exec jekyll build
```

## Theme Customization

The tactical theme uses CSS custom properties defined in `assets/css/style.css`:

- **Background**: `--tactical-bg` (dark), `--tactical-surface`
- **Text**: `--tactical-text`, `--tactical-text-secondary`
- **Accent**: `--tactical-accent` (gold), `--tactical-olive` (military green)

## Contributing

1. Create a feature branch
2. Make your changes
3. Test locally with `bundle exec jekyll serve`
4. Submit a pull request

## License

All rights reserved.

---

**Train hard. Stay ready.**

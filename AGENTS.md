# AGENTS.md
This file provides guidance to AI coding assistants working in this repository.

**Note:** CLAUDE.md, .clinerules, .cursorrules, .windsurfrules, .replit.md, GEMINI.md, .github/copilot-instructions.md, and .idx/airules.md are symlinks to AGENTS.md in this project.

# Tactical Training Blog

A Jekyll-based static blog focused on tactical fitness, training protocols, and performance optimization for military professionals, first responders, and serious athletes. Built with the Minima theme and customized with a military/tactical dark aesthetic.

## Project Overview

- **Framework**: Jekyll 4.x static site generator
- **Theme**: Minima 2.5 with custom tactical/military dark styling
- **Language**: Ruby (Gemfile/Bundler), Liquid templates, HTML, CSS, Markdown
- **Output**: Builds to `../public/blog` for integration with main Rails application
- **Plugins**: jekyll-feed, jekyll-seo-tag, jekyll-sitemap

## Build & Commands

### Development

```bash
# Install dependencies
bundle install

# Serve locally with live reload (default: http://localhost:4000/blog)
bundle exec jekyll serve

# Serve with drafts visible
bundle exec jekyll serve --drafts

# Build site without serving
bundle exec jekyll build

# Build for production
JEKYLL_ENV=production bundle exec jekyll build

# Clean build artifacts
bundle exec jekyll clean
```

### Common Tasks

```bash
# Create a new post (manually - follow naming convention below)
# Filename: _posts/YYYY-MM-DD-title-slug.md

# Check for broken links or issues
bundle exec jekyll doctor
```

## Directory Structure

```
blog/
├── _config.yml         # Jekyll site configuration
├── _includes/          # Reusable HTML partials
│   ├── header.html
│   └── footer.html
├── _layouts/           # Page templates
│   ├── default.html    # Base HTML structure
│   ├── home.html       # Home/index page
│   ├── page.html       # Static pages
│   └── post.html       # Blog post layout
├── _posts/             # Blog posts (Markdown)
├── assets/
│   └── css/
│       └── style.css   # Custom tactical theme CSS
├── about.md            # About page
├── index.html          # Home page entry point
├── Gemfile             # Ruby dependencies
└── Gemfile.lock        # Locked dependency versions
```

## Code Style

### Markdown Posts

- **Filename**: `YYYY-MM-DD-title-with-dashes.md`
- **Front matter** (required):
  ```yaml
  ---
  layout: post
  title: "Post Title"
  date: YYYY-MM-DD HH:MM:SS -0500
  categories: [Category1, Category2]
  author: Tactical Training Team
  tags: [tag1, tag2, tag3]
  reading_time: 5
  ---
  ```
- Use descriptive, action-oriented titles
- Include reading_time for estimated read duration
- Categories: Training, HIC, LISS, SE, Strength, Nutrition, Recovery, Mindset

### Liquid Templates

- Use `{%- tag -%}` (with hyphens) to trim whitespace
- Prefer `relative_url` filter for internal links: `{{ '/path' | relative_url }}`
- Escape user content: `{{ page.title | escape }}`
- Use conditional blocks sparingly with clear if/endif markers

### HTML/CSS

- **CSS Variables**: Use custom properties defined in `:root` (e.g., `var(--tactical-bg)`)
- **Naming**: BEM-style class names (e.g., `.post-card`, `.nav-link`, `.post-meta`)
- **Colors**: Follow tactical theme palette:
  - Background: `--tactical-bg`, `--tactical-surface`
  - Text: `--tactical-text`, `--tactical-text-secondary`
  - Accent: `--tactical-accent` (gold), `--tactical-olive` (military green)
- **Responsive**: Mobile-first with breakpoints at 640px and 1024px
- **Units**: Use `rem` for typography, CSS variables for spacing

### Formatting Rules

- 2-space indentation for HTML, Liquid, CSS, YAML
- No trailing whitespace
- Single blank line between sections
- Comments for major CSS sections: `/* ============================================ */`

## Content Guidelines

### Writing Style

- Evidence-based, practical information
- Professional but accessible tone
- Focus on actionable training advice
- Use military/tactical terminology appropriately
- End posts with motivational closer: "Train hard. Stay ready."

### Categories

Use these standardized categories:
- **Training**: General training methodology
- **HIC**: High Intensity Conditioning
- **LISS**: Low Intensity Steady State
- **SE/Strength**: Strength Endurance training
- **Nutrition**: Diet and fueling strategies
- **Recovery**: Rest and regeneration
- **Mindset**: Mental performance

### SEO

- Use descriptive, keyword-rich titles
- Include meta descriptions via jekyll-seo-tag
- Proper heading hierarchy (H1 for title, H2 for sections)
- Alt text for all images

## Configuration

### _config.yml Key Settings

- `baseurl: "/blog"` - Site is served from /blog path
- `destination: ../public/blog` - Builds to Rails public folder
- `theme: minima` with `skin: dark`
- Kramdown markdown with GitHub Flavored Markdown (GFM)
- Rouge syntax highlighter

### Environment Variables

No environment variables required for basic development. Production builds should use:
```bash
JEKYLL_ENV=production bundle exec jekyll build
```

## Dependencies

- Ruby (see Gemfile for version compatibility)
- Bundler gem
- Jekyll 4.x and plugins defined in Gemfile

## Testing

### Manual Verification

- Check all links work with `bundle exec jekyll doctor`
- Preview locally before publishing
- Verify responsive layout on mobile viewport
- Test dark theme readability

### Build Verification

```bash
# Full rebuild and check for errors
bundle exec jekyll clean && bundle exec jekyll build --verbose
```

## Security

- No user input processing (static site)
- Escape all dynamic content in Liquid templates
- Keep dependencies updated with `bundle update`
- Exclude sensitive files in _config.yml

## Deployment

Site builds to `../public/blog` for serving by the parent Rails application. Ensure Jekyll build runs before Rails asset compilation in production deployments.

## Reports Directory

ALL project reports and documentation should be saved to the `reports/` directory:

```
blog/
└── reports/              # All project reports and documentation
    └── *.md             # Various report types
```

## Temporary Files & Debugging

All temporary files, debugging scripts, and test artifacts should be organized in a `/temp` folder:
- Never commit files from `/temp` directory
- Include `/temp/` in `.gitignore`

## Agent Delegation & Tool Execution

### Parallel Execution

When performing multiple operations, send all tool calls in a single message to execute them concurrently for optimal performance.

**These cases MUST use parallel tool calls:**
- Searching for different patterns (imports, usage, definitions)
- Reading multiple files or searching different directories
- Any information gathering where you know upfront what you're looking for

**Sequential calls ONLY when:**
You genuinely REQUIRE the output of one tool to determine the usage of the next tool.

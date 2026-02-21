---
title: Getting Started
order: 1
description: How to set up and use JayDocs
---

# Getting Started

Get your documentation site running in under a minute.

## Installation

```bash
# Clone or copy JayDocs into your project
cd your-project

# Install dependencies
npm install

# Start the dev server
npm run dev
```

## Adding Documentation

Create markdown files in the `docs/` directory:

```
docs/
├── index.md            # Home page
├── getting-started.md  # This page
├── api/
│   ├── overview.md     # /api/overview
│   └── endpoints.md    # /api/endpoints
└── guides/
    └── writing-docs.md # /guides/writing-docs
```

### File Naming

- Use **lowercase-with-dashes** for filenames: `my-cool-page.md`
- Files named `index.md` become the root of their folder
- Folder names become categories in the sidebar

## Building for Production

```bash
# Build static files
npm run build

# Preview the build
npm run preview
```

The output goes to `dist/` — deploy it anywhere that serves static files.

## Configuration

### Changing the Site Title

Edit the `<title>` tag in `index.html`:

```html
<title>My Project Docs</title>
```

### Custom Ordering

Use the `order` frontmatter field to control page order:

```yaml
---
title: First Page
order: 1
---
```

Lower numbers appear first in the sidebar.

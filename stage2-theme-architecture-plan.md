# Stage 2 — Architecture & Theme Planning

## 1) Theme architecture map

### 1.1 Theme identity
- Theme name (working): `wedding-custom`
- Type: fully custom classic theme (PHP templates + Gutenberg-compatible blocks)
- Principle: no page builder dependency; block-editor friendly content assembly

### 1.2 WordPress template hierarchy mapping

Core templates:
- `style.css` (theme metadata)
- `functions.php` (setup, enqueue, supports, hooks)
- `index.php` (ultimate fallback)
- `front-page.php` (homepage)
- `home.php` (posts index)
- `single.php` (single post fallback)
- `page.php` (default page)
- `archive.php` (archive fallback)
- `search.php`
- `404.php`

Context templates:
- `single-{post_type}.php` (e.g. testimonial, event)
- `archive-{post_type}.php`
- `taxonomy-{taxonomy}.php`
- `template-parts/content/content-{post_type}.php`

Global partials:
- `header.php`
- `footer.php`
- `sidebar.php`
- `template-parts/layout/site-branding.php`
- `template-parts/navigation/primary-nav.php`
- `template-parts/layout/mobile-nav.php`
- `template-parts/footer/footer-widgets.php`

### 1.3 Reusable template part strategy

Reusable template parts by concern:
- Layout: container wrappers, section headings, breadcrumbs
- Content cards: service card, testimonial card, gallery card, post card
- CTA modules: primary CTA strip, RSVP CTA, newsletter CTA
- Media modules: hero media block, logo strip, gallery slider wrapper

Rule:
- Keep business logic in `inc/` helpers, templates only for rendering.

### 1.4 CPT strategy (conditional)

Create CPTs only if needed after verified Stage 1 data:
- `service` — service offerings/packages
- `testimonial` — social proof entries
- `story` or `event` — timeline/case entries

If content volume is low, use native Pages + block patterns instead to reduce maintenance.

### 1.5 Taxonomy strategy (conditional)
- `service_category` (hierarchical)
- `event_type` (non-hierarchical or hierarchical based on UX)
- `gallery_type` (if gallery content becomes CPT-driven)

### 1.6 Custom fields strategy

Preferred approach:
- ACF (free) JSON sync with local versioning in `/acf-json`

Field group patterns:
- Global options: contact details, social links, footer text, CTA defaults
- Page-level hero fields: eyebrow, title accent, media, CTA labels/URLs
- Repeater groups for timeline, FAQs, testimonials highlights

Fallback if avoiding ACF:
- Use post meta with registered meta schema + custom metaboxes + sanitization callbacks.

---

## 2) Asset architecture

### 2.1 Folder hierarchy

```text
wedding-custom/
├─ style.css
├─ functions.php
├─ screenshot.png
├─ front-page.php
├─ home.php
├─ page.php
├─ single.php
├─ archive.php
├─ search.php
├─ 404.php
├─ header.php
├─ footer.php
├─ index.php
├─ inc/
│  ├─ setup.php
│  ├─ enqueue.php
│  ├─ cleanup.php
│  ├─ template-tags.php
│  ├─ navigation.php
│  ├─ schema.php
│  ├─ accessibility.php
│  ├─ cpt.php
│  ├─ taxonomies.php
│  ├─ fields.php
│  └─ security.php
├─ template-parts/
│  ├─ layout/
│  ├─ navigation/
│  ├─ content/
│  ├─ modules/
│  └─ footer/
├─ assets/
│  ├─ src/
│  │  ├─ scss/
│  │  │  ├─ base/
│  │  │  ├─ tokens/
│  │  │  ├─ layout/
│  │  │  ├─ components/
│  │  │  ├─ utilities/
│  │  │  └─ pages/
│  │  ├─ js/
│  │  │  ├─ modules/
│  │  │  ├─ vendors/
│  │  │  └─ app.js
│  │  └─ images/
│  └─ dist/
│     ├─ css/
│     ├─ js/
│     └─ images/
├─ languages/
├─ acf-json/
└─ readme.txt
```

### 2.2 CSS/SCSS architecture
- Methodology: token-first + BEM-inspired component naming
- Layers:
  - `tokens/` (colors, spacing, typography, shadows, z-index)
  - `base/` (reset, typography defaults, form normalization)
  - `layout/` (grid, header, footer, wrappers)
  - `components/` (buttons, cards, nav, sliders, forms)
  - `utilities/` (spacing helpers, visibility helpers)
  - `pages/` (minimal page-specific overrides)

### 2.3 JS modular structure
- Entry: `assets/src/js/app.js`
- Modules:
  - `navigation.js` (mobile menu + submenus + focus trap)
  - `header.js` (sticky header behavior)
  - `carousel.js` (lightweight slider wrapper)
  - `forms.js` (client-side UX validation only)
  - `a11y.js` (keyboard interactions, skip links)
- Vendor policy:
  - prefer native JS
  - allow one lightweight carousel dependency if required

---

## 3) Design system definitions

### 3.1 Responsive breakpoints
- `xs`: 0–479px
- `sm`: 480–767px
- `md`: 768–1023px
- `lg`: 1024–1279px
- `xl`: 1280–1535px
- `xxl`: 1536px+

### 3.2 Grid system
- Base container max-width: `1200px` (up to `1320px` for wide sections)
- Standard horizontal gutter: `24px` desktop, `16px` tablet/mobile
- 12-column grid on `lg+`, 6-column on `md`, stacked on `sm`

### 3.3 Typography scale (fluid-preferred)
- Font pair: one serif display + one sans-serif body (self-host when licensing allows)
- Scale sample:
  - `--fs-900`: 56/64
  - `--fs-800`: 44/52
  - `--fs-700`: 36/44
  - `--fs-600`: 30/38
  - `--fs-500`: 24/32
  - `--fs-400`: 20/28
  - `--fs-300`: 18/26
  - `--fs-200`: 16/24
  - `--fs-100`: 14/20

### 3.4 Design tokens
- Color tokens:
  - `--color-bg`, `--color-surface`, `--color-text`, `--color-muted`
  - `--color-primary`, `--color-primary-strong`, `--color-accent`
- Spacing tokens:
  - 4, 8, 12, 16, 24, 32, 48, 64, 96
- Radius tokens:
  - 4, 8, 12, pill
- Shadow tokens:
  - soft, medium
- Motion tokens:
  - standard duration 200ms, emphasized 320ms

---

## 4) Performance strategy

### 4.1 Asset optimization
- Build pipeline outputs minified CSS/JS into `assets/dist`
- Use content hashes or version strings derived from filemtime
- Enqueue only where needed (template-conditional loading)

### 4.2 Image and media handling
- Use `wp_get_attachment_image()` with responsive `srcset/sizes`
- Enforce WebP/AVIF where possible
- Lazy-load non-critical images (`loading="lazy"`)
- Preload hero image only on pages where required

### 4.3 Critical CSS strategy
- Inline minimal above-the-fold CSS for homepage hero/header
- Defer full stylesheet when feasible without CLS regressions
- Avoid layout shift by setting intrinsic media dimensions

### 4.4 Caching compatibility
- Compatible with page caching plugins and server caching (Nginx/LiteSpeed)
- No dynamic content in header/footer that breaks full-page caching unless necessary
- Cache-bust static assets by versioning

### 4.5 JS execution strategy
- `defer` non-critical scripts
- Split by feature and page context
- Avoid jQuery dependency unless required by a selected third-party library

---

## 5) Development standards document

### 5.1 Coding standards
- Follow WordPress PHP Coding Standards and escaping best practices
- Prefix functions/classes with theme namespace prefix (e.g., `wedding_custom_`)
- Keep template files logic-light; move logic to `inc/`

### 5.2 Accessibility standards
- Semantic landmarks (`header`, `nav`, `main`, `footer`)
- Keyboard-operable menus/sliders
- Visible focus states
- ARIA attributes only where semantic HTML is insufficient
- Color contrast aligned with WCAG AA

### 5.3 Security and data integrity
- Escape on output (`esc_html`, `esc_attr`, `wp_kses_post`, etc.)
- Sanitize on input (`sanitize_text_field`, `sanitize_email`, etc.)
- Nonce validation for custom form/AJAX endpoints

### 5.4 Maintainability practices
- Keep components modular and reusable
- Document custom hooks/functions in inline PHPDoc
- Avoid hardcoded content in templates; expose via customizer/options/fields
- Keep plugin reliance minimal and replaceable

### 5.5 Quality gates before Stage 3 completion
- PHP lint clean
- Theme Check major issues resolved
- No console errors in primary templates
- Mobile and desktop nav behavior verified

---

## 6) Stage 2 completion note

✅ Stage 2 architecture and planning deliverables are defined.

Waiting for confirmation before proceeding to **Stage 3 — Core Theme Development (No Page Builders)**.

# Frido — Valentine's Day Landing Page

> **Frontend Developer Assignment** · D2C E-Commerce · Built with Shopify + Replo + Storefront API

---

## Table of Contents

1. [Project Overview & Links](#1-project-overview--links)
2. [Features & UI/UX](#2-features--uiux)
3. [Technical Architecture & State Management](#3-technical-architecture--state-management)
4. [API Integration & Dynamic Data](#4-api-integration--dynamic-data)

---

## 1. Project Overview & Links

A fully dynamic, theme-switching Valentine's Day landing page built for **Frido**, a D2C health and wellness brand. The page showcases curated gift collections across multiple lifestyle categories and adapts its entire visual identity in real-time based on the shopper's relationship status — no page reload required.

| Resource | Link |
|---|---|
| 🌐 Live Page | _[Add Live URL here]_ |
| 🎬 Walkthrough Video | _[Add Walkthrough Video URL here]_ |
| 🛠️ Replo Editor | _[Add Replo Editor Link here]_ |

**Tech Stack**

| Layer | Technology |
|---|---|
| Storefront | Shopify |
| Page Builder | Replo (visual, no-code/low-code) |
| Data Layer | Shopify Storefront API (GraphQL) |
| Templating | Liquid (Shopify) |
| State Management | Replo Variables (custom state-machine) |

---

## 2. Features & UI/UX

### 🎯 Dual-Mode Experience
- **Couple Mode** — warm, romantic colour palette with a dedicated hero video and curated "couples" product grid.
- **Single Mode** — dark, bold aesthetic with its own hero video and an independently filtered product grid.
- A single toggle seamlessly crossfades between both modes without a page reload, preserving scroll position and filter state.

### 🗂️ Category Pill Navigation
- Filterable pill tabs: **Sleep · WFH · Gym · Mom · Dad · Self-Care** (and more).
- Clicking a pill instantly re-queries the Storefront API and re-renders only the product grid — the rest of the page is untouched.

### 🛍️ Dynamic Product Cards
- Product cards are fetched at runtime from the Shopify Storefront API — nothing is hardcoded.
- Each card renders: product image, title, real-time price, compare-at price, and an auto-calculated **discount badge**.

### 💸 Live Discount Badges
- Discount percentage is calculated directly in the UI layer using Liquid-style math:
  ```
  ((compare_at_price − price) / compare_at_price) × 100
  ```
- The badge only renders when a compare-at price is present in the API payload.

### 📱 Responsive Layout
- Fully responsive grid that adapts from single-column mobile to multi-column desktop without layout shift.

---

## 3. Technical Architecture & State Management

### Custom State-Machine (Replo Variables)

Replo's native variable system was extended into a lightweight **finite state-machine** to drive the page's two primary modes:

```
State variable: pageMode  ("couple" | "single")
```

| Event | Transition | Side-effects |
|---|---|---|
| Toggle → Couple | `pageMode = "couple"` | Hero video A fades in, Hero video B fades out, Couple product grid becomes visible |
| Toggle → Single | `pageMode = "single"` | Hero video B fades in, Hero video A fades out, Single product grid becomes visible |

All UI bindings — colours, typography tokens, video sources, grid visibility, and CTA copy — are **bound to `pageMode`** as the single source of truth, eliminating duplicated logic.

### Hero Video Crossfade
Both hero videos are rendered in the DOM simultaneously and stacked via absolute positioning. Opacity is bound to `pageMode`, producing a CSS-driven crossfade with zero JavaScript overhead.

### Zero-Height / Overflow-Hidden Layout Architecture

Standard `display: none` toggling collapses a container but causes layout recalculation artifacts that shift surrounding page sections. To handle dynamic product grid heights without breaking the page layout, the following architecture was implemented:

```
Active grid:   height: auto;   overflow: visible;  opacity: 1;   pointer-events: auto;
Inactive grid: height: 0;      overflow: hidden;   opacity: 0;   pointer-events: none;
```

This technique keeps both grids in the render tree (avoiding layout shift) while making the inactive grid completely invisible and non-interactive. The transition between states is animated with a CSS `transition` on `opacity` for a smooth UX.

### Category Filter State

A second state variable (`coupleCategory` / `singleCategory`) tracks the active pill tab per mode:

```
coupleCategory: "sleep" | "wfh" | "gym" | "mom" | "dad" | ...
singleCategory: "sleep" | "wfh" | "gym" | "self-care" | ...
```

Each pill's `onClick` handler updates the relevant variable, which is directly bound to the Storefront API filter query, triggering an instant re-fetch.

---

## 4. API Integration & Dynamic Data

### Shopify Storefront API Connection

Products are fetched dynamically via the **Shopify Storefront API** (GraphQL) rather than being hardcoded in the page builder. Replo's data-source integration was configured to query the following fields per product:

```graphql
{
  products(first: 12, query: "tag:<category>") {
    edges {
      node {
        id
        title
        handle
        featuredImage { url altText }
        priceRange {
          minVariantPrice { amount currencyCode }
        }
        compareAtPriceRange {
          minVariantPrice { amount currencyCode }
        }
      }
    }
  }
}
```

### Dynamic Category Filtering

The `query` parameter in the Storefront API call is **dynamically bound** to the active category state variable:

```
query: "tag:{{coupleCategory}}"   ← resolves at runtime, e.g. "tag:sleep"
```

When a shopper clicks a category pill, the state variable updates, the bound query re-evaluates, and Replo re-fetches the API — delivering a filtered product grid **without a page reload or custom JavaScript**.

### Discount Percentage Calculation (Liquid Math)

The discount badge on each product card uses a Liquid-style expression evaluated directly in the Replo UI layer:

```liquid
{{ ((product.compareAtPrice - product.price) / product.compareAtPrice * 100) | round }}%
```

This avoids the need for a custom metafield or pre-computed backend value — the percentage is always accurate and derived purely from the live API payload.

### Data Flow Diagram

```
User clicks pill tab
        │
        ▼
categoryState variable updated
        │
        ▼
Storefront API query re-evaluated (tag filter injected)
        │
        ▼
GraphQL response parsed by Replo data binding
        │
        ▼
Product grid re-rendered with new cards
(discount badge calculated inline from price fields)
```

---

## Engineering Notes

Building complex conditional logic inside a **visual page builder** requires disciplined workarounds for constraints that traditional codebases handle trivially. The key engineering decisions in this project — a variable-driven state machine, zero-height layout toggling, and API-bound filter variables — were chosen specifically to achieve production-quality interactivity within Replo's declarative, no-code model, while keeping the solution maintainable and easily editable by non-technical team members.

# Frido-Assignment-

Frido Valentine's Day Landing Page | Frontend Engineering Assignment
This repository contains the technical implementation and documentation for the Valentine's Day Landing Page built for Frido. The project leverages Shopify, Replo, and the Shopify Storefront API to deliver a high-performance, state-driven "Quick Commerce" experience.


🎯 Project Scope
The goal was to create a dual-experience landing page that pivots between Couple Mode and Single Mode. The implementation focuses on polished animations, complex state management within a visual builder, and a fully dynamic data pipeline via the Shopify Storefront API.

🛠️ Technical Architecture
1. Advanced State Management
Rather than building two separate pages, I engineered a single-page state machine:

Mode Toggling: A global switch triggers a state change that handles the synchronized crossfade of hero background videos and transitions the UI theme from "Warm Red" to "Dark/Black."

Zero-Height Layout Engine: To prevent layout shifts or overlapping when switching between dynamic product grids, I implemented a 0px Height / Overflow: Hidden logic. This ensures that the inactive mode's container is entirely collapsed, allowing the active grid to dictate the page's vertical flow and footer positioning.

2. Dynamic API Data Pipeline
The product section is built for scale, moving away from hardcoded content:

Storefront API Integration: Product cards are dynamically populated using a Repeater component bound to the Shopify Storefront API.

Variable-Based Filtering: The category tabs (Sleep, Travel, WFH, Gym, etc.) are wired to localized state variables. When a user selects a category, the variable updates, which in turn re-triggers the Shopify API filter to fetch and render products matching that specific tag in real-time.

3. Logic-Driven UI Components
I utilized Liquid-style expressions to handle data-heavy UI elements:

Calculated Discount Badges: Instead of static labels, I implemented a mathematical formula to calculate the exact discount percentage dynamically:
{{ (compare_at_price - price) / compare_at_price | times: 100 | round }}% OFF

Price Mapping: Both current and "compare-at" prices are mapped directly from the API, including logic to apply strikethrough styling only when an original price exists.

Sticky Navigation: Optimized the z-index and sticky positioning context to ensure the navigation bar remains the top-most layer (Z: 999) during complex video transitions.

📱 Responsive Strategy
While the reference design was mobile-centric, I extended the layout for Desktop/Large Screens:

Implemented a responsive grid that scales from a 2-column layout on mobile to a 4-column e-commerce grid on desktop.

Used Auto-Margins and Max-Width constraints (1200px) to ensure the landing page remains centered and professional on high-resolution displays.

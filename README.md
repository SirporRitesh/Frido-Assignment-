# Frido Valentine's Day Landing Page | Frontend Assignment

This repository documents the technical architecture and implementation details for the Valentine's Day Landing Page assignment. The project was built utilizing **Shopify**, **Replo**, and the **Shopify Storefront API** to create a highly dynamic, state-driven user experience.

## 🔗 Project Links
* **Live Store URL:** [Insert Live Link Here]
* **Technical Walkthrough Video:** [Insert Video Link Here]
* **Replo Editor Link:** [Insert Replo Link Here]

*(Note: The walkthrough video provides a complete demonstration of the live UI transitions, as well as a backend tour of the state variables and API bindings inside the Replo editor.)*

---

## 🎯 Project Overview
The objective was to build a dual-mode "Quick Commerce" style landing page featuring a seamless toggle between "Couple Mode" (warm theme) and "Single Mode" (dark theme). The page requires dynamic data fetching from the Shopify Storefront API, category filtering, and complex layout transitions without triggering page reloads.

## ⚙️ Technical Architecture & Implementation

Instead of relying on static drag-and-drop elements, I engineered a custom state-machine and dynamic data pipeline using Replo's visual logic tools.

### 1. State Management & UI Transitions
* **The Global Toggle:** Implemented a global state toggle that drives the entire hero section. Clicking the switch updates the global state, triggering a smooth, crossfade animation between the respective background videos.
* **Dynamic Layout Engine:** To swap the product grids dynamically without breaking the flex-based document flow or overlapping elements, I utilized a `0px Height / Overflow: Hidden` state architecture. This ensures that inactive product grids completely collapse, preserving the structural integrity of the page and footer layout.
* **Z-Index Layering:** Configured precise `z-index` stacking contexts (e.g., setting the sticky navigation to `z-index: 999`) to ensure the navigation bar sits cleanly above the crossfading hero videos and toggle switch during scroll.

### 2. Shopify Storefront API Integration
* **Dynamic Rendering:** All product cards are rendered via a Repeater List hooked directly into the Shopify Storefront API.
* **State-Driven Filtering:** Built pill-based category tabs (e.g., *Sleep, WFH, Gym* for Couple Mode; *Mom, Dad, BFF* for Single Mode). Clicking a tab updates a localized state variable (e.g., `coupleCategory`). This variable is bound directly to the Storefront API filter parameters, instantly re-querying and re-rendering the product grid without any page reload.
* **Cart Integration:** Each product card includes an "Add to Cart" action wired to the Storefront API's cart mutation, enabling a fully functional add-to-cart flow.

### 3. Theme Switching (Couple Mode vs. Single Mode)
* **Couple Mode:** Warm, romantic color palette with a soft background video. Product categories: *Sleep, WFH, Gym*.
* **Single Mode:** Dark, moody theme with a contrasting background video. Product categories: *Mom, Dad, BFF*.
* The entire page — including the hero, product grid, category tabs, and color accents — transitions reactively based on the single global state toggle.

---

## 🧰 Tools & Technologies Used
| Tool | Purpose |
|---|---|
| **Shopify** | E-commerce backend, product catalog, and cart API |
| **Replo** | Visual page builder with state management and API binding |
| **Shopify Storefront API** | Dynamic product fetching and cart mutations |
| **CSS (via Replo)** | Crossfade animations, z-index layering, overflow hidden layout |
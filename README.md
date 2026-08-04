# Shopify Theme Development SOPs

This repository contains Standard Operating Procedures (SOPs) for custom Shopify theme development, focusing on reusable architecture patterns, complex Liquid logic, and front-end JavaScript integrations.

These documents serve as technical references for e-commerce engineers and developers maintaining enterprise-level Shopify storefronts.

## Available SOPs

| ID | Title | Description |
|----|-------|-------------|
| **[SOP-001](SOP-001-sold-out-availability-fix.md)** | Sold Out Badge and Add-to-Cart Availability Fix | Resolving storefront availability blockers caused by secondary inventory locations not enabled for online fulfillment. |
| **[SOP-002](SOP-002-conditional-display-collection.md)** | Conditional Display on Collection Pages | Passing parameters from collection sections to product-card snippets to hide pricing and badges based on product theme templates. |
| **[SOP-003](SOP-003-popup-form-system.md)** | Custom Popup Form System Architecture | Building a reusable, section-based popup form system with JavaScript instantiation and asynchronous Formspree submissions. |
| **[SOP-004](SOP-004-popup-faq-and-blog-triggers.md)** | Popup Form Triggers for FAQs and Blog Posts | Specialized implementation patterns for triggering popups via hidden buttons and JavaScript intercepts in complex sections. |

## Tech Stack Covered
- **Shopify Liquid:** Snippet rendering, variable scoping, parameter passing, and template suffix logic.
- **JavaScript:** Class instantiation, DOM manipulation, event interception, and asynchronous `fetch()` API usage.
- **Shopify Architecture:** Section/snippet relationships, schema configuration, metafields, and inventory location logic.

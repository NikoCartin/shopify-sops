# SOP-006: Get a Quote Button on Collection Pages

**Repository:** primal-strength-us-shopify  
**Theme:** ux-project / Live  
**Date:** August 2026  
**Author:** Niko Cartin

---

## Overview

This SOP documents how to display a "Get a Quote" pill-shaped button on collection page product cards for products that use the `quote` product template suffix. The button replaces the price display and links directly to the product's PDP.

---

## How It Works

Shopify passes a `show_price` variable from the collection grid section to the product card snippet. When a product has `template_suffix == 'quote'`, `show_price` is set to `false`. The product card snippet uses this variable to conditionally render either the price or the "Get a Quote" button.

---

## Files Modified

| File | Type | Change |
|------|------|--------|
| `sections/main-collection-product-grid.liquid` | Section | Sets `show_price = false` for quote template products |
| `snippets/product-card.liquid` | Snippet | Renders "Get a Quote" button when `show_price == false` |

---

## Step 1: `sections/main-collection-product-grid.liquid`

Inside the `{%- for product in collection.products -%}` loop, locate the `show_price` assignment block and ensure it reads exactly as follows. Remove any `is_quote` variable or separate button block outside the `product-card` render call.

```liquid
{%- assign show_price = true -%}
{%- if product.template_suffix == 'quote' -%}
  {%- assign show_price = false -%}
{%- endif -%}

{% render 'product-card',
  card_product: product,
  show_secondary_image: section.settings.show_secondary_image,
  show_vendor: section.settings.show_vendor,
  show_description: section.settings.show_description,
  show_badge: section.settings.show_badge,
  lazy_load: lazy_load,
  comparison: false,
  show_price: show_price
%}
```

**Important:** Do NOT add any additional button block after the `product-card` render. The button must live entirely inside the card snippet.

---

## Step 2: `snippets/product-card.liquid`

Locate the price render block and add an `{%- else -%}` clause that renders the "Get a Quote" button when `show_price` is `false`.

```liquid
{%- if show_price != false -%}
  {% render 'price', product: card_product, price_class: 'product-price', custom_inventory: false, rrp: false %}
{%- else -%}
  <a
    href="{{ card_product.url }}"
    style="
      display: block;
      text-align: center;
      border: 1.5px solid #111;
      border-radius: 50px;
      padding: 8px 16px;
      font-size: 0.75em;
      font-weight: 600;
      letter-spacing: 0.08em;
      text-decoration: none;
      text-transform: uppercase;
      color: #111;
      background: transparent;
      margin-top: 8px;
    "
  >
    Get a Quote
  </a>
{%- endif -%}
```

---

## Common Mistakes to Avoid

| Mistake | Result | Fix |
|---------|--------|-----|
| Adding a separate button block outside the `product-card` render in the collection grid | Button renders twice, breaks grid layout | Remove the external block; button must be inside the card snippet only |
| Using `card_product` variable in the collection grid file | Variable is undefined outside the snippet scope | Use `product` in the collection grid and `card_product` inside the snippet |
| Adding `is_quote` variable and rendering button in the grid | Duplicate rendering, layout broken | Use only `show_price` passed to the snippet |
| Placing the button in `card-badges.liquid` | Badge renders on the image overlay, not below the title | The button belongs in `product-card.liquid` inside the content area |

---

## How to Apply to a New Product

To make a product show "Get a Quote" instead of a price on collection pages:

1. Go to the product in Shopify Admin
2. In the top right, find **Theme template**
3. Change the template from `Default product` to `quote`
4. Save the product

The button will appear automatically on all collection pages without any additional code changes.

---

## Related Files

- `snippets/card-badges.liquid` - Badge overlays on product images (SALE, Sold Out, custom labels). Do not add the Get a Quote button here.
- `templates/product.quote.json` - The quote product template that triggers the PDP quote form

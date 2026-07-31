# SOP-002: Conditional Display on Collection Page Based on Product Theme Template

**Category:** Shopify Theme Development  
**Applies to:** Custom Shopify themes using a product-card snippet rendered from a collection section

## 1. Purpose
This SOP documents two related customizations for Shopify collection pages:
1. Hiding the price on the collection page for products assigned a specific theme template (e.g., `quote`), so that "request a quote" products do not display pricing to customers.
2. Understanding how product card badges (e.g., "SALE", "PACKAGE OFFER") are rendered and how to remove them — either per-product via metafields or globally via code.

## 2. Background & Concepts

### 2.1 How Shopify Theme Templates Work
Every product is rendered using a template file. When a product is assigned an alternate template named `quote`, Shopify stores the suffix `'quote'` in the `product.template_suffix` property. This property is accessible anywhere the product object is in scope, including inside a collection loop.

### 2.2 Passing Parameters to Snippets
When a section renders a snippet using `{% render 'snippet-name', param: value %}`, variables defined outside the snippet must be explicitly passed to be available inside it. This mechanism communicates the `show_price` flag from the collection section to the product card snippet.

### 2.3 Why `show_price != false`
In Liquid, an unassigned variable evaluates to `nil`. Using `show_price != false` means the condition passes for both `true` and `nil`. This ensures the price renders normally in all contexts where the snippet is called without the `show_price` parameter, preventing breakages in other sections.

### 2.4 Product Card Badges
Badges such as "SALE" are stored as values in the `custom.badges` metafield on each product. They are managed at the product data level, not hardcoded in the template logic.

## 3. Procedure A — Hide Price for Products with a Specific Template

### Step 1: Add the condition to the collection grid
1. Open `sections/main-collection-product-grid.liquid`.
2. Locate the `{%- for product in collection.products -%}` loop and the `{% render 'product-card' %}` call.
3. Immediately before the render call, insert:
   ```liquid
   {%- assign show_price = true -%}
   {%- if product.template_suffix == 'quote' -%}
     {%- assign show_price = false -%}
   {%- endif -%}
   ```
4. Pass the parameter into the snippet:
   ```liquid
   {% render 'product-card',
      card_product: product,
      show_price: show_price
   %}
   ```

### Step 2: Wrap the price render call in the snippet
1. Open `snippets/product-card.liquid`.
2. Locate the line rendering the price: `{% render 'price', product: card_product %}`.
3. Wrap it with the condition:
   ```liquid
   {%- if show_price != false -%}
     {% render 'price', product: card_product %}
   {%- endif -%}
   ```

## 4. Procedure B — Managing Product Card Badges

### Option 1: Remove a badge from a specific product (Recommended)
1. Go to **Shopify Admin > Products** and open the target product.
2. Scroll to the **Metafields** section.
3. Locate the `custom.badges` field and clear the badge value.

### Option 2: Conditionally hide badges for specific templates (Code change)
In `snippets/product-card.liquid`, wrap the badge render call with the same condition used for pricing:
```liquid
{%- if show_price != false -%}
  {% render 'card-badges', card_product: card_product %}
{%- endif -%}
```

## 5. Verification
- Browse a collection containing a product with the `quote` template; the price should be hidden on the card.
- Browse a collection containing a standard product; the price should display normally.
- Open the PDP of both product types; price behavior on the PDP should be completely unaffected.

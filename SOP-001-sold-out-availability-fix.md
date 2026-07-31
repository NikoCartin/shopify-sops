# SOP-001: Sold Out Badge and Add-to-Cart Availability Fix

**Category:** Shopify Theme Development  
**Applies to:** Custom Shopify themes where inventory locations block storefront availability

## 1. Purpose
This SOP documents the procedure to resolve a critical storefront bug where products incorrectly display a "Sold Out" badge and the "Add to Cart" button is disabled, despite having inventory available in secondary distribution centers.

## 2. Background & Concepts
Shopify evaluates product availability (`product.available`) based on inventory levels across locations. If a product is stocked in a location that is *not* enabled for online order fulfillment, Shopify treats the product as unavailable for online storefront rendering. 

When this occurs, themes typically block the add-to-cart action via JavaScript validation and render hardcoded "Sold Out" badges via Liquid logic. Resolving this requires both a Shopify Settings adjustment and theme code refactoring to ensure dynamic availability rendering.

## 3. Procedure

### Step 1: Enable Online Fulfillment for the Inventory Location
Before modifying theme code, ensure the inventory location is authorized to fulfill online orders.
1. Navigate to **Shopify Admin > Settings > Locations**.
2. Select the relevant distribution center (e.g., "USA - BNB DISTRIBUTIONS").
3. Ensure the location is not set as the "Default location". If it is, change the default to another location first.
4. Toggle **"Use inventory at this location to fulfill online orders"** to **ON**.
5. Enable **Shipping** for the location and configure the appropriate shipping zones.

### Step 2: Fix the Add-to-Cart Button Liquid Logic
Remove hardcoded `disabled` states from the buy button snippet to allow the form to process availability dynamically.
1. Open `snippets/buy-button.liquid`.
2. Locate the `<button>` element for the add-to-cart action.
3. Remove static `disabled` attributes and hardcoded "Out of stock" text fallbacks.
4. Ensure the button text relies dynamically on `product.available`.

### Step 3: Fix JavaScript Inventory Validation
Prevent theme JavaScript from preemptively blocking the add-to-cart action for items stocked in secondary locations.
1. Open `assets/gaia-product-form.min.js` (or equivalent form handler).
2. Locate the validation block checking for inventory quantities.
3. Add a condition to bypass the block if the inventory policy allows continued selling:
   ```javascript
   if (inventory_policy !== "continue") {
       // Proceed with strict inventory blocking
   }
   ```

### Step 4: Restore Dynamic Sold Out Badges
Ensure the "Sold Out" badge only renders when a product is genuinely unavailable across all active fulfillment locations.
1. Open `snippets/card-badges.liquid`.
2. Locate the badge rendering condition.
3. Remove any overriding static conditions (e.g., `{%- if false -%}`).
4. Implement the correct dynamic condition:
   ```liquid
   {%- if card_product.available == false -%}
     <span class="badge price__badge-sold-out">Sold Out</span>
   {%- endif -%}
   ```

## 4. Verification
1. Open the storefront and navigate to a product stocked exclusively in the secondary location.
2. Verify the "Sold Out" badge does not appear.
3. Verify the "Add to Cart" button is active and functional.
4. Navigate to a genuinely out-of-stock product and verify the "Sold Out" badge renders correctly.

# SOP-004: Popup Form Triggers for FAQs and Blog Posts

**Category:** Shopify Theme Development  
**Applies to:** Custom Shopify themes utilizing the `PopupForm` JavaScript class

## 1. Purpose
This SOP documents specialized implementation patterns for triggering popup forms from complex sections, specifically the FAQs (Advanced) section and Blog Post templates, which require deviations from the standard Media Banner trigger pattern.

## 2. Triggering Popups from the FAQ Section

### 2.1 The Challenge
The FAQs section uses a `component-heading` snippet to render its heading and CTA button. This snippet renders a standard `<a>` tag, not the `<button class="open-popup">` required by the `PopupForm` class. Therefore, the popup system cannot detect clicks natively.

### 2.2 The Solution
The solution requires a two-part implementation in `sections/faq.liquid`:

**Part A: The Hidden Trigger**
A hidden button with the correct class and data attributes is injected into the DOM when the popup feature is enabled.
```liquid
{%- if section.settings.use_popup_form and section.settings.shared_popup_id != blank -%}
  <!-- The visible CTA rendered by component-heading -->
  {% render 'component-heading', cta_link: '#', cta_label: section.settings.button_text %}
  
  <!-- The hidden trigger button -->
  <button type="button" class="open-popup" data-id="{{ section.settings.shared_popup_id }}" style="display:none;" aria-hidden="true"></button>
{%- endif -%}
```

**Part B: The JavaScript Intercept**
A script listens for clicks on the visible `<a>` tag, prevents default navigation, and programmatically clicks the hidden trigger button.
```javascript
document.addEventListener('DOMContentLoaded', function() {
  var faqSection = document.getElementById('FaqSection--{{ section.id }}');
  if (!faqSection) return;
  
  // Select the visible link inside the heading container
  var ctaLink = faqSection.querySelector('.section-heading-container a');
  var hiddenTrigger = faqSection.querySelector('.open-popup[data-id="{{ section.settings.shared_popup_id }}"]');
  
  if (!ctaLink || !hiddenTrigger) return;
  
  ctaLink.addEventListener('click', function(e) {
    e.preventDefault();
    hiddenTrigger.click();
  });
});
```

### 2.3 Troubleshooting FAQ Triggers
If the popup fails to open from an FAQ section, the most common cause is a broken CSS selector in the JavaScript intercept. Verify that `document.querySelector('.section-heading-container a')` successfully targets the visible CTA element. If the `component-heading` snippet is modified to use a different wrapper class, this selector must be updated.

## 3. Implementing Popups on Blog Posts

### 3.1 Template Architecture
Unlike standard pages, blog posts utilize article templates. The method for adding a popup depends on whether the blog post uses a shared or unique template.

### 3.2 Procedure
1. Open the Theme Editor and navigate to the target blog post.
2. If the blog post uses a **unique template**, add the Popup Form section to the template and copy its unique section ID from the URL.
3. If the blog post uses a **shared template** (e.g., `default article`), the section only needs to be added once. The same section ID will apply to all posts using that template.
4. Configure the trigger section (e.g., Media Banner) by pasting the section ID into the **Shared popup ID** field.

*Note: Because shared templates use the same section ID across all associated URLs, the hidden metadata fields in the form snippet (like `page_url`) are critical for identifying which specific blog post generated the submission.*

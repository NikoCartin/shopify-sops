# SOP-003: Custom Popup Form System Architecture and Implementation

**Category:** Shopify Theme Development  
**Applies to:** Custom Shopify themes utilizing the `PopupForm` JavaScript class

## 1. Overview
This SOP documents the architecture and implementation process for adding popup inquiry forms to any Shopify page. The system utilizes a reusable JavaScript class (`PopupForm`) and routes all submissions via `fetch()` to a Formspree endpoint, preventing page reloads upon submission.

## 2. Architecture
The popup form system requires three components working in tandem:

1. **The Form Snippet (`snippets/[name]-form.liquid`)**: Contains the form fields, HTML structure, and the Formspree JavaScript fetch logic.
2. **The Popup Wrapper Section (`sections/popup-form-[name].liquid`)**: Renders the hidden modal container, includes the snippet, and initializes the `PopupForm` class.
3. **The Trigger Button**: A CTA on the page (typically inside a Media Banner) configured to pass the wrapper section's unique ID.

## 3. How the `PopupForm` Class Works
The class is instantiated within the wrapper section:
```javascript
new PopupForm(
  '#{{ section.id }}', // The hidden popup wrapper
  '.open-popup[data-id="{{ section.id }}"], .form__close-btn[data-id="{{ section.id }}"]', // Triggers
  'body'
);
```
When a user clicks an element matching the trigger selector, the class removes the hidden state from the wrapper. The critical requirement is that the trigger button must have `class="open-popup"` and a `data-id` matching the popup section's ID.

## 4. Procedure: Adding a Popup to a Page

### Step 1: Create the Form Snippet
Create `snippets/[purpose]-form.liquid` (e.g., `leasing-inquiry-form.liquid`).
- Include standard inputs and hidden metadata fields (`_subject`, `form_source`, `page_url`).
- Include the Formspree honeypot: `<input type="text" name="_gotcha" style="display:none">`.
- Include the submission script to intercept the submit event, send FormData via `fetch()`, and display a success message without reloading the page.

### Step 2: Create the Popup Wrapper Section
Create `sections/popup-form-[purpose].liquid`.
- Render the outer wrapper with `id="{{ section.id }}"` and the `popup-form--hidden` class.
- Include a close button with `class="form__close-btn" data-id="{{ section.id }}"`.
- Render the snippet from Step 1.
- Include the `<script>` block to instantiate `PopupForm` on `DOMContentLoaded`.

### Step 3: Add the Section to the Target Page
1. Open the Theme Editor and navigate to the target page.
2. Add the newly created Popup Form section to the page.
3. Select the section and copy its unique ID from the browser URL (everything after `&section=`, e.g., `template--19084232261731__popup_form_leasing_q7AJFz`).

### Step 4: Connect the CTA Trigger
1. In the Theme Editor, select the section containing the CTA (e.g., Media Banner).
2. Toggle "Use CTA to open popup form" to ON.
3. Paste the section ID copied in Step 3 into the **Shared popup ID** field.
4. Save and test on the live preview URL.

## 5. Troubleshooting
- **Popup doesn't open:** Verify the `Shared popup ID` in the trigger section exactly matches the section ID in the URL. Ensure you are testing on the live preview, as the Theme Editor blocks `javascript:void(0)` links.
- **"Can't include Liquid syntax" error:** This occurs if Liquid tags are pasted directly into a Theme Editor text field. Use the `Shared popup ID` field instead.
- **Logo renders as a square:** If using a square logo file, force rectangular rendering by setting explicit width and height attributes (e.g., `width="300" height="108"`) without using `max-width` alone.

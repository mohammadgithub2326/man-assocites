# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.# 📘 Project Best Practices

## 1. Project Purpose
MAN Associates website is a static, multi-page marketing site for a manpower/facilities management company. It showcases services (security, e-security, housekeeping, office support, pantry), locations, company profile, and provides conversion paths via quote request forms and direct call/WhatsApp links.

Primary goals:
- Present offerings and credibility (certifications, industries, stats)
- Drive inquiries via quote forms and calls
- Deliver a responsive, performant, and accessible experience

## 2. Project Structure
Current layout (root-level static site):
- index.html (landing + form)
- About_Us.html, Our_Services.html, Events_Gallery.html, Contact_Us.html, … other pages
- Shared visual/behavior patterns are duplicated across pages (header, footer, back-to-top, counters)
- Assets at root: videos (mp4), images (png), icons
- External dependencies via CDN in each HTML file

Key roles:
- Header and Footer: Navigation, branding, contact info, policy links (Privacy/Terms/Cookie)
- Hero sections and content blocks: Tailwind utility classes for layout and theming
- Forms: Integrated with formsubmit.co for email submissions + honeypot and captcha disabled flag
- Scripts: Inline per page, wrapped with DOMContentLoaded for safety; custom feature IDs used as hooks
- Tailwind: CDN runtime with a small tailwind.config object (primary/secondary colors, radii)

Recommended structure improvements (non-breaking, incremental):
- Organize assets:
  - /assets/img (png, jpg, svg)
  - /assets/video (mp4)
  - /assets/css (optional future external CSS)
  - /assets/js (optional future shared JS)
- Consider centralizing header/footer and shared widgets (modals, back-to-top, counters) into shared includes or a small JS injection utility to reduce duplication.
- Standardize file names to kebab-case and correct typos (e.g., security-guard-service.html) with proper link updates.

## 3. Test Strategy
Given a static site, focus on functional, accessibility, and performance checks.

- Frameworks: None (no unit test framework in use). Prefer lightweight validation/QA tools.
- Test organization: Manual checklists + automated site scans on each page change.

Recommended testing guidelines:
- Functional
  - Verify all nav links, CTA buttons, tel:/mailto:, and WhatsApp links resolve correctly across pages.
  - Validate forms (required fields, hidden honeypot, proper action to formsubmit.co).
  - Ensure modals, dropdowns, and back-to-top work and are keyboard accessible where possible.
- Accessibility (a11y)
  - Use axe DevTools or Lighthouse a11y audits.
  - Ensure alt text on all images; meaningful link text; sufficient color contrast; focus states visible.
- Performance
  - Run Lighthouse: target 90+ scores; avoid large blocking resources; prefer modern image sizes.
  - Defer heavy animations and keep videos optimized; set video attributes (muted, playsinline, preload as needed).
- HTML/CSS/Link validation
  - W3C HTML validator for each page.
  - Check broken links (e.g., linkinator or online link checkers).
- Cross-browser/mobile
  - Smoke test on latest Chrome, Edge, Firefox, Safari; Android + iOS.

When/How to add automated tests:
- If extracting shared JS into modules, consider minimal unit tests for logic (e.g., dropdown, counter, modal) using a lightweight runner (Jest) in a separate tooling folder without blocking static hosting.

## 4. Code Style
Languages: HTML5, inline CSS (Tailwind utilities + small custom styles), vanilla JS in-page scripts.

- HTML
  - Semantic tags where applicable (header, main, section, footer).
  - Keep indentation consistent (2 spaces or 4 spaces, be consistent per file).
  - Prefer descriptive IDs and data-attributes for JS hooks (e.g., data-counter, data-modal).
  - Always supply alt text for images and descriptive titles for iframes/videos if added.
- Tailwind & CSS
  - Continue using Tailwind CDN; keep app-specific styles minimal in <style> blocks.
  - Use the defined palette: primary #0B3C5D, secondary #328CC1; reuse utility classes consistently.
  - Avoid long, duplicated utility chains across pages; consider composing via small component classes if repeated.
- JavaScript
  - Wrap DOM logic in DOMContentLoaded to prevent null references.
  - Guard optional APIs (e.g., requestVideoFrameCallback) with feature detection and fallbacks.
  - Avoid polluting the global scope; keep variables/function names localized to script blocks.
  - Prefer addEventListener over inline on* handlers; if inline is used, keep to essential navigation only.
  - Use early returns and null checks when querying elements by ID/class.
- Naming conventions
  - Files: prefer kebab-case (about-us.html). If renaming, update all references.
  - IDs: kebab-case (back-to-top, service-dropdown-menu).
  - CSS helper classes: kebab-case (font-poppins), keep consistent across pages.
- Comments/Docs
  - Document non-trivial animations and logic blocks (counters, splash, modals) with brief comments.
  - Keep script blocks labeled via unique script IDs already in use (e.g., <script id="counter-animation">…).
- Errors & Exceptions
  - Defensive checks around querySelector results; avoid assuming elements exist on every page.
  - For external resources (CDN, images), gracefully handle failures (e.g., alt text, fallback background colors).

## 5. Common Patterns
- Layout
  - Fixed header with shadow on scroll; responsive sections built with Tailwind grid/flex utilities.
  - Footer with social links and policy pages.
- Components/Behaviors
  - Splash screen with controlled fade-out.
  - Back-to-top button visibility toggled by scroll threshold.
  - Counter animation: IntersectionObserver + requestAnimationFrame.
  - Service modals (Our_Services) with overlay and Escape/click-outside handling.
  - Dropdown for service selection in forms with hidden input sync.
  - Floating WhatsApp action button; tel and mailto links.
- Theming
  - Two-color theme (primary/secondary) reused in gradients and accents.
  - Google Fonts: Poppins (headings), Lato (body).
- Icons
  - Remix Icon via CDN; global :where([class^="ri-"])::before override present (ensure it does not break icons).

## 6. Do's and Don'ts
- Do
  - Keep header/footer/nav structure consistent across all pages.
  - Reuse the primary/secondary palette and typography utilities.
  - Provide descriptive alt text, accessible labels, and keyboard-friendly interactions.
  - Validate links after file renames; update all references and sitemaps if added.
  - Keep script blocks small, scoped, and guarded by DOMContentLoaded.
  - Use data-* attributes for behavior toggles (e.g., data-counter, data-modal).
  - Optimize images (dimensions, compression) and preload critical fonts via Google Fonts where reasonable.
  - Maintain the _honey pot field and captcha settings for FormSubmit forms.
- Don't
  - Don’t duplicate complex logic across pages—extract to shared JS when changes become frequent.
  - Don’t introduce blocking, render-synchronous scripts/styles above the fold.
  - Don’t rely solely on external dynamic image URLs for critical content; use local fallbacks.
  - Don’t remove feature detections or null checks around optional APIs and selectors.
  - Don’t insert markdown code fences or non-HTML syntax into HTML files.

## 7. Tools & Dependencies
- Tailwind CSS via CDN: https://cdn.tailwindcss.com/3.4.16
  - Local tailwind.config object extends colors and radii per page.
- Google Fonts: Poppins, Lato
- Remix Icon: https://cdnjs.cloudflare.com/ajax/libs/remixicon/4.6.0/remixicon.min.css
- Form handling: https://formsubmit.co (with _honey and _captcha=false fields)
- Image placeholders: readdy.ai generated URLs (replace with final production assets when available)

Setup/Run
- No build step required. Open any HTML file in a browser.
- Recommended for local dev: VS Code + Live Server extension for auto-reload.
- Optional enhancements: If adding a toolchain, keep it opt-in and avoid breaking static hosting.

## 8. Other Notes
- Consistency hooks to preserve across pages:
  - IDs: header, back-to-top, service-dropdown, service-dropdown-menu, selected-service, service-type-hidden, current-year, etc.
  - Data attributes: data-counter on statistics, data-modal on service cards.
- SEO/Meta
  - Maintain descriptive titles and meta for each page; use consistent brand naming.
- Accessibility
  - Ensure focus management for modals; set aria-modal, role="dialog" if enhancing.
- Year auto-update
  - Keep the current-year script to avoid hardcoding.
- Typos/renames
  - If correcting filenames like ManPower_Catogeries.html or Security_Gaurd_Service.html, update every link site-wide.
- Videos
  - Keep attributes: autoplay loop muted playsinline; consider poster images and preloading strategies.
- Z-index layering
  - Maintain z-indices for splash, floating buttons, and modals to avoid interaction conflicts.

LLM guidance for new code in this repo:
- Follow Tailwind utility-first style; reuse primary/secondary colors and font utilities.
- Mirror existing header/footer and navigation; add new links consistently across all pages.
- Wrap any new JS with DOMContentLoaded, use null checks, and keep behavior isolated per page unless shared via a separate JS file.
- Prefer semantic HTML and accessible patterns; ensure keyboard and screen-reader compatibility.

# Master SVG Pipeline and Quality Standards

This is the specification the skill follows. Read it before building a design. Every number below is a real threshold from the pipeline, not a guideline.

## 1. Visual and Architectural Rules

- **Canvas and ViewBox:** Every SVG must use `viewBox="0 0 1024 1024" width="100%" height="100%"`.
- **85% Auto-Centering Normalization:** Normalize the bounding box of all contours so the longest dimension equals `870.0 px` (`85%` of `1024 px`), centered at `(512.0, 512.0)`. This guarantees at least a `77 px` safety margin on every side.
- **Single-Path Compound Stencil Format:**
  - Single `<path fill-rule="evenodd" fill="#111827" d="..." />` element.
  - Zero `<image>`, `<clipPath>`, `<mask>`, `<filter>`, or external stylesheet dependencies.
- **Multi-Layer Flat-Vector Color Format:**
  - Grouped inside `<g id="Design-Name">`, with one `<path id="layer-name" fill="#HEX" d="..." />` per color group, ordered back-to-front: base and background shapes first, then the main body, then middle details, then foreground features, then the smallest top accents last.
- **Stencil Separation Moats:** When converting a multi-layer colored design into a 1-color stencil cut file, render `14 px` to `24 px` negative-space separation moats (`#FFFFFF`) or bold outlines (`#000000`) between overlapping layers so every feature remains distinct and weedable.

## 2. Contour Extraction and Curve Fitting

- **Margin Clearing:** Zero out the outer `8 px` border (`img[:8,:]=255`, etc.) before thresholding to eliminate edge artifacts.
- **4x Lanczos Supersampling:** Resize `1024x1024` inputs to `4096x4096` via `cv2.INTER_LANCZOS4`, apply `cv2.GaussianBlur(img_up, (7, 7), 1.6)`, and threshold at `180` (`cv2.THRESH_BINARY_INV`).
- **Dust and Micro-Speck Filtering:** Drop contours with `area < 350` in `4x` space (or `< 650` if total sub-paths exceed `120`) and `perim < 10.0` in `1x` space.
- **Exact Circle Recognition:** For small round contours (`perim_norm < 55.0` and `circularity > 0.82`), output an exact 4-segment cubic Bezier circle (`k = 0.55228475 * r`).
- **Corner-Aware Adaptive Cubic Bezier Fitting:** Detect sharp corners (`angle > 48 deg`) and fit smooth least-squares cubic Bezier chains (`C` commands) between corners (`max_err = 0.9` to `1.0`).
- **No Polygon-Only Dumps:** Never ship a raw `cv2.approxPolyDP` contour (`L` commands only). Fit smooth cubic Bezier (`C`) curves so cutting blades glide smoothly.

## 3. Cut-Safety Checks

These sit on top of the pipeline standards and are checked by `validate.py` in the premium pack.

- **Node budget:** About 1,000 nodes for HTV, about 1,500 for permanent vinyl. Over budget is a warning; simplify until under it.
- **Stroke-to-fill:** No `stroke` attributes with a real color anywhere. Output filled shapes only.
- **Overlaps:** Overlapping paths in the same fill must be welded or subtracted, or they cut into each other.
- **Acute angles:** Internal corners sharper than 30 degrees on short edges tear vinyl during weeding. Soften or thicken them.
- **ViewBox integrity:** The viewBox must be present and exactly `0 0 1024 1024`. A missing or damaged viewBox imports at a massive or microscopic scale.

## 4. Pre-Flight Validation and Packaging

- **Programmatic XML Check:** Validate every `.svg` file with `xml.etree.ElementTree.parse()`.
- **Rendered Visual QA:** Render every `.svg` via `cairocffi` (`cairo.FILL_RULE_EVEN_ODD`) to `1024x1024` transparent PNGs and a high-resolution `00-Bundle-Preview-Catalog.png` contact sheet.
- **Validator:** Run `validate.py` on every SVG before packaging. Errors are not shipped; warnings are notes.
- **Clean Workspace Rule:** Build all intermediate files in `/tmp/` and remove any temporary files from the current working directory before creating the final `.zip` archive so only the `.zip` is delivered.
- **No Cut-Test Claims:** The validator is a static pre-check. No machine cut test is claimed for any file in this skill. The buyer test-cuts on their own machine.

# MathJax-Tex-Converter
The LMS WYSIWYG editor rewrites or strips TeX delimiters like \(...\), \[...\], $$...$$, and sometimes backslashes, so equations render until the next edit, then degrade to plain text.

Solution overview
We deploy a lightweight JavaScript helper (via the LMS “Custom HTML” area) alongside MathJax v3. Authors store TeX inside safe attributes:

Inline math:
<span data-tex-inline=" \frac{85}{100} = 0.85 "></span>

Display/centered math:
<div data-tex-display=" \text{F1} = 2\cdot \frac{PR}{P+R} "></div>

At runtime, the script:

Finds placeholders with data-tex-inline or data-tex-display.

Normalizes backslashes so authors can use \, \\, &bsol;, or &#92; inside attributes.

Materializes TeX delimiters by wrapping content as \( … \) (inline) or \[ … \] (display).

Calls MathJax (typesetPromise) to render the math into scalable SVG.

Dynamic re-typesetting
A MutationObserver watches for DOM insertions (e.g., accordion opens, lazy-loaded lessons, returning from the HTML editor). When new content appears, the script re-runs the convert-and-typeset step so equations stay rendered everywhere.

MathJax configuration

Uses MathJax v3 (tex-svg output).

Enables the AMS package so environments like \begin{align}...\end{align} work.

Sets processEscapes: true for robust backslash handling.

Skips known non-content tags (script, style, pre, code, etc.) to avoid false positives.

Global SVG font cache for performance.

Authoring convention

Use data-tex-inline for inline math, data-tex-display for block math.

Write normal TeX inside the attribute. For line breaks in display math, use \\.

Avoid $...$ and raw \(...\)/\[...\] in the editor; the script adds those at runtime.

Performance

The helper is tiny; work scales with the number of equations on the page.

MathJax typesetting runs once on load and again only when new nodes are added.

No server changes or page reloads required.

Security & scope

The script only reads data-attributes and sets inner HTML to TeX delimiters; it does not execute user content or eval code.

It touches only explicit math placeholders and leaves all other HTML unchanged.

Accessibility

MathJax’s SVG output is screen-reader friendly; we can optionally load the MathJax a11y extension if desired.

Browser compatibility

Works on all modern browsers that support MutationObserver (widely supported). No IE support is targeted.

Operational impact

One-time migration: convert existing equations to the placeholder form.

Ongoing authoring: continue using the placeholder attributes; equations remain editable and won’t break on future edits.

Optional enhancements

Pin MathJax to a specific v3.x version or self-host if you prefer no CDN dependency.

Add a small authoring guide and regex recipes to convert legacy content quickly.

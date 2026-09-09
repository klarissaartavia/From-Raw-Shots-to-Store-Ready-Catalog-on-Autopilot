# Case Study: Building a Repeatable Product Image Workflow for Elyon Industrial

## Project context

For my client Elyon Industrial, I built a system to automate the preparation of product images from a photography shoot. The objective was to turn the source material into consistent catalog and website assets, including branded versions and the required image formats.

I designed the workflow around both creative requirements and operational details: what the product should look like, which source image belongs to which item, how outputs should be organized, and how rejected images should move through another correction round.

My contribution centered on connecting those requirements into a working production process: product prompt configuration, Python automation, branding rules, file handling, and human quality review.

## The production challenge

Industrial products contain small details that matter. A button with an LED, a button without an LED, and a pilot light can look similar while representing different products. Terminal positions, housing proportions, label text, and color variants can also determine whether an image represents the right item.

An image model can improve presentation while accidentally changing those details. At the same time, even a visually correct image can become unusable if it has the wrong filename, a missing angle, an old watermark version, or an incorrect export format.

The workflow therefore needed to manage image quality and the relationships between files throughout production.

## Approach

### 1. Give each image a stable identity

The source naming convention connects an inventory row, view number, internal product code, and brand. An illustrative filename is:

```text
12.1_INTERNAL-CODE_BRAND.png
```

Here, `12` identifies the inventory row and `1` identifies the view. This example is schematic, rather than a client SKU.

Product configuration modules describe the expected items and views. They provide the shared visual description, the instruction for each angle, and the cleanup requirements. This gives the generation process structured context while keeping the processing stages reusable across brands.

Products can have different numbers of legitimate views. The production lessons call for numbering those views consecutively from one, so the filename convention and export rules remain aligned.

### 2. Generate from a reference with explicit preservation instructions

The image editing step uses an existing product photo as its reference. Prompts request a clean white background, improved lighting and presentation, and preservation of the product's physical details.

These prompts communicate the intended result; they do not guarantee fidelity. That distinction is why visual review remains part of the workflow.

The implementation contains a browser-based generation path using Playwright and a later direct API path. The API path reuses the same product and prompt-building logic and supports per-image quality settings and correction instructions. It also handles rate-limit responses with bounded retries.

### 3. Apply branding through repeatable image processing

The standard logo step composites a prepared Elyon logo asset onto the generated image. The watermark stage applies a diagonal pattern using product masking, with light or dark treatment based on the product's appearance.

Thin cables exposed a limitation of the standard approach: a pattern and mask suitable for a broad product surface can spill beyond a narrow cable or barely touch it. The implementation includes a dedicated thin-product path that adjusts the pattern to the measured cable thickness and composites the corner logo at a different point in the process.

This exception illustrates how a shared production system can accommodate different product geometry through explicit rules.

### 4. Produce consistent delivery versions

The export stage creates three sets on each side of the watermark split:

- Source-format PNG/JPG images.
- A mixed set with the first view in PNG and subsequent views in WebP.
- An all-WebP set.

The label “No watermark” refers to the absence of the diagonal watermark. For standard products, those files can still carry the Elyon corner logo. Thin-product handling has its own exception.

The resulting six-folder structure is part of the delivery convention. Converting a JPG source to PNG satisfies a format requirement; it does not recover image detail lost in the original JPG.

### 5. Build review and recovery into the process

The local QC interface groups views by product and places source and final images side by side. The reviewer can approve or reject a product, record a reason, select the affected stage, and choose API generation quality when regeneration is required.

Automated warnings assist with file parity, logo presence, and watermark differences. Those checks are useful signals, while human comparison determines whether the product itself is represented faithfully.

For generation defects, the recovery code archives existing outputs for the selected images and queues them for regeneration. This invalidates dependent versions so an old export does not remain attached to a newly corrected source. Watermark and format problems have narrower reprocessing paths.

The process also creates follow-up review reports for rejected subsets. An operator still needs to interpret ambiguous comments, handle structural changes, and launch regeneration where required.

## Lessons from real corrections

| Issue encountered | What it taught me |
|---|---|
| A prompt template contained escaped braces, leaving a product instruction as literal text | Inspect the assembled prompt, including whether product-specific instructions actually appear |
| Different folder naming conventions allowed an old watermarked output to survive a correction | Resolve real output locations consistently and check that derived files follow the corrected source |
| Similar-looking product categories were confused during review | Use inventory descriptions and exact product attributes to support visual judgment |
| A correction comment referenced an image for the wrong product | Open and inspect replacement references before using them |
| Repeating a correction script affected items already fixed in an earlier round | Limit each correction run to its intended files and verify coverage afterward |
| An object in the logo corner triggered the logo-presence heuristic | Treat automated image checks as fallible and retain visual inspection |

These lessons were recorded in the production project's QC notes for future runs. That is maintained operational knowledge; it is not an autonomous learning mechanism.

## Outcome and practical limits

The project connected image editing, branding, exports, review, and correction into a reusable client workflow. It made routine transformations executable through scripts and gave rejected images a defined route back through production.

The code supports recovery and reuse, but existing-file checks do not prove that an output is current or visually correct. Some problems required one-off repair scripts and manual judgment. The original implementation also uses local filesystem paths and client-specific configuration, so this documentation package is not a portable application release.

No audited before-and-after timing, total generation cost, final acceptance rate, or completed-image total is included here. The business value described is the implemented production capability and the repeatability it enables.

## Connection to my next learning step

This work helped me understand where AI agents could be useful. The workflow already has tools, saved state, product context, and a feedback process. Its next actions are largely prescribed by code or chosen by an operator.

A possible extension would be an assistant that reads a rejection, inspects the relevant images, chooses an appropriate correction tool, and returns another result for review within a fixed attempt limit. That is a future direction, separate from the system described in this case study.

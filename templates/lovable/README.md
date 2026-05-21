# Lovable Prototype Templates

A pattern for saving Lovable credits when building multiple feature prototypes for the same product.

## The Problem

Every new Lovable project regenerates the same app shell — sidebar, header, navigation. That's repeated credit spend on UI you've already designed.

## The Pattern

Build your product's app shell **once** in Lovable, save it as a "base project," then **duplicate** the base for each new feature instead of regenerating from scratch.

## One-Time Setup

1. Write a Lovable prompt that generates your product's standard app shell (whatever combination of sidebar, header, top nav, tabs, etc. is shared across features).
2. Save that prompt at `templates/lovable/base.md` so it's checked into the repo. (This file isn't shipped with the template — you author it once, tailored to your product.)
3. Paste the prompt into Lovable and generate the shell.
4. Save the result as your "Base" project in your Lovable account.

## Per-Feature Workflow

For each new feature prototype:

1. **Duplicate** the saved Base project in Lovable. Rename to the feature name.
2. **Generate the feature-specific sections** with `/lovable-structured` (run from your project workspace — it reads the PRD/solution design and produces sectioned prompts).
3. **Paste** the feature sections into the duplicated project.

You only pay credits for the feature-specific work; the shell comes free with the duplicate.

## Notes

- `base.md` is product-specific. There's no canonical version shipped here — each forker writes the one that fits their product's design system.
- If you want to regenerate the shell from scratch instead of duplicating (e.g., the design has drifted), just paste `base.md` into a new Lovable project — but you'll pay credits.

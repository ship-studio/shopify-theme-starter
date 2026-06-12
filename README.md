# Shopify Theme Starter

Build a Shopify storefront theme with your AI agent. A complete Online Store 2.0 theme — clean, minimal, and ready to make your own.

## What is this?

A starter Shopify theme for building storefronts with Claude Code inside [Ship Studio](https://www.ship.studio). Open the project, connect your store, and Ship Studio runs the theme preview with your real products and collections — changes hot-reload as your agent works. Just describe what you want, and your agent handles the Liquid, sections, and CSS.

## Getting Started

Open the project in Ship Studio. It will ask you to connect a Shopify store, then the storefront preview appears in the workspace with live data from that store.

**You'll need a Shopify store.** Don't have one? Free development stores are available through [Shopify Partners](https://www.shopify.com/partners).

## Project Structure

```
layout/          The HTML shell that wraps every page
templates/       One JSON file per page type (home, product, cart...)
sections/        The building blocks — header, hero, product grid, etc.
snippets/        Reusable pieces like product cards and prices
config/          Theme settings (colors, fonts, page width)
locales/         All storefront text, ready for translation
assets/          theme.css and a tiny theme.js
CLAUDE.md        Instructions for Claude Code
```

## Publishing

When your theme is ready, ask your agent to run `shopify theme push` — it uploads the theme to your store, where you can preview it in the Shopify admin and publish it when you're happy.

---

Built for use with [Claude Code](https://claude.com/claude-code)

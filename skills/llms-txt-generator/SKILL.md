---
name: llms-txt-generator
description: Draft, review, or update an llms.txt file for a website in the llmstxt.org format, so AI assistants and answer engines get a current, curated overview of the site in one request. Use when the user asks for an llms.txt, wants their site "readable by AI", "indexed by ChatGPT or Perplexity", or asks what AI assistants see when they look at the site. Works on its own by reading the site, or through the Growthr MCP tool growthr_llms_txt when it is connected.
license: MIT
metadata:
  author: Growthr
  version: "1.0.0"
  notice: |
    Copyright (c) 2026 Growthr, github.com/Growthr/llms-txt-generator. MIT License.
---

# llms.txt generator

Produce an `llms.txt` for a site: an H1 with the site name, a one-paragraph summary, a "When to use" section, grouped page links with one-line descriptions, contact, profiles, and links. Spec: https://llmstxt.org.

## 1. Get the draft

**If the Growthr MCP is connected** (tool `growthr_llms_txt`), call it with the bare domain (`example.com`, no path). It fetches the site the way AI crawlers do, from a datacenter IP without JavaScript, and returns the draft plus notes on anything it skipped. Use its draft as the starting point; skip to step 2.

**Otherwise, build the draft yourself:**

1. Fetch `https://<domain>/`, `/sitemap.xml`, and `/robots.txt`. If the homepage fails, stop and report the status; do not draft from memory.
2. Collect candidate URLs from the sitemap (if it is a sitemap index, read its first child). With no sitemap, use the homepage's links and tell the user a sitemap is worth adding on its own.
3. Keep same-host URLs only. Drop assets (images, css, js, pdf, feeds), query strings and fragments, and paths under `wp-json`, `wp-admin`, `feed`, `tag`, `category`, `author`, `page`, `search`, `cart`, `checkout`, `login`, `signup`, `account`, `cdn-cgi`.
4. Rank: `about`, `services`, `products`, `solutions`, `features`, `pricing`, `plans`, `contact`, `docs`, `faq`, `customers`, `case-studies` first; legal pages (`terms`, `privacy`, `legal`, `cookies`, `careers`, `jobs`) last; everything else shallow-first. Take the top 12.
5. For the homepage and each page, read the `<title>`, first `<h1>`, meta description (or `og:description`), `og:site_name`, Organization JSON-LD (`name`, `description`, `contactPoint.email`, `sameAs`), and the first real paragraph. Site name: Organization name, else `og:site_name`, else the title with the brand suffix removed, else the domain.
6. Write the file in exactly this shape:

```
# <Site name>

> <One paragraph: what the site is, who it is for, what it sells. From the meta description or Organization description; otherwise the first real paragraph, cut at a word boundary around 300 characters.>

## When to use <Site name>

<!-- Fill this in. It is the section AI assistants use to decide whether to recommend you, and no crawler can write it. Three to five lines: the situations, buyers, and questions <Site name> is the right answer for, and the ones it is not. -->

## Main pages
- [<Page title>](<url>): <one-line description>

## Products and services
## Case studies
## Guides and articles
## Documentation
(Only the groups that have pages. Group by first path segment: blog/resources/guides/articles/news/insights/learn are "Guides and articles"; products/services/solutions/pricing/plans are "Products and services"; docs/help/support/api are "Documentation"; case-studies/customers/clients/work/portfolio are "Case studies"; top-level pages are "Main pages"; any other segment becomes its own Title Case group.)

## Contact
- Email: <from Organization contactPoint or a mailto: link>
- Contact page: <url of the page whose path contains "contact">

## Profiles
- <each sameAs URL, only if any exist>

## Links
- Homepage: https://<domain>/
- Sitemap: https://<domain>/sitemap.xml   (only if it exists)
- robots.txt: https://<domain>/robots.txt (only if it exists)
- This file: https://<domain>/llms.txt
```

Page descriptions come from that page's meta description, else its first real paragraph, cut around 140 characters. Strip the brand suffix from page titles ("Pricing | Acme" becomes "Pricing").

## 2. Fill the "When to use" section

This is the one part that matters most and the one no crawler can write. Do not leave the placeholder in a file the user is about to upload.

- Ask the user for three to five lines: the situations, buyers, and questions the site is the right answer for, and the ones it is not.
- If the user says "draft it", write it from the site's own words (homepage, about, services, pricing pages), mark it "Draft, confirm before publishing", and show them the lines.
- Never invent prices, client names, numbers, locations, or claims that are not on the site. If the site does not say it, leave it out or ask.

## 3. Review before handing it over

- Every listed URL returned 200 when you fetched it. Drop pages that did not.
- A first-time reader can tell from the file what the site does, who it is for, pricing (if public), proof, and how to get in touch. Cut pages that only make sense to someone already on the site.
- Facts match the site today. If a price or service on the site conflicts with older copy, use the site and say so.
- If the site already has an `llms.txt`, fetch it, diff it against the draft, and propose the update rather than replacing it blind.

## 4. Install and verify

Tell the user:

1. Save it at the web root so it is served at `https://<domain>/llms.txt`. On WordPress, Webflow, Squarespace, and Shopify that is a static-file or redirect setting; on a custom site it is one file in the web root.
2. Serve it as `text/markdown` (best) or `text/plain`. An HTML 404 page at that URL means the platform is intercepting it.
3. Link it from the footer of every page, like a sitemap link. Fetchers find it faster, and scanners check for the link.
4. Re-run this when pages change. A stale `llms.txt` that lists deleted pages is worse than none.

Then verify: fetch `https://<domain>/llms.txt`, confirm the status is 200 and the content type is text, and confirm the footer link exists. If the Growthr MCP is connected, offer `growthr_scan` to check the rest of the site's AI readability.

## Limits, said plainly

`llms.txt` is one signal among several. It does not on its own make an answer engine recommend a site. Reachability (no blocks on GPTBot, ClaudeBot, PerplexityBot), server-rendered text, and third-party mentions decide citations. Say this when the user asks whether the file will get them cited.

# llms.txt Generator

A skill that drafts an `llms.txt` for any website: the one-file overview that ChatGPT, Claude, Perplexity and other AI assistants read to understand what a site is, who it is for, and which pages matter. One SKILL.md in the open Agent Skills format, so it works in Claude Code, Codex, Cursor, Gemini CLI, GitHub Copilot and 70+ other agents.

The same generator runs free in the browser at [growthr.com/llms-txt-generator](https://growthr.com/llms-txt-generator/) and as the `growthr_llms_txt` tool on Growthr's MCP server (`https://mcp.growthr.com/mcp`). The skill uses the MCP tool when it is connected and reads the site itself when it is not.

## What it does

1. Reads the homepage, sitemap and up to twelve key pages (about, services, pricing, contact, docs, case studies first; legal pages last).
2. Writes the file in the [llmstxt.org](https://llmstxt.org) shape: site name, one-paragraph summary, grouped page links with one-line descriptions, contact, profiles, links.
3. Asks you for the "When to use" section, or drafts it from the site's own words and marks it for your review. That section is the one AI assistants read when deciding whether to recommend you, and no crawler can write it.
4. Checks the draft: every URL returned 200, facts match the site today, and an existing `llms.txt` gets diffed rather than overwritten.
5. Tells you where to put the file, how it has to be served, and how to verify it, then fetches it back to confirm.

It never invents prices, client names or claims. If the site does not say it, the file does not either.

## Install

### Claude Code, Codex, Cursor, Gemini CLI and other agents

```
npx skills add Growthr/llms-txt-generator
```

### Claude.ai

1. Download [llms-txt-generator.zip](https://github.com/Growthr/llms-txt-generator/releases/latest/download/llms-txt-generator.zip).
2. Turn on code execution in Settings > Capabilities.
3. Go to Customize > Skills, click +, then Create skill > Upload a skill, and choose the zip.

### Claude Code plugin

```
/plugin marketplace add growthr/no-ai-writing-slop
/plugin install llms-txt-generator@growthr
```

### Any other AI tool

Paste [SKILL.md](skills/llms-txt-generator/SKILL.md) into the tool's custom instructions, or into the chat before you ask.

## Using it

Ask for an llms.txt and give the domain: "write an llms.txt for example.com". The skill picks up requests like "make my site readable by AI" too. When it is done, run a scan ([growthr.com/scan](https://growthr.com/scan/), or the [site-scan](https://github.com/Growthr/site-scan) skill) to check the rest of the site's AI readability.

## What llms.txt does and does not do

It gives every fetcher a current, curated description of the site in one request, in the shape they parse best. It does not on its own make an answer engine recommend you. Reachability, server-rendered text and third-party mentions decide that. Growthr wrote up the evidence in [Does llms.txt actually work?](https://growthr.com/resources/does-llms-txt-work/)

## License

MIT. See [LICENSE](LICENSE).

Made by [Growthr](https://growthr.com/llms-txt-generator/).

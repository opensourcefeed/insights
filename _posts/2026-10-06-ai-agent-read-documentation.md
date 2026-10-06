---
layout: post
title: "Can an AI agent read your project documentation?"
categories: [open-source, tools, tutorial]
tags: [documentation, ai-agents, robots-txt, llms-txt, seo, markdown, developer-docs]
description: "Check whether an AI agent can read your project documentation: audit HTML responses, robots.txt rules, Markdown copies and llms.txt."
---

**A** documentation page can look complete in a browser while giving an automated reader little useful text. The installation command may appear only after JavaScript runs. A firewall may return a challenge instead of the page. A link to an older release may send an agent to instructions for the wrong version.

Start with a public page that answers a common support question, then inspect what a reader can retrieve without a signed-in browser session.

## Check the response before the layout

Choose an installation guide or API reference. Read the HTML response and look for the page title, the main explanation and a complete example. A successful HTTP status alone is not enough: a page can return 200 while showing a loading screen or a bot challenge.

If the useful content arrives only after a browser executes scripts, check the tools your readers use. Some agents can operate a browser; a basic HTTP fetcher cannot execute the same workflow. Rendering the main documentation into the initial HTML gives both kinds of reader a usable starting point.

For an initial audit, [Good for Bots](https://goodforbots.com/) provides a free website scan, a score from 0 to 100 and a public report of the checks behind it. Use the individual findings to choose what to inspect next. A scan samples pages and reports what its own crawler observed; it cannot establish how every AI service will handle every URL.

## Separate crawler policy from access failures

Read the rules that apply to the crawler in robots.txt. The Robots Exclusion Protocol, specified in RFC 9309, groups path rules under user-agent names. A rule intended for one crawler may differ from the fallback rule for other crawlers.

Keep deliberate restrictions. If you want a particular service to read public documentation, check both its applicable robots rules and the response it receives from your hosting or CDN. An allowed path can still be blocked by a firewall. The robots.txt file is not an authentication system and cannot protect private credentials or customer data.

## Preserve the details a developer needs

Read one procedure as plain text. Can you still tell which operating system it applies to, which command comes first and whether an option is required? Make version requirements explicit near the example. Put warnings beside the step they affect.

Use headings to separate procedures, normal links for related pages and text for information that would otherwise appear only in screenshots. With tabbed examples, verify that the extracted content identifies each language or platform. Without platform labels, an agent may pick a command intended for a different system.

## Keep a Markdown copy in sync

A Markdown version can make documentation easier to consume with text-based tools. Generate it from the same content as the HTML page where possible, and check that code, links and warnings survive the conversion.

The llms.txt proposal offers a place for brief guidance and links to useful documents. Treat it as another route into maintained content. A short index pointing to current installation and API pages is easier to check than a second, manually copied documentation tree.

## Measure the result you care about

After a change, repeat the request that failed and compare the returned content. Keep the URL, date and response details so the next deployment can be checked against the same example.

Track retrieval, citations and visits separately. Fixing an empty response makes content available to a reader, but does not prove it will be selected as a source. Google's guidance for AI Overviews and AI Mode says there are no extra technical requirements beyond its existing search requirements, and that eligibility does not guarantee indexing or display. For a maintainer, the immediate test is concrete: can a reader retrieve the correct instructions and use them without losing a step?
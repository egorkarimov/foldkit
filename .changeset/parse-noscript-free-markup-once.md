---
'foldkit': patch
---

Server rendering parses the rendered markup fewer times. `renderToString` and `injectIntoTemplate` check every page by parsing it back with parse5, and on a large page those parses took most of each request. A request that renders a page without `<noscript>` and injects it into a template now parses the whole page 6 times, which roughly halves the time those two calls take.

The check that the rendered root closes cleanly now shares its parse with the other checks on the root, unless the markup contains the text of the comment that check appends. Every check also skips its scripting-disabled parse when the markup has no `<noscript>` start tag, because the scripting flag changes how the parser reads only that tag, so both modes produce the same tree. Markup that contains the text `<noscript` anywhere, including inside an attribute value or a comment, is parsed in both modes as before.

Every check and every error message is unchanged, and the rendered and injected HTML is the same byte for byte.

```css
img[alt] {}
a[href="https://cs.ucla.edu"] {}
```
- `a[href]` a containing href, `a[href="https://google.com"]` (exact match)
	- `~=` whitespace delimited token, `*=` contains, `^=` starts, `$=` ends, `|=` exact same or starts with + hyphen
- `>` direct children, `+` sibling immediately after, `~` sibling and appears after at some point
- `:` = pseudoclass = state of element (hover, visited, link aka not yet visited, nth-child)
- `::` = pseudoelement (first-letter)
- `<link rel="stylesheet" href="style.css" title="Default">`
	- Put in head, can import CSS stylesheets with `@import(path)` at TOP of stylesheet
- Precedence: Origin (author, then user style settings in browser, then user agent style browser default), how defined (inline, embedded with style tag, external), specificity, whichever one comes last
- Specificity: (# IDs, # Classes or pseucoclass or attribute selector without or without equality, # Tags or pseudo-element), convert to number 100*, 10*, 1*, highest weight wins, then last declared
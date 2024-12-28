- Custom properties:
	- Declare at highest part b/c inherited
	- `--key: white`l, then use `var(--key, def)` in place of color, `def` if custom property not defined
- Box model
	- `border: 5px solid red;` (width, style, color)
	- 1 = all, 2 = top/bottom, left/right, 3 = top right/left bottom, 4 = top right bottom left
	- inline: top/bottom margin and padding does not work, use `line-height` instead
	- Margin collapsing: multiple side-by-side, max of margins is used, not added
	- default margins: p (1em), h1 (21pm), body (8px), div (0)
	- Negative margins to overlap or counteract another margin
	- auto margin: center horizontally, not vertically
- Overflow
	- visible (spill out), hidden (clipped), scroll (always show scrollbar), auto (only show if it fits), clip (hidden but no programmatic scrolling)
	- `overflow-x` `overflow-y`
	- overflow-wrap: normal (break on spaces, then overflow), anywhere, break-word (breaks long word onto new line)
- 1px = 0.75pt, 1pt = 1/72nd inch
- Display
	- block: newline before and after, takes up entire horizonal space
	- inline: same line as current block, only necesssary space
	- none: hide, take no space. visibility:hidden to maintain space
	- inline-block: inline but can have height/width and top/bottom margins
- Position:
	- static: default
	- relative: relative to the parent, using `top,left,right,bottom` to shift relative
	- absolute: remove from normal flow (no space maintained), positioned relative to closest positioned ancestor (if any) or body
	- fixed: fixed to a part relative to screen
- z-index: vertical location, higher z-index = appears on top
### CSS Grid
- grid-template-rows/grid-template-columns define size of each row/col
	- `grid-template-rows: 1px 2px;` 2 rows, height is 1px second is 2px
	- `grid-column-start: span 2;` span 2 columns, apply this to cell element
### Flexbox
- `flex-direction`
- `justify-content`: justify content with box. `flex-start, flex-end, center, space-between, space-around`
- `align-items`: align cross axis `flex-start, flex-end, center, stretch, baseline` (baseline lines up bottom of text)
# Responsive Web Design
- Fixed vs fluid layout: elements use "percentage" of width of page
- Rules: no horizontal scroll, no fixed-width elements, no zooming in/out to read text
- `<meta name="viewport" content="width=device-width, initial-scale=1">`: forces width to actually be device width, tells mobile browser site is ready for tiny screen
- Use pointer events, not mouse events, for touchscreens
```css
@media screen and (max-width: 800px) {
	/* css rules */
}
```
- Media types: screen, print (when printing), all
- Media features: orientation, min-width, max-width, min-height, max-height, resolution, device-aspect-ratio, color (bitrate), scan (specific to TV monitors)
- Boolean and, or, not
# CSS in prod
- Other tools like Sass and Tailwind to simplify
### SASS
- Syntactically Awesome Style Sheets
- Extension to CSS
- CSS **pre-processor**
- Allows features that doesn't exist in CSS, like variables, nested rules,
mixins, imports, inheritance*, built in functions, and other stuff.
```scss
$primary_1: #a2b9bc;
/* use the variables */
.main-header { background-color: $primary_1; }
nav {
	ul {}
}
/* same as */
nav ul {}

/* nested properties */
p {
	text: {
		align: center;
		transform: lowercase;
		overflow: hidden;
	}
}

/* mixins */
@mixin test($color) {
	test: property;
	border; 10px solid $color;
}
.danger {
	@include test(blue);
}
```
### Tailwind
- Provides single-purpose utility classes
	- Specify classes in HTML and CSS generated from it
```html
<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
Click me!
</button>
```
- Generates with appropriate css
### UI Frameworks
1 Bootstrap: Can be used with Sass. Provides a lot of styles to
make generating UI components and experiences easier.
2 Materialize based on Google Material UI.
3 MaterialUI based on Google Material UI, but for React only.
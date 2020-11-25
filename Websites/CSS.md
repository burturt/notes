CSS

# CSS Notes:
- Cascading Style Sheets!
  - CSS basically allows you to mass add attributes to html tags
- CSS format:
```css
selector {
   property: value;
   property: value;
}
```
# Selectors:

## 3 basic selectors:

1. type selectors: used to select HTML elements by element name. This is like your h1, p, a, etc. Your selector will simply be the name of the tag.
2. class selectors: used to select HTML elements by a specific class value. classes can be added to html tags by using the class attribute. html tags may have as many classes as you want. Your selector will be a `.` followed by the class name, like `.blueBackground`
3. id selectors: used to select an HTML element associated with a specific id value. IDs can be added to html tags by using the id attribute. You may only use an id once, and you can only assign one id to each tag! Your selector will be a `#` followed by the id name, like `#blueButton`
Type selectors will be applied first, then class, then id. Within each group, the *most specific* group gets applied. If you have the following css:
```css
body {
    background: red;
}
.myText {
    background: blue;
}
h1 {
    background: green;
}
```
a `p` tag with myText class will be colored blue, a `p` tag within the body but not a `.myText` class will color the background green as it is more specific, and everywhere else on the page will be red as neither the `.myText` class nor the `h1` or `body` tag applies.

#### Selecting multiple elements, separate by commas:
```css
/* Selecting multiple HTML element types */
h1, p {
  border: 1px solid black;
}

/* Selecting styles to be applied to several classes */
.ingredientsList, div.instructionsList {
  font-size: 1.2em;
}

/* Using multiple kinds of selectors*/
h3, .red, #redElement{
  color: red;
}
```

#### Nested elements:

```css
/* select only h1 elements within div's */
div > h1 {
	border-bottom: 1px solid black;
}

/* select only p elements within div's class red */
div.red {
	color: red;
}
```

Properties:

- `background` - background color like `red`
- `color` - color of text like `white`
- `font-family` - font of text, like `Arial`
- `text-align` - align left, right, center.
- `border: width type color;`
- `font-size: 2em;`


# The box model
- Every HTML element is just a box!
- Margin --> border --> padding --> content
	- Content innermost, this is what width and height controls
- Relative vs Absolute:
	- Percentages are relative, and take a percent of the area of the element one above
	- \_px are absolute and are a fixed pixel width

#### Padding
- `padding`: create spacing between content area and border, think "expand box outward"
```css
/* A single value will apply the same 
padding on all sides of the box */
#padding1 {
  padding: 10px;
}

/* Two values will apply as: 
		first value - vertical sides
		second value - horizontal sides */
#padding2 {
  padding: 5px 20px;
}

/* Three values will apply as:  
		first value: top side
		second value: horizontal sides
		third value: bottom side */
#padding3 {
  padding: 10px 5px 30px;
} 

/* Four values will apply as: 
		first value: top
		second value: right
		third value: bottom
		fourth value: left
*/
#padding4 {
  padding: 10px 5px 60px 35px;
}
```

	- Relative lengths are relative to the width of the parent element
	- Apply to a single side using `padding-bottom/left/right/top`, accepts single value

#### Borders
- `border: width style color;`
	- Styles: dotted, groove, double, solid, etc
	- `border-style/color/width` expansion, also `border-top-width`, `border-left-color`, etc
- `border-radius: size;` Round off corners of border
	- `border-radius: size / size;` make elliptical corners
#### Margins
- `margin` very similar to padding
	- Area is outside border, background does not cover margin area

#### Properties:
- `width`
- `height`
- `padding`
- `border`
- `margin`

# Color

- `rgb`: use red, green, and blue parameters to define a color
	- Use by replacing color with `rgb(255,255,255)` where 255 is the red/green/blue amount
	- `rgba` very similar except they have alpha (transparency)
	- `rgba(255,255,255,1)` a value is 0 to 1, 1 being opaque
	- Colors use hex values
- Alternate: `hsl` and `hsla`: hue, saturation, and lightness
	- Hue: Angle between 0 and 360
	- Saturation and lightness: percentage 0-100%

# Font Styling 
- Change font of text using `font-family` property
	- Wrap names with spaces in quotes, `"Times new Roman"`
### Web safe fonts:
- Not every system has all fonts. web-safe fonts are on every platform
- Common web-safe fonts:
	- Arial
	- Times/Times New Roman
	- Helvetica
	- Courier New/Courier (monospace)
	- Verdana
	- Georgia
	- Sans-Serif
### Font Stacking:
- Apply fonts in an order until one is available
- `font-family: "Helvetica Neue", Helvetica, sans-serif;`
- Generic-family font: a family of fonts that contains multiple font types. Always end font-family stacks with a fallback of a generic-family font
### Loading additional fonts
- Google fonts: https://fonts.google.com/
- Add `<link href="https://fonts.googleapis.com/css?family=Open+Sans:400,700" rel="stylesheet">` to your head
- *Note: more fonts = slower page!*

# Text manipulation
## Alignment:
- Use `text-align` property. 4 options:
	- `center`
	- `left`
	- `right`
	- `justify` - spread text to fill full width of container
## Sizing:
- `font-size` property. Absolute value: `px`, relative values: `em` or `rem`
	- `em`: equivalent of one font-size of element's parent
	- `rem`: equivalent of one font-size of the **root element** of the entire html document
		- Easier to use, `em` can get complicated to find reference point
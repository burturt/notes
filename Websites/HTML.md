HTML

# HTML Notes:
- HTML is a **markup** language. It simply states how something is rendered.
- HTML is made up of tags. There are 2 types of tags:
1. The basic tag: `<tagname> content </tagname>`. For example, a p tag with content "Hello, world!" would be `<p> Hello, world! </p>`
2. The "self-closing" tag: `<selfclosing/> content`. An example would be the br tag, the line break like `<br>`. **There is no closing tag for self-closing tags, trying to close it is an error!**
- Tags can and should be nested! Put header tags inside of url ones, make headers strong, do whatever you want!
New tags:
```html
   <h1>Heading Level 1</h1>
   <h2>Heading Level 2</h2>
   <h3>Heading Level 3</h3>
   <h4>Heading Level 4</h4>
   <h5>Heading Level 5</h5>
   <h6>Heading Level 6</h6>
```
Heading level 1 is the largest while level 6 is the smallest.
```html
<strong> This is strong text (default bold) </strong>
<em> This is emphasized (default italics) </em>
<u> This is underlined </u>
```

# Attributes!
- You can add attributes to tags. You add attributes as follows:
`<tagname attribute=value attribute2=value2> content </tagname`
- One common one is using the href attribute using the a tag to make the content in the a block hyperlinked to a url. 
`<a href="https://example.com"> click here to go to https://example.com </a>`
- img tag: this is self closing! add the source attribute to the link of the image. `<img src="https://example.com/logo.png">`
--- img also has width and height tags. `<img src="https://example.com/logo.png" height=500 width=100>`. If you only specify one, the image will scale, keeping the aspect ratio.

## Default structure:
```html
<!DOCTYPE html>
<html> 
  <head>
  <!-- Stuff that you do not see (web page title, style, scripts, etc) -->
  </head>

  <body>
  <!-- Stuff that you do see (h1, p, img, etc) -->
  </body>
</html>
```

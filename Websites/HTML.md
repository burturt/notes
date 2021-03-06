HTML

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
<a href="https://google.com">This is a url link</a>
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

## Other Tags:
### Lists
```html
<!-- Unordered list -->
<ul>
  <li>Phone</li>
  <li>Tablet</li>
  <li>Computer</li>
  <li>Monitor</li>
</ul>

<!-- Ordered list -->
<ol>
	<li>Go to school</li>
	<li>Go to college</li>
	<li>Get a job</li>
	<li>???</li>
	<li>Profit</li>
</ol>
```
#### Attributes:
- `type=TYPE`
	- Unordered lists:
		- circle
		- disc
		- square
	- Ordered lists:
		- `1` Numerals
		- `I` Upper-case Roman numerals
		- `i` Lower-case roman numerals
		- `A` Upper-case alphabet
		- `a` Lower-case alphabet
- `start=NUMBER`
	- Ordered lists: change the starting number/letter

### Inline, Block, and Divs
- Block-level elements take up the full width of the page, creating a "block" in the page
	- `<h1>`, `<ol>`, `<p>`, etc
- Inline elements do not take up the full width of the page, being "in line" with text
	- `<a>`, `<img>`, `<strong>`, etc
- `<div>` tag allows splitting up html content into different logical sections. By themselves nothing changes but they can become very useful as you get into css and javascript

### Tables
```html
<table>
    <tr>
      <th>Column 1 header</th>
      <th>Column 2 header</th>
      <th>Column 3 header</th>
    </tr>
	<tr>
      <td>Column 1 row 1</td>
      <td>Column 2 row 1</td>
      <td>Column 3 row 1</td>
    </tr>
	<tr>
      <td>Column 1 row 2</td>
      <td>Column 2 row 2</td>
      <td>Column 3 row 2</td>
    </tr>
</table>
```

### Forms
- Note: while we create the form here, actually processing the data or making the form actually work comes later
```html
<form>
	
	<!-- One line text inputs -->
	
	<label for="onelinetext">
		One Line of Text Input:
		<input type="text" name="text field" id="onelinetext">
	</label>
	
	<label for="passwordtest">
		Hidden text when typing:
		<input type="password" name="text field" id="passwordtest">
	</label>
	
	<!-- Types of input type values: text, password, search, url, tel, email. url, and email validate input on the browser. -->
	
	<!-- Multi-line text input/output --> <br>
		
	<label for="textarea">
		Big text area:
		<textarea id="textarea" rows="5" cols="50">
			<!-- Add text here for it show up in text area -->
		</textarea>
	</label>
	
	<!-- Buttons! --> <br>
	
	<button type="submit">Submit form</button>
	<button type="reset">Reset form</button>
	<button type="button">Blank button (for use with javascript later)</button>
	
	<!-- Dropdowns --> <br>
	
	<label for="dropdown">
		Choose one:
		<select id="dropdown">
			<option>Option 1</option>
			<option selected>Option 2</option> <!-- selected means this one will be selected by default instead of the first one. -->
			<option>Option 3</option>
		</select>
	</label>
	
	<!-- You can also create groups in dropdowns: -->
     <label for="dropdowngroups">
		 Choose one from a group:
       <select id="dropdowngroups">
         <optgroup label="Group 1">
           <option>Option 1</option>
           <option>Option 2</option>
         </optgroup>
         <optgroup label="Group 2">
           <option>Option 3</option>
           <option>Option 4</option>
         </optgroup>
         <optgroup label="Group 3">
           <option>Option 5</option>
           <option>Option 6</option>
         </optgroup>
       </select>
     </label>
	
	<!-- Radio buttons / check boxes with input. Group radio options together by giving the same name element --> <br>
	
	<label for="o1">
		Option 1
		<input type="radio" id="o1" name="radio">
	</label>
	<label for="o2">
		Option 2
		<input type="radio" id="o2" name="radio">
	</label>
	<label for="o3">
		Option 3
		<input type="radio" id="o3" name="radio">
	</label>
	
	<!-- Use type="checkbox" instead for checkboxes: -->
	
		<label for="c1">
		Option 1
		<input type="radio" id="c1" name="radio">
	</label>
	<label for="c2">
		Option 2
		<input type="radio" id="c2" name="radio">
	</label>
	<label for="c3">
		Option 3
		<input type="radio" id="c3" name="radio">
	</label>
	
</form>
```
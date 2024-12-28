- Confidentiality
- Message/data integrity
- Authentication
- Authorization
![Screenshot 2024-06-08 at 2.34.00 PM.png](../../_resources/Screenshot%202024-06-08%20at%202.34.00%20PM.png)
- Certificate: contains Distinguished Name of entity that owns public key, entity that issued, time when valid, public key itself
- Famous SQL injections
	- Russian operatives steal voter info in Illinois
	- 2021: 70GB stolen from Gab, introduced by CTO
	- 2015: TalkTalk personal data stolen
	- 2012: 0.5 mil login creds stolen from Yahoo!
	- 2005: Teen broke into Taiwanese IS mag to steal info
## Accessibility
- Make website usable by as many as possible:
	- include those with no obvious barriers, physical needs, mental needs (neurodiversity), modile devices, slow network connections/rural
	- Required laws: US ADA, Canada Accessile Canadat Act, EU EU web Accessibilty Directive, Australia Disability Discrimination Act, UK Equality Act
- Do your best, but make accessibility statement to explain attempts at making site accessible
- Can evaluate using Wave: 
### W3C Accesibility Standards
- WCAG: Perceivable, Operable, Understandable, Robust
- Use correct semantic elements, source order should make sense w/o css, interactive should have focus, hover, and active states
	- visibility hidden/display none will hide from screen reader as well
- WAI-ARIA Overview
	- Provides additional HTML attributes for complex form controls and panels
### Visual Impairment
- Zoom in
- screen readers
	- Forms: all form elements and their types should be announced.
		- Form labels should be clear and be announced.
	- The states, properties, values and changes to them must be announced.
	- Tables: they must have names and header cells and should be simple with no empty rows, columns or cells.
	- Accordions/Expandos: each panel should be able to be opened and its content accessed by the screen reader.
	- Carousels/Sluide shows: auto advancing can be problematic
	- Image: always include alt
	- alerts, errors, pop-ups, fire in way screen reader can alert user
- Use high contract colors
	- use colors that are compatible, e.g. blue + brown, not green + red
	- Avoid conveying tone only with style and color
### Hearing
- Closed captioning, simplified language
	- Best manually
	- `HTML5 track element inside <video>`
### Mobility
- Develop keyboard input usability
### Cognitive or Neurological Impairment
- Deliver multiple ways
- Easily understood content (simple text, active voice in present indicative tense, proper spelling and grammar)
	- Provide low reading level TL;DR 
- Focus attention on important content
	- Avoid distraction/overstimulation/unnessesary content
- Consist web layout, navigation, styles
- Divde long processes into logical, essential steps w/ progress indicators
- Easy web authentication
- Simple error messages
- Define unusual words (e.g non-native english and sarcasm)
- `<abbr>` for abbreviation or ruby text
### Slow Internet Connections
- Lots of caching
- Low bandwidth version (e.g. limited images and videos)
- Multiple versions of images and videos for lower quality/more compressed
- Reudce animations/replace with text-only
# Checklist
1 Make sure your HTML is as semantically correct as possible. Validating it is a good start, as is using an Auditing tool.
2 Check that your content makes sense when the CSS is turned off.
3 Make sure your functionality is keyboard accessible. Test using Tab, Return/Enter, etc.
4 Make sure your non-text content has text alternatives. An Auditing tool is good for catching such problems.
5 Make sure your site’s color contrast is acceptable, using a suitable checking tool.
6 Make sure hidden content is visible by screen readers.
7 Make sure that functionality is usable without JavaScript wherever possible.
8 Use ARIA to improve accessibility where appropriate.
9 Run your site through an Auditing tool.
10 Test it with a screen reader.
11 Include an accessibility policy/statement somewhere findable on your site to say what you did.
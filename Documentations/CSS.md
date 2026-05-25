# Chapter One:

**we can put the styles inside html style tags like this:**
``` html
<style>
	p {
		color: purple;
	}
</style>
```
**we can also apply styles using links:**
``` html
<link rel="stylesheet" href="css/style.css">
```
**or apply it directly with inline CSS:**
``` html
<body>
	<p style="color: blue">I am learning CSS!</p>
</body>
```

![[Selector Stucture.png]]
# Chapter Two, CSS Selectors:

**Element Selector:** Targets all instances of a specific HTML tag across a page. When you define properties within this selector, the browser applies those styles to every element that matches that tag type automatically.
``` css
body {
	font-size; 22px;
}
p {
	color: purple;
}
h1 {
	color: blue;
}
```

**Class Selector:** Targets specific elements that have been assigned a `class` attribute. This allows you to apply the same set of styles to multiple, unrelated HTML elements regardless of their tag type, providing a flexible way to group styles.

The CSS:
``` css
.highlight-text {
  font-weight: bold;
  background-color: yellow;
}
```
The HTML:
``` html
<p class="highlight-text">This paragraph is highlighted.</p>
<li class="highlight-text">This list item is also highlighted!</li>
```

**ID Selector:** Targets a single, unique element with a specific `id` attribute. Because IDs must be unique within an HTML document, this selector is used for individual elements that require distinct styling, such as a navigation bar or a specific layout container.

The CSS:
``` css
#main-header {
  background-color: #333;
  padding: 20px;
}
```
The HTML:
``` html
<header id="main-header">
  <h1>Welcome to My Site</h1>
</header>
```

**Grouping Selector:** Used to select multiple HTML elements simultaneously by separating selectors with a **comma**. This minimizes code redundancy 
(the "DRY" principle: Don't Repeat Yourself) and makes stylesheets much easier to maintain.
``` css
h1, h2, p {
  text-align: center;
  color: purple;
}
```

**Descendant Selector:** Targets an element that is nested anywhere inside another specified element. It doesn't matter how deep the nesting goes; as long as the second element is "wrapped" by the first, the style applies.
``` css
/* Only targets <span> tags that are inside a <div> */
div span {
  color: red;
  font-weight: bold;
}
```

**Universal Selector:** Matches every element in the document, including `<html>`, `<body>`, and every nested tag like `<div>`, `p`, and `section`. It is most commonly used for "CSS Resets" to clear out the default margins and padding that browsers add automatically.
``` css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

**The CSS "Power Ranking" (Lowest to Highest):**
1. **Universal Selector (`*`):** Has **zero** priority. It’s easily overridden by anything else.
2. **Element Selector (`p`, `h1`):** Worth **1 point**. It’s the baseline.
3. **Class Selector (`.my-class`):** Worth **10 points**. It beats any number of element selectors.
4. ID Selector (`#my-id`):** Worth **100 points**. This is the "heavy hitter."
5. **Inline Styles (`style="..."`):** Worth **1,000 points**. These are written directly in the HTML tag and beat almost everything in the CSS file.
6. **The Nuclear Option (`!important`):** This isn't a selector, but a tag you add to a value. It overrides **everything** else, even inline styles.

**The main rule: CSS Always follows what is more specific!**

Lower priority styles automatically gets ignored by browsers:
![[Screenshot 2026-02-05 162945.png]]

**Inheritance:**
The `font: inherit;` rule tells the browser: **"Don't use your default button/input font. Instead, use the exact same font as the parent element."**
``` css
button, input, textarea {
	font: inherit;
}
```

**!important:** In the world of CSS, `!important` is the **ultimate override**. It is a tag you add to the end of a property value to tell the browser: _"Ignore all other rules and specificity scores; this is the one that counts."_
Think of it as the **"I said what I said"** button for code.

**How it looks in practice:**
Even if you have a highly specific ID selector, the `!important` tag on a low-level element selector will still win.
``` css
/* This SHOULD win because it's an ID (100 points) */
#main-button {
    color: blue;
}

/* This WINS because of !important */
button {
    color: red !important;
}
```

# Chapter Three, CSS Colors:

For setting the text color we use the **color** property:
``` css
body {
	color: darkblue;
}
```
For setting the background color we use the **background** property:
``` css
body {
	background: papayawhip;
}
```
Using the **rgb** property:
``` css
body {
	color: rgb(0, 0, 0);
	background: papayawhip;
}
```
Using **Rgba**, the last a is for the alpha channel, which works for changing the transparency:
``` css
body {
	color: rgba(0, 0, 0, 0.5)
}
```
Using hexadecimal:
``` css
body {
	color: #FF0000; /* red color */
	color: #00FF00; /* green color */
	color: #0000FF; /* blue color */
}
```

Each two corresponding pair represent of the three channels of the RGB
**NOTE:** When a pairs are matched, we can remove the pairs and replace them with single letters: `#FFFFFF` is the same like `#FFF` and `#000000` is the same like `#000`.

For good color choices we can use **palettes generator**: https://coolors.co/

# Chapter Four, Units and Sizes:

### **pixels:**
``` css
p {
	font-size: 32px;
}
h1 {
	border: 2px dashed red;
}
```

### **percentages:**
When you set `width: 50%`, the element will take up half the width of its immediate parent container.

- **Width:** Relative to the parent’s **width**.
- **Height:** Relative to the parent’s **height**.

**The "Height" Catch:** For `height: 50%` to work, the parent must have an explicit height (like `height: 500px` or `height: 100vh`). If the parent's height is "auto" (meaning it grows with its content), the percentage height on the child will often default to 0 or its content height because 50% of "auto" is undefined.

``` css
header {
	width: 50%;
}
h1 {
	border: 2px dashed red;
	width: 50%;
}
```

Now if we have a header tag and an h1 tag inside it, we will have a 25% of the screen size for the h1, that's because the header is taking only 50% of the screen size and the h1 tag is nested inside the header tag, h1 tag will take 50% of the 50% which results in 25%.

### **Rem:**
**1rem** = 16px (default)
**2rem** = 32px
**0.5rem** = 8px
`If you change the font size of the `html` tag, every `rem` value on your site updates automatically.`

Rem vs. Em: The Compounding Problem
The biggest advantage of `rem` is that it avoids "nesting madness." Look at how they behave differently when nested:

### **Why Use Rem Instead of Pixels?**
Using `rem` is considered a "best practice" for two main reasons:
- **Accessibility:** If a user has vision impairment and changes their browser settings to "Large Text," your entire layout (padding, margins, and fonts) will scale up proportionally. Pixels (`px`) are "hard-coded" and will often ignore the user's preference.
- **Scalability:** You can change the scale of your entire website for different screen sizes just by changing one line of CSS in a media query:

``` css
p {
	font-size: 2rem;
}
```

### **When to use Em ?**
Use **`em`** for properties that should scale proportionally with the element's own font size, such as **padding, margin, or border-radius**.

For example, if you define an `h1`'s font size in `rem` (for global consistency), but set its padding in `em`, the spacing will stay visually balanced even if you change the heading's size later.

``` css
h1 {
	border: 2rem dashed red;
	width: 50%;
	font-size: 3rem;
	padding: 0.5em;
}
```

Now, the size of the padding with always be half the size of the text of the h1 tag due to the declaration: `padding: 0.5em`, even if the size of the whole page got larger; and the text `3rem` size gets larger, the padding size will keep at half the size of the text.

### **ch sizing:**
ch means character, it's a measuring unit according to the space occupied by characters in the page, for example if we need a p tag with max line length of 40 characters we can do as following:
``` css
p {
	font-size: 3rem;
	padding: 0.5em;
	width: 40ch;
}
```
now the width of the padding `the line length` can hold only 40 chars.

### **Viewport:**
While **percentages** are relative to the parent container, **`vh`** and **`vw`** are relative to the **Viewport** (the actual visible window of the browser).

Think of them as "The Big Picture" units. They don't care about parents, nesting, or margins; they only care about how big the screen is.

#### 1. The Definitions
- **`vw` (Viewport Width):** 1 unit is equal to **1%** of the viewport's width.
- **`vh` (Viewport Height):** 1 unit is equal to **1%** of the viewport's height.

**The Math:**
If your browser window is **1000px wide** and **800px tall**:
- `10vw` = **100px**
- `10vh` = **80px**
- `100vw` = **Full width** of the screen.
- `100vh` = **Full height** of the screen.

### **vw VS %:**
#### 1. How Percentages (%) Calculate Space
When you set an element to `width: 100%`, the browser looks at the **available space** inside the parent.

- The browser first places the vertical scrollbar on the right.
- It then says, "Okay, the remaining space for content is $X$ pixels."
- `100%` becomes exactly that $X$ value. It fits perfectly.

#### 2. How Viewport Width (vw) Calculates Space:
The Viewport Units are defined by the **window size**, not the content area.

- `100vw` is equal to the width of the window **including** the scrollbar.
- If your scrollbar is 15px wide, `100vw` is the full screen width.
- Because the scrollbar sits _on top_ of the window, your element tries to go underneath it to reach the full width.
- The browser sees the element is now 15px wider than the _visible_ space and creates a **horizontal scrollbar** to let you see those hidden pixels.

# Chapter Five, CSS Box Model:

![[Screenshot 2026-02-08 235200.png]]

The auto set margins by the browsers can cause issues and problems with the developers, and that's why we use css-reset and we take the control over the components:
``` css
* {
	margin: 0;
	padding: 0;
	box-size: border-box;
}
```

### content-box vs border-box:
In content-box sizing, the sizing is set according to the content, regardless the padding and the margins, which means, if we have h1 tag with a width of 400px, this 400px will be the size of the text, for example if we have  a defined 50px margin and 50 px padding, the total box size will be 500px, but for border-box, the size defined will be used as the total final size, for example when you define the width of h1 tag to be 400px, 400px = text-size + margin + padding, so you simply get the total size you need, and it is calculated easily out of the box!

#### **Ways to define margins:**
``` css
.container {
	border: 2px dashed red;
	font-size: 1.5rem;
	margin: 2em;
}
```
You can also define each side, define the sides explicitly:
``` css
.container {
	border: 2px dashed red;
	font-size 1.5rem;
	margin-right: 2em;
	margin-left: 2em;
	margin-top: 2em;
	margin-bottom: 2em;
}
```
but why would you do so! you won't write such syntax, but you may need to do this if one of the sides is unlike the other sides; as following:
``` css
.container {
	border: 2px dashed red;
	font-size 1.5rem;
	margin-right: 2em;
	margin-left: 2em;
	margin-top: 1.5em;
	margin-bottom: 2em;
}
```
but there is another shorter way to do so!
`margin: top right bottom left;`
so you can use this way in such a case!
``` css
.container {
	border: 2px dashed red;
	font-size 1.5rem;
	margin: 1.5em 2em 2em 2em;
}
```

outline and outline-offset:
``` css
.container {
	border: 10px double red;
	font-size: 1.5rem;
	margin: 1.5em;
	padding: 1.5em;
	outline: 5px solid purple;
	outline-offset: 5px;
}
```
**Note:** Outline is unlike border, while border takes size in the box model; outline doesn't.

**Circles in CSS:**
``` css
.circle {
	width: 100px;
	height: 100px;
	background-color: gold;
	border-radius: 50%;
}
```

# Chapter Six, Typography:

**Font size inheritance:**
``` css
body {
	padding: 10%;
	font-size: 2rem;
	color: purple;
}

input, button {
	font: inherit;
}
```
### Text Decorations:
The `text-decoration` property in CSS is the go-to tool for adding visual flair to text, most commonly used for underlines, strikethroughs, and overlines. While it used to be a simple single-value property, it is now a **shorthand** for several sub-properties that offer granular control.

#### The Sub-Properties:
To get the most out of text decorations, it helps to know what’s happening under the hood:
- **`text-decoration-line`**: Sets the type of decoration (`underline`, `overline`, `line-through`, or `none`).
- **`text-decoration-color`**: Changes the color of the line (e.g., `red`, `#00ff00`).
- **`text-decoration-style`**: Changes the appearance of the line (`solid`, `double`, `dotted`, `dashed`, or `wavy`).
- **`text-decoration-thickness`**: Allows you to set a specific width for the line (e.g., `3px` or `auto`).
#### Common Usage Examples:
``` css
p {
	text-decoration: underline; /* Standard Underline */
	text-decoration: line-through; /* Strikethrough */
	/* Wavy RedUnderline */
	text-decoration: text-decoration: underline wavy red;
	/* Thick Dashed Line */
	text-decoration: underline dashed 5px;
	/* Remove Link Underline */
	text-decoration: none;
	text-transform: lowercase; /* Convert every letter to lowercase */
	text-transform: uppercase; /* Convert every letter to uppercase */
}
```
### Text Alignment:
``` css
p {
	/* Text allignment: */
	text-align: left;
	text-align: justify;
	text-align: right;
	text-indent: 2em;
	line-height: 1.5; /* Change the spacing between the lines */
	letter-spacing: 1em; /* Change the spacing between the letters */
	word-spacing: 0.5em; /* Change the spacing between the letters */
}
```
### Font Weights, Styles, and Families:
``` css
p {
	font-weight: bolder;
	font-style: italic;
	font-family: sans-serif;
}
```

### Font Stack:
The reason there are more than one "family" listed is to create a **fail-safe system**. Browsers read that list from left to right; if they can't find the first one, they move to the next.

``` css
body {
	font-family: 'Courier New', 'Courier', monospace;
}
```

### Choosing External Fonts:
**Google Fonts:** https://fonts.google.com/

Choose the font from google fonts and then get the external link and place it in the html header tag:
``` html
<header>
	<link rel="preconnect" href="https://fonts.googleapis.com">
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
	<link href="https://fonts.googleapis.com/css2?family=Roboto:ital,wdth,wght@0,75..100,100..900;1,75..100,100..900&display=swap" rel="stylesheet">
</header>
```

And we will be given CSS class for variable style:
``` css
.roboto-<uniquifier> {
  font-family: "Roboto", sans-serif;
  font-optical-sizing: auto;
  font-weight: <weight>;
  font-style: normal;
  font-variation-settings:
    "wdth" <width>;
}
```
Snippet from **Google Fonts**. It’s a template where you need to replace the "placeholders" (the text inside `< >`) with actual values to make the code functional.
Here is exactly how to clean that up and use it:

1. Replace the Placeholders
	You need to swap out the bracketed terms with your desired settings:
	- **`<uniquifier>`**: Replace this with a name that makes sense for your project (e.g., `header-font` or `body-text`).
	- **`<weight>`**: Use a number like `400` (normal) or `700` (bold).
	- **`<width>`**: Since Roboto is a variable font, this controls how wide or narrow the letters are (usually `100` for standard).
	
**Example of a "finished" version:**
``` css
.roboto-body {
  font-family: "Roboto", sans-serif;
  font-optical-sizing: auto;
  font-weight: 400;
  font-style: normal;
  font-variation-settings: "wdth" 100;
}
```

Finally apply the class in the html tag:
``` html
<p class="roboto-body">This text will now appear in Roboto!</p>
```


# Chapter 7, Styling links:

Links are usually placed in a tags!
``` css
a {
	text-decoration: underline;
	cursor: pointer;
	color: blue;
}
```

Sudo classes:
``` css
a:visited {
	color: purple;
}
a:hover, a:focus {
	text-decoration: none;
}
a:active {
	color: red;
}
```

# Chapter 8, List Styles:

``` css
ol {
	/* this will make the numbering in the alphapet system: */
	/* the default value is 'decimal' */
	list-style-type: lower-alpha;
	
	/* we can also make ol act as ul: */
	list-style-type: disc;
	
	/* we can make it empty 'non-filled' circle too: */
	list-style-type: circle;
	
	/* square bullet points: */
	list-style-type: square;
}
```

If we want to use external icon as the bullet points we can do as following:
``` css
ul {
	list-style-image: url("url-here");
}
```
Changing the color of specific list item:
``` css
ul li:nth-child(even) {
	color: red;
}
```

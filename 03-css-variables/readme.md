# CSS Variables

This is the third project of WesBos's [JS30 series](https://javascript30.com). 
We will be building a simple app to play with CSS variables. Check the first few minuted of the video to find out what were building -

<iframe width="640" height="360" src="https://www.youtube.com/embed/AHLNzv13c2I?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Here is the codepen with the starter files

<iframe height='455' scrolling='no' title='JS30-03-css-vars-a' src='//codepen.io/deepakkarki/embed/wXrwwO/?height=455&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/wXrwwO/'>JS30-03-css-vars-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


At under 50 lines we have minimal, self explanatory starter code.

The main part we'll be dealing with in the HTML is the user control

```html
<div class="controls">
    <label for="spacing">Spacing:</label>
    <input id="spacing" type="range" name="spacing" min="10" max="200" value="10" data-sizing="px">

    <label for="blur">Blur:</label>
    <input id="blur" type="range" name="blur" min="0" max="25" value="10" data-sizing="px">

    <label for="base">Base Color</label>
    <input id="base" type="color" name="base" value="#ffc600">
</div>
```

Change the border thickness and blur based on the sliders. Change the color of the border and heading to the 'Base Color' picked.

Quick intro to CSS variables -

```css
/* Declaring a variable*/
element {
  --main-bg-color: brown;
} 

/* Using the variable */
element {
  background-color: var(--main-bg-color);
}
```

All variables start with `--` and are followed by a value, it is like settign any other CSS property except that this value set can be accessed by using `var(--var-name)`. Variables are also inherited (following the standard CSS cascading principles), variables defined at `:root` apply to all the other elements unless the variable is overridden by a closer ancestor. Let's take an example, assume we have the following HTML -

```html
<div class="one">
  <div class="two">
    <div class="three"></div>
    <div class="four"></div>
  </div>
</div>
```

And this is our CSS

```css
.two {
  --test: 10px;
}

.three {
  --test: 2em;
}
```

Results (note that the inheritence is similar to any other inheritable CSS property) -

- for the class="two" element: 10px
- for the class="three" element: 2em
- for the class="four" element: 10px (inherited from its parent)
- for the class="one" element: invalid value, which is the default value of any custom property.

To access the variable from JS -

```js
//assuming :root is being set, else call setProperty on the specific element
document.documentElement.style.setProperty('--var-name', '$val');
```

This was taken from an example on [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables), go there to learn more about CSS variables.


So now to carry on with our web page, we can break it down into three parts -

1. Set the CSS properties to read from the variable
2. Capture input
3. Update the variable

### Set the CSS variables

We have three variables `--base`, `--spacing` and `--blur`

```css
:root {
  --base: yellow;
  --spacing: 5px;
  --blur: 0px;
}

img {
  padding: var(--spacing);
  background: var(--base);
  filter: blur(var(--blur));
}

.hl {
    color: var(--base);
}
```

So now the styles are set by the variables, chaging the variables would automatically update the styles wherever the variables were referenced!

### Capture the input

One thing to note, `htmlElem.dataset` gives us the object that contains key value pairs of all `data-*` attributes. 
Eg. If our element was `<div data-x="a" data-y="b"></div>`, we'd get `{'x': 'a', 'y': 'b'}` and it is of type `DOMStringMap`

```js

//get the list of all the input element
const inputs = document.querySelectorAll('.controls input')

//doing it a bit differently than WesBos here, this way seems better
inputs.forEach(input => input.addEventListener('input', handleUpdate))

// we need to update the correct variable with the new value
function handleUpdate(){
  // this => the element that generated the input event

  // check if the value needs to be suffixed with a unit
  const suffix = this.dataset.sizing || '';
  
  // this.name is the same as the CSS variable it should update
  document.documentElement.style.setProperty(`--${this.name}`, this.value + suffix);
}
```

This completes our webpage, completed codepen below -

<iframe height='457' scrolling='no' title='JS30-03-css-vars-b' src='//codepen.io/deepakkarki/embed/RJLoOO/?height=457&theme-id=dark&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/RJLoOO/'>JS30-03-css-vars-b</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
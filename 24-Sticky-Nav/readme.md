# Sticky Nav

This is the 24th project of WesBos's [JS30 series](https://javascript30.com).
Today we'll learn how to make a sticky nav in JS.

Video -

<iframe width="640" height="360" src="https://www.youtube.com/embed/5FLOBCGH3_U?list=PLu8EoSxDXHP6CGK4YVJhL_VWetA865GOH" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Codepen -

<iframe height='265' scrolling='no' title='JS30-24-stickyNav-a' src='//codepen.io/deepakkarki/embed/YvJQGo/?height=265&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/YvJQGo/'>JS30-24-stickyNav-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

The `<nav>` element is what we'll be manipulating.

```html
 <!-- heading before the nav -->
<header>...</header>

 <!-- the nav bar -->
<nav id="main">
  <ul>
    <li class="logo"><a href="#">LOST.</a></li>
    <li><a href="#">Home</a></li>
    <li><a href="#">About</a></li>
    <li><a href="#">Images</a></li>
    <li><a href="#">Locations</a></li>
    <li><a href="#">Maps</a></li>
  </ul>
</nav>

<!-- rest of the body -->
<div class="site-wrap"> ... </div>
```

We need to figure out the distance of the nav bar from the top of the page, then when we've scrolled to that point we need to change the CSS property of the nav to a fixed position.

```js
const nav = document.querySelector('#main');
let topOfNav = nav.offsetTop;

function fixNav(){
}

window.addEventListener('scroll', fixNav);
```

`elem.offsetTop` gives us the distance of the element in px. We listen to the scroll event and call `fixNav()` to check if the nav position should change. Instead of changing the style, we can just add a class on the body element itself. 

`window.scrollY` gives us the vertical scroll in px.

```css
body.fixed-nav nav {
  position: fixed;
  box-shadow:0 5px 0 rgba(0,0,0,0.1);
}
```

```js
function fixNav() {
  if (window.scrollY >= topOfNav) {
    document.body.style.paddingTop = nav.offsetHeight + 'px';
    document.body.classList.add('fixed-nav');
  } else {
    document.body.classList.remove('fixed-nav');
    document.body.style.paddingTop = 0;
  }
}
```

If the scroll us beyond the `topNav` position, then the `fixed-nav` class is added. Else it is removed. As we see from the CSS fragment above, once the body has the class of `fixed-nav`, the position of the nav is fixed (to the top) and there is also a box shadow.

Now when you fix the position of an element, the space it occupies goes free for other elements to reflow into. Hence along with the `fixed-nav` class, we add and remove the padding `document.body.style.paddingTop` equivalent to the height of the nav bar.

Now for a little more styling 

```css
.fixed-nav li.logo {
  max-width:500px;
}

body.fixed-nav .site-wrap {
  transform: scale(1);
}
```

Now along with the fixed nav bar, the logo animates into view and the text gets a little bigger.

Here is the final code -

<iframe height='442' scrolling='no' title='JS30-24-stickyNav-b' src='//codepen.io/deepakkarki/embed/aKRypz/?height=442&theme-id=dark&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/aKRypz/'>JS30-24-stickyNav-b</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
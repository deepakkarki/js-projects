# JS Events

This is the 25th project of WesBos's [JS30 series](https://javascript30.com).
In this exercise we'll play with JS events - bubbling, capture and one time events.

<iframe width="640" height="360" src="https://www.youtube.com/embed/F1anRyL37lE?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Code -

<iframe height='376' scrolling='no' title='JS30-25-Events-a' src='//codepen.io/deepakkarki/embed/LrgJOZ/?height=376&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/LrgJOZ/'>JS30-25-Events-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


We'll just be trying out different exercises to figure out how events in the browser work!

The HTML structure we'll be working with - 

```html
<div class="one">
  <div class="two">
    <div class="three">
    </div>
  </div>
</div>
```

These are just three nested boxes. We'll select them in the `divs` variable.

```js
const divs = document.querySelectorAll('div')
```

## Bubbling

```js
function bubbles(e){
  console.log(e.target)
  console.log("bubbles ", this.classList.value)
}

divs.forEach(div => div.addEventListener('click', bubbles))

// Output (after cliking on three)-

// <div class=​"three">​​</div>​
// bubbles  three
// <div class=​"three">​​</div>​
// bubbles  two
// <div class=​"three">​​</div>​
// bubbles  one
```

As we can see, the target is always the third div (inner most box), but click is triggered for each div. This is due to bubbling - where the event bubbles up to the parent. 


## Capturing

```js
function captures(e){
  console.log(e.target)
  console.log("captures ", this.classList.value)
}

divs.forEach(div => div.addEventListener('click', captures, {capture:true}))

// Output on cliking div.three

// <div class=​"three">​​</div>​
// captures  one
// <div class=​"three">​​</div>​
// captures  two
// <div class=​"three">​​</div>​
// captures  three
```

Again the target is always the div we've clicked on, but click is triggered on each of the boxes starting from the parent box this time! This is what setting `{capture:true}` will do. So instead of bubbling there is capture.


> Note that events still work the same in both cases.
>
> The event goes from parent to child in a pahse called as 'capture' and then
> from the target child all the way upto the window in a phase known as 'bubbling'
> 
> It's just that setting capture to true in `addEventListener()` will trigger the callback
> during the capture phase rather than the default bubbling phase.

Learn more at [Javascript.info](https://javascript.info/bubbling-and-capturing) and [MDN Docs](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#Bubbling_and_capturing_explained)


Example 

```js
function logAll(e){
    phase = {0:"unknown", 1:"capture", 2: "target", 3:"bubbling"}
    console.log(phase[e.eventPhase], this.classList.value)
}

// same handler for both phases, we log the event phase in the handler
divs.forEach(div => div.addEventListener('click', logAll))
divs.forEach(div => div.addEventListener('click', logAll, {capture:true}))

// Output on clicking div.three
// capture one
// capture two
// target three
// target three
// bubbling two
// bubbling one
```

Hopefully the above example helps you visualise what is happening! The `phase` object is derived from the phase to number mapping on [this page](https://developer.mozilla.org/en-US/docs/Web/API/Event/eventPhase).


## Stop Propagation

`Event.stopPropagation()` Prevents further propagation of the current event in the capturing and bubbling phases.

```js
function logEv(e){
  console.log(this.classList.value)
  e.stopPropagation()
}

divs.forEach(div => div.addEventListener('click', logEv))

//Output (without e.stopPropagation())
// three
// two
// one

// After adding e.stopPropagation()
// three
```

The event is no longer bubbled. The effect will be similar if done during the capture phase.


## Trigger once

When we want an event to trigger only once,

```js
function runOnce(e){
    console.log("I should run only once")
}
divs.forEach(div => div.addEventListener('click', runOnce, {once:true}))
```

When you click `div.three` you'll see `"I should run only once"` logged thrice (once per parent), which means it is called the first time. Click it again and nothing happens. Even if you click `div.one` or `div.two`, nothing happens! This is because the callbacks have already run once even though they were not the target!


-------

This concludes this exercise, I'm not adding a final codepen as it's simpler to just open your console and test this out.
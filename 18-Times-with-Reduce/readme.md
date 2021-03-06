# Add times with reduce

This is the 18th project of WesBos's [JS30 series](https://javascript30.com).
Today we'll be using map and reduce to add time of individual videos find the total time of a playlist.

Video -

<iframe width="640" height="360" src="https://www.youtube.com/embed/SadWPo2KZWg?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Codepen -

<iframe height='265' scrolling='no' title='JS30-18-reduce-a' src='//codepen.io/deepakkarki/embed/wXxGMa/?height=265&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/wXxGMa/'>JS30-18-reduce-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

The HTML structure we have is something similar to what there would be in a youtube playlist -

```html
<ul class="videos">
  <li data-time="5:43">
    Video 1
  </li>
  <li data-time="2:33">
    Video 2
  </li>
  ...
</ul>
```

The `<ul>` represents the playlist, each `<li>` is a video item. The `data-time` attribute holds the time of the video. What we need to do is find the total time of the playlist, i.e. add the individual times (represented as a string) of each element.

1. Extract all the times from the `<li>` into array
2. Convert each string in the array to seconds
3. Add all the seconds
4. Display the total time in "hh:mm:ss" format


## Extract times from items

We use `Array.from` to create an array object from the NodeList object `document.querySelectorAll` gives us.
Then we map the array to the `data-time` value of each element to get an array of times in strings.

```js
const items = Array.from(document.querySelectorAll('.videos li'))
const times = items.map(item => item.dataset.time)
```

## Convert string array into seconds array

We use map to convert each string into an interger which represents the time of the video in seconds.

```js
const seconds = times.map(time => {
    [min, sec] = time.split(':').map(parseFloat)
    return (min*60) + sec
})
```

If we have something like `'4:30'`, it is split into `["4", "30"]` and we convert it into numbers with `parseFloat()`. We capture the minutes and seconds in `min, sec` by ES6 destructuring.


## Add all the seconds

We use reduce to add all the time in seconds to get the total time in seconds.

```js
let totalSec = seconds.reduce((sum, time) => sum + time)
```


## Display the total time

We have to split the time in sec to hours, minutes and seconds. `totalSec` is the time we have in seconds, assume it's equal to `125`. First let's shave off the whole minutes from the seconds - `totalSec % 60` gives us the reminder of seconds once we have divided by 60 (we use 60 since one min has 60 sec). `125 % 60` will give us 5. Now `totalMin` is the number of whole minutes we have, that is `(125-5) / 60` viz 2 minutes. We repeat a similar process for hours - shave off any extra minutes and divide by 60 to get the whole hours. Try it out with a bigger number (greater than 3600, since there are 3600 secs in an hour).

```js
let secs = totalSec % 60
let totalMin = (totalSec - secs) / 60

let mins = totalMin % 60
let hours = (totalMin - mins) / 60

console.log(hours + ':' + mins + ':' + secs)
```

This completes our exercise. Final codepen -

<iframe height='423' scrolling='no' title='JS30-18-reduce-b' src='//codepen.io/deepakkarki/embed/YvjWaN/?height=423&theme-id=dark&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/YvjWaN/'>JS30-18-reduce-b</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


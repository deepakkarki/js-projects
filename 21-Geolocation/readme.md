# Geolocation

This is the 21st project of WesBos's [JS30 series](https://javascript30.com).
We make a really simple compass and speedometer with just JavaScript.

Video -

<iframe width="640" height="360" src="https://www.youtube.com/embed/X7Cbtra0C6I?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Starter file -

<iframe height='437' scrolling='no' title='JS30-21-geolocation-a' src='//codepen.io/deepakkarki/embed/YvOQop/?height=437&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/YvOQop/'>JS30-21-geolocation-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

The compass logo is got from an SVG, we'll be applying a transform on it based on the data from the `Position` object.

Get the handles to the html elements -

```js
const arrow = document.querySelector('.arrow');
const speed = document.querySelector('.speed-value');
```

We use `watchPosition` to get the data. If the position data changes (either by device movement or if more accurate geo information arrives), you can set up a callback function that is called with that updated position information. This is done using the `watchPosition()` function. The callback function is called multiple times, allowing the browser to either update your location as you move, or provide a more accurate location as different techniques are used to geolocate you. The error callback function, which is optional, can be called repeatedly.

```js
navigator.geolocation.watchPosition((data) => {
    console.log(data);
}, (err) => {
    console.error(err);
});
```

Manipulating the DOM elements with the position data -

```js
// inside watchPosition
speed.textContent = data.coords.speed;
arrow.style.transform = `rotate(${data.coords.heading}deg)`;
```

This completes this exercise. The codepen (though I doubt this will work unless you're on a phone/tablet)

<iframe height='265' scrolling='no' title='JS30-21-geolocation-b' src='//codepen.io/deepakkarki/embed/WyggPB/?height=265&theme-id=dark&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/WyggPB/'>JS30-21-geolocation-b</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
# Sort without articles

This is the 17th project of WesBos's [JS30 series](https://javascript30.com).
Today we learn how to sort HTML elements based based on their text content.

The video can be found here

<iframe width="640" height="360" src="https://www.youtube.com/embed/PEEo-2mRQ7A?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

The codepen for the starter code

<iframe height='265' scrolling='no' title='JS30-17-SortBands-a' src='//codepen.io/deepakkarki/embed/pKKeyv/?height=265&theme-id=dark&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/pKKeyv/'>JS30-17-SortBands-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


The data we have at hand 

```js
const bands = ['The Plot in You', 'The Devil Wears Prada', 'Pierce the Veil', 'Norma Jean', 'The Bled', 'Say Anything', 'The Midway State', 'We Came as Romans', 'Counterparts', 'Oh, Sleeper', 'A Skylit Drive', 'Anywhere But Here', 'An Old Dog'];
```

We need to insert the sorted data as `<li>` elements into

```html
<ul id="bands"></ul>
```

While sorting the data we need to ignore articles such as `the, an, a`.


Steps to take

- Call standard sort on the array
- Function to strip the strings
- Compare the stripped version
- Add the elements to the `ul#bands`


## Call standard sort on the array

First we sort normally, with the articles

```js
const sortedBands = bands.sort((a, b) => a > b ? 1 : -1);

//["A Skylit Drive", "An Old Dog", "Anywhere But Here", "Counterparts", "Norma Jean", "Oh, Sleeper", "Pierce the Veil", "Say Anything", "The Bled", "The Devil Wears Prada", "The Midway State", "The Plot in You", "We Came as Romans"]
```


## Function to strip the strings

We'll use a RegEx to find any instance of articles (`/i` implies case insensitivity), and reduce them to nothing.

```js
function strip(bandName) {
  return bandName.replace(/^(a |the |an )/i, '').trim();
}
```


## Compare the stripped version

```js
const sortedBands = bands.sort((a, b) => strip(a) > strip(b) ? 1 : -1);
```

## Add the elements to the list

Map the list data to a list of strings of `"<li>"`'s. Then concatinate them and set them to the inner html of the `<ul>`

```js
document.querySelector('#bands').innerHTML =
  sortedBands
    .map(band => `<li>${band}</li>`)
    .join('');
```

Finished codepen 

<iframe height='480' scrolling='no' title='JS30-17-SortBands-b' src='//codepen.io/deepakkarki/embed/LrrwQe/?height=480&theme-id=dark&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/LrrwQe/'>JS30-17-SortBands-b</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
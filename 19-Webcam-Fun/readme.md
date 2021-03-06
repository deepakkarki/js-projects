# Webcam fun

This is the 19th project of WesBos's [JS30 series](https://javascript30.com).
Today we'll use getUserMedia and Canvas to capture a video stream from a user's webcam and manipulate the pixels.

Video -

<iframe width="640" height="360" src="https://www.youtube.com/embed/ElWFcBlVk-o?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Code -

<iframe height='265' scrolling='no' title='JS30-19-webcam-a' src='//codepen.io/deepakkarki/embed/ZRjOgz/?height=265&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/deepakkarki/pen/ZRjOgz/'>JS30-19-webcam-a</a> by Deepak Karki (<a href='https://codepen.io/deepakkarki'>@deepakkarki</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


We're going to be working on a bunch of experiments relating to webcam data and HTML canvas.

* Getting webcam data and displaying it via `<video>` element
* Paint the data to a canvas
* Take a photo at any given time
* Add effects to the video in realtime

The JS template we're given -

We have the video and canvas elements, the canvas' 2D context, strip is the div where the photos are displayed, snap is the audio element that triggers on photo capture.

```js
const video = document.querySelector('.player');
const canvas = document.querySelector('.photo');
const ctx = canvas.getContext('2d');
const strip = document.querySelector('.strip');
const snap = document.querySelector('.snap');
```

## Get and display webcam data

The `MediaDevices.getUserMedia()` method prompts the user for permission to use a media input which produces a MediaStream with tracks containing the requested types of media. That stream can include, for example, a video track, an audio track, and possibly other track types.

It returns a Promise that resolves to a MediaStream object. If the user denies permission, or matching media is not available, then the promise is rejected with PermissionDeniedError or NotFoundError respectively.

Learn more about Media devices @ [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)

```js
function getVideo() {
  navigator.mediaDevices.getUserMedia({ video: true, audio: false })
    .then(localMediaStream => {
      video.src = window.URL.createObjectURL(localMediaStream);
      video.play();
    })
    .catch(err => {
      console.error(`OH NO!!!`, err);
    });
}
```

The `URL.createObjectURL(param)` method creates a DOMString containing a URL representing the object given in the parameter (`param`). The URL lifetime is tied to the document in the window on which it was created. The new object URL represents the specified File object or Blob object.

`param` : A File, Blob or MediaSource object to create an object URL for.

The url created looks like this `blob:http://localhost:3000/11ca48db-0726-4e9a-a0af-7131c2a93748`. We assign this as the video element's source, and set it to `play()`. This will show in realtime what the webcam sees.


## Paint the data on a canvas

The `drawImage()` method of the Canvas 2D API provides different ways to draw an image onto the canvas.

`void ctx.drawImage(image, dx, dy, dWidth, dHeight)`

`image` : An element to draw into the context. The specification permits any canvas image source (CanvasImageSource), specifically, a CSSImageValue, an HTMLImageElement, an SVGImageElement, an HTMLVideoElement, an HTMLCanvasElement, an ImageBitmap, or an OffscreenCanvas.
`dx` : The X coordinate in the destination canvas at which to place the top-left corner of the source image.
`dy` : The Y coordinate in the destination canvas at which to place the top-left corner of the source image.
`dWidth` : The width to draw the image in the destination canvas. This allows scaling of the drawn image. If not specified, the image is not scaled in width when drawn.
`dHeight` : The height to draw the image in the destination canvas. This allows scaling of the drawn image. If not specified, the image is not scaled in height when drawn.


```js
function paintToCanvas() {
  const width = video.videoWidth;
  const height = video.videoHeight;
  canvas.width = width;
  canvas.height = height;

  return setInterval(() => ctx.drawImage(video, 0, 0, width, height), 16)
}
```

We get the width and height of the video source (in my case 640 x 480), set it as the canvas dimentions. The image is redrawn every 16 miliseconds, or little over 60fps.


## Take a photo

Clicking on the "take phooto" button triggers the `takePhoto` function. The function plays the 'snap' audio, captures the canvas data into a jpeg.
Then a link tag is created with the attribute `download`. The image captured is placed inside the link as a img tag. When the user clicks on the image thumbnail, the 

```js
function takePhoto() {
  // played the sound
  snap.currentTime = 0;
  snap.play();

  // take the data out of the canvas
  const data = canvas.toDataURL('image/jpeg');
  const link = document.createElement('a');
  link.href = data;
  link.setAttribute('download', 'handsome');
  link.innerHTML = `<img src="${data}" alt="Handsome Man" />`;
  strip.insertBefore(link, strip.firsChild);
}
```

The `canvas.toDataURL('image/jpeg')` call converts the image the canvas is displaying into a JPEG and then turns it into a data url format `"data:image/jpeg;base64,/9j/4AA...QSkZ2Q=="`. [Data URLs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) are URLs prefixed with the data: scheme, allow content creators to embed small files inline in documents.


## Cool effects

For the effects we'll have the following steps 

1. Draw image to canvas
2. Extract pixels from canvas
3. Transform the pixel data
4. Write the pixels back to the canvas

For this we'll need to know how to extract and write the pixels to the canvas and how the pixel data actually looks like!

To get the pixel data out, we have `ctx.getImageData(sx, sy, sw, sh)`. The `getImageData()` method of the Canvas 2D API returns an ImageData object representing the underlying pixel data for the area of the canvas denoted by the rectangle which starts at (sx, sy) and has an sw width and sh height.

Similarly `ctx.putImageData(imgData, x, y)` puts back the image data (`imgData`) starting from `x,y`.

The actual data is of type [ImageData](https://developer.mozilla.org/en-US/docs/Web/API/ImageData). The ImageData interface represents the underlying pixel data of an area of a `<canvas>` element. The data is contained in `ImageData.data` which is a `Uint8ClampedArray` representing a one-dimensional array containing the data in the RGBA order, with integer values between 0 and 255 (included). The `Uint8ClampedArray` array represents an array of 8-bit unsigned integers clamped to 0-255.

So the whole image is represented as an array, each set of 4 array elements represent a pixel, i.e. an array of 8 elements would map to 
 [r1,g1,b1,a1, r2,g2,b2,a2] representing the RGBA value of two pixels. Since we our video is `640x480` we'll have `1228800` pixels (640 * 480 * 4).


The calls to the transforming functions will be added to `paintToCanvas` shown below. 

```js
function paintToCanvas() {
  // ...
  return setInterval(() => {
    //draw img to canvas
    ctx.drawImage(video, 0, 0, width, height);

    // take the pixels out
    let pixels = ctx.getImageData(0, 0, width, height);

    // mess with them
    // pixels = redEffect(pixels); // - red effect
    // pixels = rgbSplit(pixels); // - rgb split
    // ctx.globalAlpha = 0.8;  // - ghosting 
    // pixels = greenScreen(pixels); // green screen

    // put them back
    ctx.putImageData(pixels, 0, 0);

  }, 16);
}
```

Now to write each of the transforming function -


### Red effect

Loop through each pixel (`i+=4`). Increment the red value, decrement the green and half the blue.

```js
function redEffect(pixels) {
  for (let i = 0; i < pixels.data.length; i+=4) {
    pixels.data[i + 0] = pixels.data[i + 0] + 100; // RED
    pixels.data[i + 1] = pixels.data[i + 1] - 50; // GREEN
    pixels.data[i + 2] = pixels.data[i + 2] * 0.5; // Blue
  }
  return pixels;
}
```

This gives us an overall reddish aura/filter.


### RGB split

We displace the red, green and blue values of each pixel by different amounts to create the same image split in three but with red, blue and green tinges.

```js
function rgbSplit(pixels) {
  for (let i = 0; i < pixels.data.length; i+=4) {
    pixels.data[i - 150] = pixels.data[i + 0]; // RED
    pixels.data[i - 100] = pixels.data[i + 1]; // GREEN
    pixels.data[i - 175] = pixels.data[i + 2]; // Blue
  }
  return pixels;
}
```


### Ghosting 

We get the ghosting effect using the `ctx.globalAlpha` property of the Canvas 2D API which specifies the alpha value that is applied to shapes and images before they are drawn onto the canvas. The value is in the range from 0.0 (fully transparent) to 1.0 (fully opaque).

Set `ctx.globalAlpha = 0.1` and then call `ctx.putImageData(pixels, 0, 0)`. This persists the old image on the screen as the new image has a transparency of only 10%. So a image that is written persists for 10 more frames (since opacity of eac incoming frame is only 10%), which makes it seem as though the hand is following you. 


### Green Screen

We get the different tresholds for RGB values from the input sliders, then we extract RGB values for each pixel. Make sure that each of the RGB values fall within their respective min and max values. If not set opacity to zero - wiping out the pixel.

```js
function greenScreen(pixels) {
  const levels = {};

  document.querySelectorAll('.rgb input').forEach((input) => {
    levels[input.name] = input.value;
  });

  for (i = 0; i < pixels.data.length; i = i + 4) {
    red = pixels.data[i + 0];
    green = pixels.data[i + 1];
    blue = pixels.data[i + 2];
    alpha = pixels.data[i + 3];

    if (red >= levels.rmin
      && green >= levels.gmin
      && blue >= levels.bmin
      && red <= levels.rmax
      && green <= levels.gmax
      && blue <= levels.bmax) {
      // take it out!
      pixels.data[i + 3] = 0;
    }
  }

  return pixels;
}
```

----

That concludes this lengthy tutorial. I'm not going to add a completed codepen since the webcam permissions don't seem to work for me anyway (which is weird.)
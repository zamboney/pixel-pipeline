# pixel-pipeline
understand the way the pixels renders in the browsers



## User Experience
###### the "jank" effect


### What is "Jank"?
> Jank is any stuttering, juddering or just plain halting that users see when a site or app isn't keeping up with the refresh rate. Jank is the result of frames taking too long for a browser to make, and it negatively impacts your users and how they experience your site or app.
> ###### [jankfree.org](http://www.jankfree.org)


- Most of the devices today render there screen 60 frame per second (60 FPS  or 60Hz).
- This means that every change on the screen need to in the interval of 16ms (1 second / 60 = 16.66ms).
- when this isn't done you get the "jank" effect.
######lets look an [this](https://zamboney.github.io/pixel-pipeline/examples/jank-effect.html) example.


- we can see that in order to 'sticky' the circle:
    - the "h1" is "position: absolute".
    - a javascript script need to calculate the position from the top.
    - the script need to listen the "document.onscroll" event.
```css
h1{
        position: absolute;
}
```
```js
document.onscroll = function () {
        document.querySelector('h1').style.top =
            document.body.scrollTop + 'px';
}
```


when we remove the js section and change the
```
 position: absolute;
```
to
```
position: fixed;
```
the jank disappears.
lets look an [this](https://zamboney.github.io/pixel-pipeline/examples/jank-effect-fixed.html) example.


- This is a major jank that can be seen on small scale, most of the jank seen after abused the rendering system.
- In this lecture we will understand the way the pixel pipe line work, a cases of pipes and then we will discuss how to find and fix long frame rate that cause "jank".



## The Pixel Pipeline
##### The Way CSS/JS effect the rendering of the screen.


### Painting is actually two tasks:
- Creating a list of draw calls.
- Filling in the pixels.


### the complete pipeline is

![alt text](.https://zamboney.github.io/pixel-pipeline/images/JS-CSS_Style_Layout_Paint_Composite.png "pixel pipe line")


###JavaScript:
Typically JavaScript is used to handle work that will result in visual changes.


* JavaScript that triggers a visual change.
    ```js
        document.querySelector('div').style.top =
            document.body.scrollTop + 'px';
    ```
* Css / Web, animations.
    ```css
        animation: 2s animate alternate;
    ```
* Add new element to the DOM.
    ```js
        document.body.appendChild(
            document.createElement('span'));
    ```


###Style:
This is the process of figuring out which CSS rules apply to which elements based on matching selectors.
the computing time that take to get the element


expensive work on the style
```html
    <div id="main"><div>...<div>
        <ul class="first"></ul><ul></ul>
    </div></div></div>
```

```css
    #main > div > div ul.first + ul {
    }
```


cheep work on the style
```html
<div id="main"><div>...<div>
    <ul class="first"></ul><ul class="main_div_div_ul_second"></ul>
</div></div></div>
```
```css
    .main_div_div_ul_second {
    }
```


###Layout:
Once the browser knows which rules apply to an element it can begin to calculate how much space it takes up and where it is on screen.
```html
<html>
...
    <div style="width:100px">
        ...
    </div>
...
</html>
```

```html
<html>
...
    <div style="width:300px">
            ...
    </div>
...
</html>
```


###Paint:
Painting is the process of filling in pixels.
- Image change.
- Font color change.

```html
<html>
    ...
        <div style="color:black">
        ...
        </div>
    ...
</html>
```

```html
<html>
    ...
        <div style="color:blue">
            ...
        </div>
    ...
</html>
```


###Compositing:
drawn to the screen in the correct order so that the page renders correctly.
this is the z-index of the drawing.

this is **NOT** the z-index css attribute!



##Effect the pipeline
what the browser do when there is a new call for draw.


###Reflow:
- when there is a change with the element height, weight or position in the page.
- this will trigger all over the pipe line
![alt text](.https://zamboney.github.io/pixel-pipeline/images/JS-CSS_Style_Layout_Paint_Composite.png "pixel pipe line")
- [Example](https://zamboney.github.io/pixel-pipeline/examples/reflow.html)


###Paint Only:
- when there is a change in the element color, background or shadow.
- the layout didn't change so expect of that the pipeline is the same.
![alt text](.https://zamboney.github.io/pixel-pipeline/images/JS-CSS_Style_Paint_Composite.png "pixel pipe line")
- [Example](https://zamboney.github.io/pixel-pipeline/examples/paint.html)


###Composite:
- when the change isn't effect the painting, like scrolling.
- this is change jump directly to Composite
![alt text](.https://zamboney.github.io/pixel-pipeline/images/JS-CSS_Style_Composite.png "pixel pipe line")


![alt text](.https://zamboney.github.io/pixel-pipeline/images/csstriggers.png "csstriggers.com")
- Find the [pipeline effects](https://csstriggers.com/).



##best practices
##### organize your javascript and style to get the best of the pixel pipeline.


### Layout Thrashing
- getting element style cost performance from the browser.
- so don't abuse the browser by calling a element style.
- if it's must then do it as less as passable.


### Layout Thrashing (explain example)
- [Thrashing Example](https://zamboney.github.io/pixel-pipeline/examples/layoutThrashing.html)

Issue Code:
```js
for (var p = 0; p < balls.length; p++) {
        var width = div.offsetWidth;
        var b = balls[p];
        b.style.marginLeft = width + 'px';
}
```

- [Fix Thrashing Example](https://zamboney.github.io/pixel-pipeline/examples/layoutThrashing_fix.html)

Fix Code:
```js
var width = div.offsetWidth;
for (var p = 0; p < balls.length; p++) {
    var b = balls[p];
    b.style.marginLeft = width + 'px';
}
```


### From Javascript to Css Animation
- using javascript get great deal of browser resources.
- by moving the javascript animation to css animation the user will notice a smooth UX
- [Javascript Animation Example](https://zamboney.github.io/pixel-pipeline/examples/Javascript-animation-example.html).
- [Css Animation Example](https://zamboney.github.io/pixel-pipeline/examples/css-animation-example.html).
- [Side by Side](https://zamboney.github.io/pixel-pipeline/examples/animation-side-by-side.html).


### from set-Timeout to request-Animation-Frame
- setTimeout start at any time on the frame
- requestAnimationFrame start at the beginning of the frame
- this way, when request or change a layout property, the request or change have more success to finish before the frame end.
- that give a smoothed feeling for the user.
```js
    setTimeout(function(){/*CODE*/},1000);
```
```js
    setTimeout(function(){
        requestAnimationFrame(function(){/*CODE*/});
    },1000)
```


### Working With Web Workers
- calculating in javascript take a great deal of resources.
- when the calculation take place in the main thread you may get the "jank" feeling.
- by excel the hard computing to a web worker, the main thread is free to handle with the UI.
- **BE CAREFUL**! export script that isn't effects the DOM directly.


### Paint Storms 1/2
- the browser is using rectangle to paint the screen.
- this rectangle can be in difference size and their size impact the rendering of the screen.
- in order to optimize the rendering the rectangle should be as small as passable.
- when a small change in the screen effect the entire screen
- this is call paint storm.


### Paint Storms 2/2
- to reduce the paint storm, elements can have there own "Composite Layer" by using css like so:
```css
element { translateZ(0)}
```
or
```css
element { backface-visibility: hidden}
```
- [Paint Storm Example](https://zamboney.github.io/pixel-pipeline/examples/paint-storm.html)
- [Fix Paint Storm Example](https://zamboney.github.io/pixel-pipeline/examples/paint-storm_fix.html)



##Resources
- [jankfree.org](http://jankfree.org/)
- [the runtime performance checklist](http://calendar.perfplanet.com/2013/the-runtime-performance-checklist/)
- [parallax](https://www.html5rocks.com/en/tutorials/speed/parallax/)



### Tank You
Ran Itzhaki
[rani@sela.co.il](mailto:rani@sela.co.il)



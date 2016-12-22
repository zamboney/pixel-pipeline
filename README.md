# pixel-pipeline
A lecture about the rendering process and the browser way of rendering the page.


* the user experience [readmore](https://paul.kinlan.me/what-news-readers-want/)
    * the 60fps rule
* pixels-to-screen
    * JavaScript: Typically JavaScript is used to handle work that will result in visual changes
    * Style calculations: This is the process of figuring out which CSS rules apply to which elements based on matching selectors.
    * Layout: Once the browser knows which rules apply to an element it can begin to calculate how much space it takes up and where it is on screen.
    * Paint: Painting is the process of filling in pixels
    * Compositing: drawn to the screen in the correct order so that the page renders correctly
* painting is actually two tasks:
    * creating a list of draw calls.
    * filling in the pixels.
* rasterization - the feeling when the screen been jank
* pipelines:
    * JS / CSS > Style > Layout > Paint > Composite.
        * *reflow*: height, weight, top, left.
    * JS / CSS > Style > Paint > Composite
        * *paint only*: background-image, text color, or shadows.
    * JS / CSS > Style > Composite
        * browser jumps to just do compositing, animations or scrolling.
    * find the [pipeline effects](https://csstriggers.com/).


* **JS:**
    * setTimeout/setInterval vs requestAnimationFrame:
        * setTimeout in any point of the frame.
        * this will be run at the start of the frame.
        * In fact, jQuery’s default animate behavior today is to use setTimeout! You can patch it to use requestAnimationFrame, which is strongly advised
    * working with web workers:
        * make the layout calculation in the main thread.
        * doesn’t require DOM access
        * 3-4ms max.
* **CSS:**
    * Reduce the complexity of your selectors.
    * Measure your Style Recalculation Cost
    * CSS, [BEM.](https://en.bem.info/methodology/css/)
* **Layout**
    * avoid using layout
    * flexbox
    * Avoid layout thrashing
* **Paint**
    * Promote elements that move or fade
        * will-change: transform;
        * transform: translateZ(0);
    * Simplify paint complexity






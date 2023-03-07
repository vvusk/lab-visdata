# Modul 6 Visualisasi Data

## Basic Interactivity

In this tutorial, we present further examples on mouse events, animated transitions, and tooltips.

### Handling User Input

With D3 visualizations, you can leverage the full power of web technologies to create interactive visualizations. For example, you can add HTML forms to enable user input or bind event listeners directly to SVG elements.

We can bind an event listener to any DOM element using `d3.selection.on()` method. We show this in the following example where we use an HTML input slider to change the radius of an SVG circle.

Here's the html, css and main.js code example for below example.

[![interactivity1](https://i.im.ge/2023/03/05/7mhTn1.interactivity1.md.gif)](https://im.ge/i/7mhTn1)


```python
<!DOCTYPE html>
<html>
<head>
    <title>D3 Basic interactivity</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<!-- ... -->
<body>
    <!-- HTML form -->
    <div>
        <label for="radius-slider">Radius: <span id="radius-value">60</span></label>
        <input type="range" min="1" value="60" max="80" id="radius-slider">
    </div>

    <!-- Empty SVG drawing area -->
    <svg id="chart" width="200" height="200"></svg>

    <script src="js/d3.v7.min.js"></script>
    <script src="js/main.js"></script>
</body>
</html>
</html>
```


```python
body {
  font-family: 'Helvetica', 'Arial', sans-serif;
}

#radius-value {
  width: 30px;
  display: inline-block;
}
```


```python
const svg = d3.select('svg');

// Show circle with initial radius of 60px
const circle = svg.append('circle')
    .attr('cx', 100)
    .attr('cy', 100)
    .attr('fill', 'none')
    .attr('stroke', 'green')
    .attr('r', 60);

function updateCircle(radius) {
  circle.attr('r', radius);
}

// Event slider for input slider
d3.select('#radius-slider').on('input', function() {
  // Update visualization
  updateCircle(parseInt(this.value));

  // Update label
  d3.select('#radius-value').text(this.value);
});
```

Above example was a very simple one. We have the following recommendations when you use the previously introduced class structure for visualizations:
- Add global event listeners (e.g., checkboxes, sliders, ...) in `main.js`. The advantage is that `main.js` acts as a controller and you can trigger changes in multiple visualization components.
- Update configurations (e.g., `myChart.config.radius = 100`) or data (i.e., `myChart.data = data`).
- Call `myChart.updateVis()` to update the visualization accordingly.
- Add chart-specific events within the `myChart.js` class. For example, when you want listen to mouseover events on SVG circles, use D3's `.on()` function when you render the circles:

` svg.selectAll('circle')
		.data(data)
   .join('circle')
   	.attr('fill', 'green')
   	.attr('r', 4)
   	.attr('cx', d => vis.xScale(d.x))
   	.attr('cy', d => vis.yScale(d.y))
   	.on('mouseover', d => console.log('debug, show tooltip, etc.'))`

- Alternatively, you could also use jQuery or other JS libraries to handle events.

### Animated Transitions

By now, you should have a solid understanding of how to select elements and update various types of SVG attributes:

`d3.selectAll('circle').attr('fill', 'blue');`

We selected all circles and changed the fill color.

D3 evaluates every `attr()` statement immediately, so the changes happen right away. But sometimes it is important to show the user what's happening between the states and not just the final result. D3 provides the `transition()` method that makes it easy to create these smooth, animated transitions between states:

`d3.selectAll('circle').transition().attr('fill", 'blue');`

When you add `.transition()`, D3 interpolates between the old values and the new values, meaning it normalizes the beginning and ending values, and calculates all their in-between states.

In our second example, the circle color changes from red to blue over time. The default time span is 250 milliseconds but you can specify a custom value by simply using the `duration()` method directly after `transition()`. We assume there are existing red circles on the web page.

This example shows an animation from red to blue (3 seconds):

`d3.selectAll('circle')
	.transition()
	.duration(3000)
	.attr('fill', 'blue');`

[![animation1](https://i.im.ge/2023/03/06/7NJypm.animation1.md.gif)](https://im.ge/i/7NJypm)


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <svg></svg>
      
      <script> 
        const w = 500;
        const h = 200;

        const svg = d3.select('svg')
            .attr("width", w)
            .attr("height", h);

        svg.append('circle')
            .attr('cx', 50)
            .attr('cy', 50)
            .attr('fill', 'red')
            .attr('r', 30);

        d3.selectAll('circle')
            .transition()
            .duration(3000)
            .attr('fill', 'blue');
            
      </script>
   </body>
</html>
```

If you need to delay an animation, you can add the `delay()` method right after `transition()`.

`d3.selectAll('circle')
			.transition()
			.delay(500)
			.duration(3000)
			.attr('fill', 'blue');`
            
#### Animation for Visualization

If done right, animations can make a visualization better and help engage the user. If done wrong (i.e., you don't follow key principles), you will achieve exactly the opposite results.

Pros
- Transitions show what is happening between states and add a sense of continuity to your visualization
- Animations can draw the user's attention to specific elements or aspects
- Animations can provide the user with interactive feedback

Cons
- Too many transitions will confuse the user (e.g., overused PowerPoint effects)
- If the transition is not continuous, animations look strange and can even be deceiving based on the interpolation used.
- Animation across many states is the least effective use case for data analysis tasks. In this case, use a static comparison of several charts/images (e.g., small multiples) instead of creating video-like animations.

### Tooltips

#### HTML tooltips

When you create interactive visualizations, you often want to show tooltips to reveal more details about your data to your audience. There are different approaches to achieve this but we recommend the creation of a global tooltip container outside of the SVG that you can show/hide and position whenever users hover over a mark. This approach allows you to create more complex tooltip objects that can be styled with CSS and contain images or even small visualizations.

Example implementation workflow:
- Add tooltip placeholder to the HTML file:

 `<div id="tooltip"></div>`
- Set absolute position, hide tooltip by default, and define additional optional styles in CSS:

 `#tooltip {
 	position: absolute;
 	display: none;
 	/* ... other tooltip styles ... */
 }`
 
- In JS (D3), update tooltip content, position, and visibility when users hovers over a mark. We distinguish between three different states: `mouseover`, `mousemove`, and `mouseleave` (in case of small marks, we add the positioning to the `mouseover` function and leave out `mousemove`).



```python
 myMarks
     .on('mouseover', (event,d) => {
       d3.select('#tooltip')
         .style('display', 'block')
         // Format number with million and thousand separator
         .html(`<div class="tooltip-label">Population</div>${d3.format(',')(d.population)}`);
     })
     .on('mousemove', (event) => {
       d3.select('#tooltip')
         .style('left', (event.pageX + vis.config.tooltipPadding) + 'px')
         .style('top', (event.pageY + vis.config.tooltipPadding) + 'px')
     })
     .on('mouseleave', () => {
       d3.select('#tooltip').style('display', 'none');
     });
```

**Tooltip examples**

[![Sortable bar chart with tooltips](https://i.im.ge/2023/03/07/7AbEhJ.tooltip1.md.gif)](https://im.ge/i/7AbEhJ)

The *Sortable bar chart with tooltips* code and preview can be seen in the [codesandbox](https://codesandbox.io/s/festive-galois-qhesr4?file=/index.html).

[![Interactive scatter plot with filters and tooltips](https://i.im.ge/2023/03/07/7Agou9.tooltip2.md.gif)](https://im.ge/i/7Agou9)

The *Interactive scatter plot with filters and tooltips* code and preview can be seen in the [codesandbox](https://codesandbox.io/s/eloquent-babycat-5nb1tm?file=/index.html).

#### Tooltips for path elements (fuzzy position)

Until now, we have created tooltips only for basic SVG elements, such as circles or rectangles. When users hover over a specific mark, we can easily get the underlying data and show it at that position. However, when we create line or area charts (SVG paths), we typically want to allow users to hover anywhere over a path and see a tooltip, and not just at a few specific points.

[![tooltip_position](https://i.im.ge/2023/03/07/7AZoyY.tooltip-position.md.png)](https://im.ge/i/7AZoyY)

We illustrate the mechanism for showing tooltips at fuzzy positions based on a line chart (`date` on x-axis, `stock price` on y-axis).
- We add a tracking area that covers the whole chart. Whenever users place their mouse cursor inside this area, we want to show a tooltip. After every `mousemove` event we need to update the tooltip accordingly.

`const trackingArea = vis.chart.append('rect')
    .attr('width', width)
    .attr('height', height)
    .attr('fill', 'none')
    .attr('pointer-events', 'all')
    .on('mouseenter', () => {
      vis.tooltip.style('display', 'block');
    })
    .on('mouseleave', () => {
      vis.tooltip.style('display', 'none');
    })
    .on('mousemove', function(event) {
      // See code snippets below
    })`
    
- Get the x-position of the mouse cursor using `d3.pointer()`. We only want to show a stock price tooltip for a specific date and the y-position is not relevant in this case, but can be extracted similarly.

`const xPos = d3.pointer(event, this)[0]; // First array element is x, second is y`

- We have used D3 scales multiple times to convert data values (input domain) to pixels (output range). We can now use the `invert()` function to do the opposite and get the date that corresponds to the mouse x-coordinate.

`const date = vis.xScale.invert(xPos);`

- We want to find the data point (stock price) based on the selected date. Therefore, we use a special helper function `d3.bisector` that returns the nearest `date` (in our dataset) that falls to the left of the mouse cursor. We initialize the `d3.bisector` somewhere outside of `.on('mousemove')`:

`const bisect = d3.bisector(d => d.date).left;`

Then we can use `bisect()` to find the nearest object `d` in our dataset.
(Don't worry too much if this looks cryptic to you. Read more details in these tutorials: [d3noob.org](http://www.d3noob.org/2014/07/my-favourite-tooltip-method-for-line.html), [observable](https://observablehq.com/@d3/d3-bisect))

`const index = vis.bisectDate(vis.data, date, 1);
const a = vis.data[index - 1];
const b = vis.data[index];
const d = b && (date - a.date > b.date - date) ? b : a;
// d contains: { date: ..., stockPrice: ... }`

At the end we can display an HTML or SVG tooltip with the available mouse coordinates and the corresponding data.

See the complete interactive line chart example on [codesandbox](https://codesandbox.io/s/crimson-shape-p3i068?file=/index.html).

[![tooltip3](https://i.im.ge/2023/03/07/7Akdr0.tooltip3.md.gif)](https://im.ge/i/7Akdr0)

Sources:
- https://github.com/UBC-InfoVis/447-materials/tree/22Jan/tutorials/4_D3_Tutorial_Basic_Interactivity
- https://bost.ocks.org/mike/transition/
- http://using-d3js.com/index.html
- http://www.d3noob.org/2014/07/my-favourite-tooltip-method-for-line.html
- https://observablehq.com/@d3/d3-bisect
- http://www.d3noob.org/2014/04/using-html-inputs-with-d3js.html

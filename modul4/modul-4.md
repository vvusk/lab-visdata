# Modul 4 Visualisasi Data

## Making a Chart

In this tutorial, you will learn how to create basic chart types, such as bar charts, scatter plots, and line charts. We will introduce the concept of input domains and output ranges which are the basis for D3 scales and needed in almost every visualization. We will also show you an object-oriented approach to make visualization components reusable.

### Reusable D3 Components (ES6 Classes)

JavaScript has some versions, one of them is ES6. ES6 stands for ECMAScript 6. ECMAScript was created to standardize JavaScript, and ES6 is the 6th version of ECMAScript, it was published in 2015, and is also known as ECMAScript 2015.

Thinking about the structure of your project early on can save you a lot of time and will make your implementation more robust, extensible and reusable. This becomes particularly important when we create interactive visualizations where the underlying data changes or small multiple displays.

#### Divide and conquer

You should always try to split a complex problem into smaller, easier-to-tackle sub-problems. Each sub-problem can then be solved independently and afterwards integrated into the final system.

**D3 visualizations should be organized and structured using individual classes for different chart types**: do not have one monolithic file containing all of your code. Also, think about **consistent structure for the functions within each class**, to make these components as flexible and reusable as possible.

We recommend creating a JavaScript class for each visualization chart type that is used following this pipeline:

[![d3_components_overview](https://i.im.ge/2023/02/25/7SfmmJ.d3-components-overview.md.png)](https://im.ge/i/7SfmmJ)

#### Function structure of an individual class

ES6 introduced classes to better support object-oriented programming that you should use. Note that when reading code for older JavaScript versions, a similar functionality was achieved by using Object.prototype functions.

Similar to other programming languages, ES6 classes have a constructor and properties can be defined by using the keyword `this`:


```python
// ES6 Class
class BarChart {

  constructor(_config) {
    this.config = {
      parentElement: _config.parentElement,
      containerWidth: _config.containerWidth || 500,
      containerHeight: _config.containerHeight || 140,
      margin: { top: 10, bottom: 30, right: 10, left: 30 }
    }

    // Call a class function
    this.initVis();
  }

  initVis() {
    ...
  }

  ...
}
```

Instantiating a new instance of a class is done by using the keyword `new`. The constructor should be used to define properties:


```python
// Create an instance (for example in main.js)
let barchart = new BarChart({
    'parentElement': '#bar-chart-container',
    'containerHeight': 400
});
```

Object attributes can be updated afterwards:

`barchart.data = data;`

The variables should be stored in the chart object. We recommend avoiding simply using the `this` keyword within complex class code involving SVG elements because the scope of `this` will change and it will cause undesirable side-effects. Instead, we recommend creating another variable (for example `vis` or `_this`) at the start of each function to store the `this`-accessor.


```python
initVis() {
    let vis = this;

    vis.svg = d3.select(vis.config.parentElement).append('svg');
    vis.chart = vis.svg.append('g')
        .attr('transform', `translate(${vis.config.margin.left},${vis.config.margin.top})`);
    ...
}
```

The functions `initVis()`, `updateVis()`, and `renderVis()` should be used, following the implementation pipeline above. You might also need additional functions. The goal is to execute only the code that is needed to update the chart instead of removing and redrawing the entire chart after a user interaction; this code structure makes that goal straightforward to achieve.


```python
initVis() {
    let vis = this;
    ...
}
updateVis() {
    let vis = this;
    ...
}
renderVis() {
    let vis = this;
    ...
}
```

#### Breakdown of project into classes

In this example, there are two chart types, stacked area chart and timeline, and so there are two class files, `stackedAreaChart.js` and `timeline.js`.

[![d3_components_overview_2](https://i.im.ge/2023/02/25/7Sf8XY.d3-components-overview-2.md.png)](https://im.ge/i/7Sf8XY)

The divide-and-conquer concept (i.e., splitting up a complex problem into various sub-tasks) also applies to the overall file structure of your project.

We recommend that you create object instances for the chart type classes in the file `main.js`, which should be the entry point for your application, so that your code stays clean and understandable. For example, if you want to use the same data for multiple charts, you would load the data only once in `main.js`, and then re-use it in each class instance.

This methodology will become very helpful for developing larger systems and more sophisticated interaction mechanisms.

What we need to do:
- Create a new re-usable component, `barchart.js`, with a constructor, and the 3 functions we recommend: `initVis()`, `renderVis()`, `updateVis()`.
- Fill out your constructor and call `initVis()` at the end. Constructors will often take either 1 or 2 parameters: `config` and `data`.
- Decompose part of the code from `showBarChart()` into `initVis()`. Often, we will define several inital features of our vis in initVis():
    - width/height
    - x/y scales
    - x/y axes (including their `<g>` tags)
    - group (`<g>`) elements that contain our chart
    - titles, legends, other static elements
- Fill out `updateVis()`.
    - Specify your x/y accessor functions `vis.xValue = d => d.value`
    - Set the scale input domains for your x/y scales
    - Call `renderVis()`
- Append rectangles and draw the chart in `renderVis()`
    - Append `<rect>` svg elements to the chart
    - (sneak-peak to the next tutorial, implement a *data-join* using the `enter-update-exit` pattern or `join` function)
    
We will make a more flexible chart for the bar chart we made in Modul 3. The data is [here](https://github.com/vvusk/lab-visdata/blob/692020a045b8ff29de1f7a9fdfe2261f47a0677b/modul3/sales.csv).


```python
<!--This is the html file, you can name it as you want-->
<!DOCTYPE html>
<html>
<head>
    <title>D3 Static Bar Chart</title>
    <link rel="icon" href="data:;base64,iVBORwOKGO=" />
    <link rel="stylesheet" href="css/style.css">
</head>
<body>    
    <!-- Empty SVG drawing area -->
    <svg id="barchart"></svg>
    
    <!-- D3 bundle -->
    <script src="js/d3.v7.min.js"></script>

    <!-- Our JS code -->
    <script src="js/barchart.js"></script>
    <script src="js/main.js"></script>
</body>
</html>
```


```python
//This is the style.css
.axis path,
.axis line {
  fill: none;
  stroke: #333;
  shape-rendering: crispEdges;
}

.axis text {
  font-family: sans-serif;
  font-size: 11px;
}

.bar {
  fill: steelblue;
  shape-rendering: crispEdges;
}
```


```python
//The content of the barchart.js
class Barchart {

  /**
   * Class constructor with basic chart configuration
   * @param {Object}
   * @param {Array}
   */
  constructor(_config, _data) {
    // Configuration object with defaults
    // Important: depending on your vis and the type of interactivity you need
    // you might want to use getter and setter methods for individual attributes
    this.config = {
      parentElement: _config.parentElement,
      containerWidth: _config.containerWidth || 500,
      containerHeight: _config.containerHeight || 140,
      margin: _config.margin || {top: 5, right: 5, bottom: 20, left: 50}
    }
    this.data = _data;
    this.initVis();
  }
  
  /**
   * This function contains all the code that gets excecuted only once at the beginning.
   * (can be also part of the class constructor)
   * We initialize scales/axes and append static elements, such as axis titles.
   * If we want to implement a responsive visualization, we would move the size
   * specifications to the updateVis() function.
   */
  initVis() {
    // We recommend avoiding simply using the this keyword within complex class code
    // involving SVG elements because the scope of this will change and it will cause
    // undesirable side-effects. Instead, we recommend creating another variable at
    // the start of each function to store the this-accessor
    let vis = this;

    // Calculate inner chart size. Margin specifies the space around the actual chart.
    // You need to adjust the margin config depending on the types of axis tick labels
    // and the position of axis titles (margin convetion: https://bl.ocks.org/mbostock/3019563)
    vis.width = vis.config.containerWidth - vis.config.margin.left - vis.config.margin.right;
    vis.height = vis.config.containerHeight - vis.config.margin.top - vis.config.margin.bottom;

    // Initialize scales
    vis.xScale = d3.scaleLinear()
        .range([0, vis.width]);

    vis.yScale = d3.scaleBand()
        .range([0, vis.height])
        .paddingInner(0.15);

    // Initialize axes
    vis.xAxis = d3.axisBottom(vis.xScale)
        .ticks(6)
        .tickSizeOuter(0);

    vis.yAxis = d3.axisLeft(vis.yScale)
        .tickSizeOuter(0);

    // Define size of SVG drawing area
    vis.svg = d3.select(vis.config.parentElement)
        .attr('width', vis.config.containerWidth)
        .attr('height', vis.config.containerHeight);

    // Append group element that will contain our actual chart 
    // and position it according to the given margin config
    vis.chart = vis.svg.append('g')
        .attr('transform', `translate(${vis.config.margin.left},${vis.config.margin.top})`);

    // Append empty x-axis group and move it to the bottom of the chart
    vis.xAxisG = vis.chart.append('g')
        .attr('class', 'axis x-axis')
        .attr('transform', `translate(0,${vis.height})`);
    
    // Append y-axis group 
    vis.yAxisG = vis.chart.append('g')
        .attr('class', 'axis y-axis');

    // Append titles, legends and other static elements here
    // ...
  }

  /**
   * This function contains all the code to prepare the data before we render it.
   * In some cases, you may not need this function but when you create more complex visualizations
   * you will probably want to organize your code in multiple functions.
   */
  updateVis() {
    let vis = this;

    // Specificy x- and y-accessor functions
    vis.xValue = d => d.sales;
    vis.yValue = d => d.month;

    // Set the scale input domains
    vis.xScale.domain([0, d3.max(vis.data, vis.xValue)]);
    vis.yScale.domain(vis.data.map(vis.yValue));

    vis.renderVis();
  }

  /**
   * This function contains the D3 code for binding data to visual elements.
   * We call this function every time the data or configurations change 
   * (i.e., user selects a different year)
   */
  renderVis() {
    let vis = this;

    // Add rectangles
    vis.chart.selectAll('.bar')
        .data(vis.data)
        .enter()
      .append('rect')
        .attr('class', 'bar')
        .attr('width', d => vis.xScale(vis.xValue(d)))
        .attr('height', vis.yScale.bandwidth())
        .attr('y', d => vis.yScale(vis.yValue(d)))
        .attr('x', 0);
    
    // Update the axes because the underlying scales might have changed
    vis.xAxisG.call(vis.xAxis);
    vis.yAxisG.call(vis.yAxis);
  }
}
```


```python
//The content of the main.js
/**
 * Load data from CSV file asynchronously and render bar chart
 */
d3.csv('data/sales.csv')
  .then(data => {
    // Convert sales strings to numbers
    data.forEach(d => {
      d.sales = +d.sales;
    });
    
    // Initialize chart
    const barchart = new Barchart({ parentElement: '#barchart'}, data);
    
    // Show chart
    barchart.updateVis();
  })
  .catch(error => console.error(error));
```

Then try running the html file. You'll see something similar to below image.

[![bar-chart2](https://i.im.ge/2023/02/19/7MM60K.bar-chart2.md.png)](https://im.ge/i/7MM60K)

Next, check out our scatter plot example on [https://codesandbox.io/s/serverless-field-r4nyy1?file=/index.html](https://codesandbox.io/s/serverless-field-r4nyy1?file=/index.html). This is different than the bar chart code in a few ways. It still uses the reusable chart approach, but includes a static html legend, and some chart titles.

[![scatterplot](https://i.im.ge/2023/02/26/7qMy3x.scatterplot.md.png)](https://im.ge/i/7qMy3x)

## Making Line and Area Charts

In the last tutorial, we briefly showed you how to create basic shape elements with SVG (rectangles, circles, text, straight lines) and we used some of those elements to build a bar chart and a scatter plot.

For creating line and area charts we want to use SVG's [path](https://www.w3schools.com/graphics/svg_path.asp) element. Specifying the coordinates for a path is significantly more complex than for basic shapes, as shown in the following code snippet and result below:

[![line-chart1](https://i.im.ge/2023/02/26/7d4jg1.line-chart1.md.png)](https://im.ge/i/7d4jg1)


```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>SVG path</title>
</head>
<body
    <svg width="500" height="200">
        <path style="fill: none; stroke: blue" d="M0 10 L100 75 L300 90 L350 20"></path>
    </svg>
</body>
</html>
```

Fortunately, D3 provides the `d3.line()` and `d3.area()` functions, allowing us to draw a line and area charts more efficiently. Basically these functions take our data and convert it into the SVG path coordinates we wrote above.

Using D3's *line generator*:

[![line-chart2](https://i.im.ge/2023/02/26/7d4ZSz.line-chart2.md.png)](https://im.ge/i/7d4ZSz)


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
        <style>
            body { font-family: Arial; }
        </style>
   </head>

   <body>
        <div id = "svgcontainer"></div>
      
      <script>
        var width = 500;
        var height = 300;
        var svg = d3.select("#svgcontainer")
           .append("svg").attr("width", width).attr("height", height);

        const data = [{x: 0, y: 10}, {x: 100, y: 75}, {x: 300, y: 90}, {x: 350, y: 20}]

        // Prepare a helper function
        const line = d3.line()
            .x(d => d.x)
            .y(d => d.y);

        // Add the <path> to the <svg> container using the helper function
        d3.select('svg').append('path')
            .attr('d', line(data))
            .attr('stroke', 'red')
            .attr('fill', 'none');
      </script>
   </body>
</html>
```

The *area generator* works similar. An area is defined by two polylines and we need to specify differing y-values (`y0` and `y1`). Most commonly, `y0` is defined as a constant representing zero. The first line (the topline) is defined by `y1` and is rendered first; the second line (the baseline) is defined by `y0` and is rendered second. The two lines typically share the same x-values.

[![line-chart3](https://i.im.ge/2023/02/26/7dBFxm.line-chart3.md.png)](https://im.ge/i/7dBFxm)


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
        <style>
            body { font-family: Arial; }
        </style>
   </head>

   <body>
        <div id = "svgcontainer"></div>
      
      <script>
        var width = 500;
        var height = 300;
        var svg = d3.select("#svgcontainer")
           .append("svg").attr("width", width).attr("height", height);

        const data = [{x: 0, y: 10}, {x: 100, y: 75}, {x: 300, y: 90}, {x: 350, y: 20}]

        // Prepare a helper function
        const area = d3.area()
          .x(d => d.x)      // Same x-position
          .y1(d => d.y)     // Top line y-position
          .y0(0)            // Bottom line y-position

        // Add the area path using this helper function
        d3.select('svg').append('path')
          .attr('d', area(data))
          .attr('stroke', 'green')
          .attr('fill', 'green');
      </script>
   </body>
</html>
```

Result if we change the baseline y-position: e.g., `.y0(150)`

[![line-chart4](https://i.im.ge/2023/02/26/7dBa9L.line-chart4.md.png)](https://im.ge/i/7dBa9L)

You can use D3's `.curve()` to interpolate the curve between points, for example, to smoothen the curve or to produce a step function, as shown below. Note: Use curve interpolation very carefully as it may misrepresent the actual data!

[![line-chart5](https://i.im.ge/2023/02/26/7dB0qS.line-chart5.md.png)](https://im.ge/i/7dB0qS)


```python
const area = d3.area()
  .x(d => d.x)
  .y1(d => d.y)
  .y0(150)
  .curve(d3.curveNatural);

// ...
```

Let's try the `.curveStep` but using line.

[![line-chart6](https://i.im.ge/2023/02/26/7deX7f.line-chart6.md.png)](https://im.ge/i/7deX7f)


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
        <style>
            body { font-family: Arial; }
        </style>
   </head>

   <body>
        <div id = "svgcontainer"></div>
      
      <script>
        var width = 500;
        var height = 300;
        var svg = d3.select("#svgcontainer")
           .append("svg").attr("width", width).attr("height", height);

        const data = [{x: 0, y: 10}, {x: 100, y: 75}, {x: 300, y: 90}, {x: 350, y: 20}]

        // Prepare a helper function
        const line = d3.line()
          .x(d => d.x)
          .y(d => d.y)
          .curve(d3.curveStep);

        // Add the <path> to the <svg> container using the helper function
        d3.select('svg').append('path')
            .attr('d', line(data))
            .attr('stroke', 'red')
            .attr('fill', 'none');
      </script>
   </body>
</html>
```

[Compare different curve interpolation types interactively here.](https://gist.github.com/d3indepth/b6d4845973089bc1012dec1674d3aff8)

Below image is an example by using a line and area generator. You can look at the full source code on [codesandbox](https://codesandbox.io/s/weathered-sunset-bznyp0?file=/index.html). We will extend this visualization in later tutorials by adding interactive tooltips and filters.

[![snp](https://i.im.ge/2023/02/26/7qMTu6.snp.md.png)](https://im.ge/i/7qMTu6)

Sources:
- https://www.w3schools.com/graphics/svg_path.asp
- https://github.com/UBC-InfoVis/447-materials/tree/22Jan/tutorials/2_D3_Tutorial_Making_Chart
- https://gist.github.com/akirap3/84e5d55c85b224466f44a35297d42a4b

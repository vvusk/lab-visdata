# Modul 5 Visualisasi Data

## Data Joins and Basic Interactivity

In this tutorial, we introduce the enter-update-exit pattern which is a key concept to make D3 visualizations fully interactive.

### Enter, Update, Exit

By now you have learned how to load external data and how to map it to visual elements, for example, to create a bar chart. But very often we have to deal with changing data or a continuous data stream rather than a static CSV file. Dynamic data often requires more sophisticated user interfaces that allow users to interact with the data (e.g., filter and sort).

**Instead of removing and redrawing visualizations each time new data arrives, we want to update only affected components to improve loading times and create smooth transitions.**

We will accomplish this by using D3's enter-update-exit pattern.

#### "Updating data" means "joining data"

A data-join is followed by operations on the three virtual selections: **enter**, **update** and **exit**.

This means that we are merging new data with existing elements. In the merging process we have to consider:
- Enter: What happens to new data values without existing, associated DOM elements?
- Update: What happens to existing elements which have changed?
- Exit: What happens to existing DOM elements which are not associated with data anymore?

[![enter_update_exit_sketch](https://i.im.ge/2023/03/04/7jhZ6Y.enter-update-exit-sketch.jpg)](https://im.ge/i/7jhZ6Y)

To take care of the enter-update-exit pattern, we have to change the sequence of our D3 code a little bit. Instead of chaining everything together, some code snippets must be separated.

We create an SVG element and select it in D3:

`const svg = d3.select('svg');`

And bind the data to SVG circles:

`let circle = svg.selectAll('circle')
    .data([5, 10, 15]);`
    
The length of the dataset is 3 and we select all SVG circles in the document. That means, if there are 3 or more existing circles, the **enter selection** is empty, otherwise it contains placeholders for the missing elements.

The page is empty because we have not appended any circles yet. We can access the **enter selection** and append a new circle for each placeholder with the following statement:

`circle = circle.enter().append('circle')
    .attr('r', d => d)
    .attr('cx', (d,index) => (index * 80) + 50)
    .attr('cy', 80);`
    
(You might have noticed that we've actually already used this pattern multiple times.)

But often you want to do the exact opposite operation. If someone filters the dataset you may want to remove existing elements. In this case, you have to use the `exit` selection. `exit` contains the leftover elements for which there is no corresponding data anymore.

We call the drawing function again with new data:

`circle = svg.selectAll('circle')
    .data([20, 30]);`

The new dataset contains 2 elements but on the website there are currently 3 circles. We can access the exit selection and remove the element that has no data-binding anymore:

`circle.exit().remove();`

There is still one problem left: *dynamic properties*. We are using a data-dependent radius and the values in the new dataset have been changed. For this reason, we have to update the dynamic properties (that we previously set in the *enter selection*) every time we update the data. To do this we use the *merge* function to apply changes to the *enter and update selection*:

`circle = svg.selectAll('circle')
	.data(data);`
    
The result of the `data()` method returns the updated selection.

Putting everything together:


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

        // Call rendering function with 2 datasets sequentially
        updateChart([5, 10, 15]);
        updateChart([20, 30]);

        function updateChart(data) {
          // Data-join (circle now contains the update selection)
          let circle = svg.selectAll('circle')
              .data(data);

          // Enter (initialize the newly added elements)
          let circleEnter = circle.enter().append('circle')
              .attr('fill', '#707086')

          // Enter and Update (set the dynamic properties of the elements)
          circleEnter.merge(circle)
              .attr('r', d => d)
              .attr('cx', (d,index) => (index * 80) + 50)
              .attr('cy', 80);

          // Exit
          circle.exit().remove();
        }
            
      </script>
   </body>
</html>
```

Hasilnya:

[![update-pattern](https://i.im.ge/2023/02/27/7qedTK.update-pattern.md.png)](https://im.ge/i/7qedTK)


#### Key function

For the sake of clarity and simplicity, we have not mentioned an important detail - the *key function* - in the last example.

**The key function defines which datum should be assigned to which element.**

`let circle = svg.selectAll("circle")
	.data([5, 10, 15]);`
    
The code .selectAll("circle") selects all circle-elements and if we chain it with .data([5, 10, 15]) we are joining the given data with the selected circles. The default key function applies and the keys are assigned by index. In our example it will use the first three circles that it finds. The first datum (first item in our array) and the first circle have the key "0", the second datum and circle have the key "1", and so on.

Assume, that we have implemeted the "enter, update, exit"-pattern and appended the three circles to the webpage.

We can now start the pipeline again, with a slightly different array:

`let circle = svg.selectAll("circle")
	.data([10, 15]);`

[![key-function-1](https://i.im.ge/2023/03/04/7jhGNr.key-function-1.png)](https://im.ge/i/7jhGNr)

The index will be used again as the default key to match the new data to the actual circles. There are three circles on the webpage and two items in the new dataset. Therefore, the last circle will be removed and the other two circles will be bound to the new data.

This is the simplest method of joining data and often sufficient. However, when the data and the elements are not in the same order, joining by index is insufficient. In this case, you can specify a key function as the second argument (callback function). The key function returns the key for a given datum or element:

`// use the actual data value as key function
let circle = svg.selectAll("circle")
	.data([5, 10, 15], d => d) // could be also d => d.customer_id if d is an object`

`// enter, update, exit`

`circle = svg.selectAll("circle")
	.data([10, 15], d => d)`

`// enter, update, exit`

In the above example, the key function allows us to map the data value directly instead of the default by-index behavior:

[![key-function-2](https://i.im.ge/2023/03/04/7jhzgq.key-function-2.png)](https://im.ge/i/7jhzgq)

This means, we can update the appropriate elements without having to delete and re-add elements. We can update them in place!

*"The key function also determines the enter and exit selections: the new data for which there is no corresponding key in the old data become the enter selection, and the old data for which there is no corresponding key in the new data become the exit selection. The remaining data become the default update selection." - Mike Bostock*

You can easily adapt the enter, update, exit sequence for any other visualizations. Using the D3 enter-update-exit pattern, code is more flexible and can accomodate changing data as well as different sized datasets.

### `join()` shortcut for the enter-update-exit pattern

Since D3 version 5, you can alternatively use the `join()` method which is simpler and more convenient for many cases. Instead of specifying enter, update, and exit operations separately, `join()` handles all three stages automatically. New elements will be added, existing elements will be updated, and obsolete elements will be removed.

The *updateChart* function from the previous example can be shortened to:


```python
function updateChart(data) {
  svg.selectAll('circle')
      .data(data)
    .join('circle')
      .attr('fill', '#707086')
      .attr('r', d => d)
      .attr('cx', (d,index) => (index * 80) + 50)
      .attr('cy', 80);
}
```

Nevertheless, sometimes it is important to have fine grained control over the enter, update, and exit selections, and a thorough understanding of this core mechanism in D3 is crucial.

You can also extend the `join()` statement to control what happens on enter, update and exit. Read more about this method and see an example: https://observablehq.com/@d3/selection-join

### Updating Scales and Axes

Whenever you get new data or existing data changes, you need to recalibrate your scales, otherwise elements will get clipped, or the visualization will show the wrong information. Appending axes multiple times will lead to overlapping tick labels and make it unreadable.

In the last tutorial, you learned how to create basic scales:

`vis.yScale = d3.scaleLinear()
	.domain([0, d3.max(vis.data, d => d.price)])
	.range([0, vis.height]);`
    
When the data changes, the **range** does not have to be updated, because the visual size of your chart usually does not change. You do need to update the **domain**, though, because the minimum and maximum of the data might change.

We can use a similar principle for updating axes. We initialize an axis and create an SVG group only once. Every time we update the chart, we use the .call() function to apply the correct scale to the axis and rerender it within the given SVG group.

The following class structure indicates when scales and axis are initialized, and when they need to be updated.


```python
class MyChart {
    constructor(_config, data) {
        // ... Class constructor ...
        this.initVis();
    }

    initVis() {
        let vis = this;

        // ... Other intialization code ...

        // Initialize scale
        vis.yScale = d3.scaleLinear()
            .range([0, vis.height]);

        // Initialize axis
        vis.yAxis = d3.axisLeft()
            .scale(vis.yScale);

       // Append axis group
       vis.yAxisGroup = vis.chart.append('g')
            .attr('class', 'y-axis axis');
    }

    updateVis() {
        let vis = this;

        // ... Other code to prepare the data etc. ...

        // Update scale domain
        vis.yScale.domain([0, d3.max(vis.data, d => d.price)]);
    }

    renderVis() {
        let vis = this;

        // ... Other code to render chart ...

        // Update the axis: guarantees that the axis component uses the correct scale
        // (adjusted to match the new input domain)
        vis.yAxisGroup.call(vis.yAxis);
    }
}
```

### Enter, Update, Exit with Nested Elements

As you might have noticed, a useful tip to re-arranging svg elements is wrapping them in `<g>` group tags. This helps better organize the structure of the elements in our chart, and allows us to group and move elements together. When working with nested elements inside groups, we need to apply the enter-update pattern to nested elements.

Nested and hierarchical elements often follow hierarchical data, like matrices. In many cases, we have to transform our flat data into a hierarchical form. D3 has built in functions for this, such as [d3.rollup()](https://github.com/d3/d3-array/blob/main/README.md#rollup), or [d3.groups()](https://github.com/d3/d3-array/blob/main/README.md#group).

Let's say you have a table of fruits:


```python
let fruit = [
    {"type": "grape", "amount": 100, "color": "green"},
    {"type": "kiwi", "amount": 15, "color": "green"},
    {"type": "banana", "amount": 7, "color": "yellow"},
    {"type": "mango", "amount": 3, "color": "yellow"},
    {"type": "strawberry", "amount": 31, "color": "red"},
    {"type": "papaya", "amount": 1, "color": "yellow"},
    {"type": "blueberry", "amount": 100, "color": "blue"}
];
```

In order to group this by color of fruit, we can apply the groups function:


```python
let result = d3.groups(fruit, d => d.color);

// Result from d3.groups
result = [
    ["green", [
            {"type": "grape", "amount": 100, "color": "green"},
            {"type": "kiwi", "amount": 15, "color": "green"}
        ]
    ],
    ["yellow", [
            {"type": "banana", "amount": 7, "color": "yellow"},
            {"type": "mango", "amount": 3, "color": "yellow"},
            {"type": "papaya", "amount": 1, "color": "yellow"}
        ]
    ],
    ["red", [
            {"type": "strawberry", "amount": 31, "color": "red"}
        ]
    ],
    ["blue", [
            {"type": "blueberry", "amount": 100, "color": "blue"}
        ]
    ]
];
```

`Rollups` and `groups` are particularly useful for transforming flat data into groups of maps or arrays.

With our data in a hierarchical format (i.e. a matrix, an array of arrays), we can create a nested selection. To join the colors to corresponding svg elements, first we join the outer array to the rows, then join the inner arrays to cells:


```python
// Generate 1 row for each fruit color
let group = svg.selectAll('.row')
                .data(result)
                .append('g')
                .attr('class', 'row');

// Generate 1 circle for each fruit
let fruits = group.selectAll('.fruit')
                .data(d => d[1], d => d.type) // Get the array of objects and use the type as the key function
                .append('circle')
                .attr('class', 'fruit')
                // Other code to generate rows based off of a categorical y-scale

```

The resulting selection will have a hierarchical structure that looks like this: 

[![fruit-nest](https://i.im.ge/2023/02/27/7sFHiy.fruit-nest.png)](https://im.ge/i/7sFHiy)

We use the nested approach for below heatmap chart. The complete code for the heatmap can be viewed [here](https://codesandbox.io/s/relaxed-dan-9s1w06?file=/index.html).

[![contoh-heatmap](https://i.im.ge/2023/02/27/7sHmR1.contoh-heatmap.md.png)](https://im.ge/i/7sHmR1)

Here comes another more complex example. The complete code for the timeline heatmap can be viewed [here](https://codesandbox.io/s/friendly-darkness-bekup5?file=/index.html).

[![contoh-timelineheatmap](https://i.im.ge/2023/02/27/7spv2T.contoh-timelineheatmap.md.png)](https://im.ge/i/7spv2T)

### LATIHAN 1

Latihan berikut ini berkaitan dengan pola enter-update pada visualisasi data menggunakan D3. Data untuk latihan ini berupa estimasi pola konsumsi alkohol pada setiap negara bagian di Amerika Serikat tahun 2012.

Kode untuk interaksi dan animasi sudah disediakan pada template. 
Download template [here](https://drive.google.com/file/d/15dOD6-MxN0bKlwcSNccc_tcy2w5hEW8A/view?usp=sharing). Template tersebut sudah memiliki data serta tampilan dasar. Anda perlu menambahkan kode yang sesuai (pada bagian yang ada tulisan TO DO di sini) agar tampilan akhirnya seperti gambar di bawah. Anda bisa berpedoman pada contoh Barchart yang ada di modul-4 (skala hampir sama hanya bertukar antara x dan y).

[![join-activity-1](https://i.im.ge/2023/03/04/7jh9cL.join-activity-1.md.png)](https://im.ge/i/7jh9cL)

- Gunakan D3 untuk membaca file CSV atau array.
- Siapkan data: konversi semua nilai numerik ke number.
- Pada class Barchart yang telah disediakan, inisialisasi skala dan sumbu X,Y yaitu:
    - xScale
    - yScale
    - xAxis
    - yAxis
- Ubah sumbu X dan Y pada method updateVis.


Sources:
- https://www.w3schools.com/graphics/svg_path.asp
- https://github.com/UBC-InfoVis/447-materials/tree/22Jan/tutorials/2_D3_Tutorial_Making_Chart
- https://gist.github.com/akirap3/84e5d55c85b224466f44a35297d42a4b
- https://bost.ocks.org/mike/nest/
- http://using-d3js.com/index.html

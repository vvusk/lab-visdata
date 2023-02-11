# Modul 2 Visualisasi Data

## D3 is Data Driven
D3.js itself is data-driven, which means it gets its super powers from data. D3 supports different types of data like arrays, CSV, XML, TSV, JSON, and so on. This data can come from a local file in your working directory or can be fetched from an API.

## Data Join in D3
Data join is another important concept in D3.js. It works along with selections and enables us to manipulate the HTML document with respect to our data set (a series of numerical values). Data join enables us to inject, modify and remove elements (HTML element as well as embedded SVG elements) based on the data set in the existing HTML document. By default, each data item in the data set corresponds to an element (graphical) in the document.

As the data set changes, the corresponding element can also be manipulated easily. Data join creates a close relationship between our data and graphical elements of the document. Data join makes manipulation of the elements based on the data set a very simple and easy process.

## How Data Join Works?
The primary purpose of the Data join is to map the elements of the existing document with the given data set. It creates a virtual representation of the document with respect to the given data set and provides methods to work with the virtual representation. Let us consider a simple data set as shown below.

`[10, 20, 30, 25, 15]`

The data set has five items and so, it can be mapped to five elements of the document. Let us map it to the li element of the following document using the selector's selectAll() method and data join's data() method.


```python
<ul id = "list">
   <li><li>
   <li></li>
</ul> 

<script>
d3.select("#list").selectAll("li").data([10, 20, 30, 25, 15]);
</script>
```

We can use all the selector's element modifying methods like attr(), style(), text(), etc., for the first two li as shown below.


```python
d3.select("#list").selectAll("li")
   .data([10, 20, 30, 25, 15])
   .text(function(d) { return d; });
```

The function in the text() method above is used to get the li elements mapped data. Here, d represent 10 for first li element and 20 for second li element. Type below code and try run in your browser.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <ul id = "list">
            <li></li>
            <li></li>
        </ul> 
      
      <script>
         d3.select("#list").selectAll("li")
           .data([10, 20, 30, 25, 15])
           .text(function(d) { return d; });
      </script>
   </body>
</html>
```

D3 just assigns the first two items in our array to the only selection li it got and forgets about the rest.
After checking the previous coding result, try adding one more li element.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <ul id = "list">
            <li></li>
            <li></li>
            <li></li>
        </ul> 
      
      <script>
         d3.select("#list").selectAll("li")
           .data([10, 20, 30, 25, 15])
           .text(function(d) { return d; });
      </script>
   </body>
</html>
```

Observe the difference. 
A quick fix for this is to manually create the other 2 li elements. But most of the time we don't actually know how many items are in our array of data that is fetched from an external API.

To solve this problem, the latest versions of D3 provides us with a **.join()** method. It appends, removes, and reorders elements as necessary to match the specified data. Let's try it with our previous example to see what happens:


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <ul id = "list" class = "clist">
            <li></li>
        </ul> 
      
      <script>
        let num = [10, 20, 30, 25, 15]
        
        d3.select(".clist")
            .selectAll("li")
            .data(num)
            .join("li") // the join method
                .attr("class", "clist")
                .text((d) => d)
      </script>
   </body>
</html>
```

The explanation for previous code:
- Select the li wrapper clist
- Select all the li elements, if none this returns an empty selection
- .data(num) - Binds the num array to the empty selection
- .join("li") - This methods creates all the li elements for each item in our Array
- .attr("class", "clist") - We set a class for each li element that was created
- .text((d) => d) - Sets the text of each created li based on the num Array

Othey way to solve the problem is by mapping the next two elements to any elements and it can be done using the data join's **enter()** and selector's **append()** method. The **enter()** method gives access to the remaining data (which is not mapped to the existing elements) and the **append()** method is used to create a new element from the corresponding data. Try below code and observe the result in your browser.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <ul id = "list">
            <li></li>
            <li></li>
            <li></li>
        </ul> 
      
      <script>
         d3.select("#list").selectAll("li")
           .data([10, 20, 30, 25, 15])
           .text(function(d) { return "This is pre-existing element and the value is " + d; })
           .enter()
           .append("li")
           .text(function(d) 
              { return "This is dynamically created element and the value is " + d; });
      </script>
   </body>
</html>
```

Data join provides another method called as the **exit()** method to process the data items removed dynamically from the data set as shown below.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <ul id = "list">
            <li></li>
            <li></li>
            <li></li>
        </ul> 
        
        <input type = "button" name = "remove" value = "Remove fifth value" 
         onclick = "javascript:remove()" />
      
      <script>
         d3.select("#list").selectAll("li")
            .data([10, 20, 30, 25, 15])
            .text(function(d) 
               { return "This is pre-existing element and the value is " + d; })
            .enter()
            .append("li")
            .text(function(d) 
               { return "This is dynamically created element and the value is " + d; });
             
         function remove() {
            d3.selectAll("li")
            .data([10, 20, 30, 15])
            .exit()
            .remove()
         }
      </script>
   </body>
</html>
```

### Dynamic Properties

If you want access to the corresponding values from the dataset, you have to use anonymous functions. For example, we can include such a function inside the text() operator:

`// Our preferred option: ES6 arrow function syntax
.text(d => d);

// Alternative: Traditional function syntax
.text( function(d) { return d; } );
`


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      
      <script>
         const provinces = ['AB', 'BC', 'MB', 'NB', 'NL', 'NT', 'NS', 'NU', 'ON', 'PE', 'QC', 'SK', 'YT'];

        const p = d3.select('body').selectAll('p')
            .data(provinces)
            .enter()
            .append('p')
            .text(d => d);
      </script>
   </body>
</html>
```

In comparison, an ordinary JS function looks like the following code below. It has a function name, an input and an output variable. If the function name is missing, then it is called an anonymous function.

`function doSomething(d) {
	return d;
}`

If you want to use the function only in one place, an anonymous function is more concise than declaring a function and then doing something with it in two separate steps. We will use this JS concept very often in D3 to access individual values and to create interactive properties.

`.text(d => d.firstName);`

In the previous example we have used the function to access individual values of the loaded array. That is one feature of D3: It can pass array elements and corresponding data indices to an anonymous function, which is called for each array element individually.

In the D3 documentation and most tutorials, you'll generally see the parameter d used for the current data value and i (or index) used for the index of the current data element. The index is passed as a second parameter in function calls and is optional.

It is still a regular function, so it doesn't have to be a simple return statement. We can use if-statements, for-loops, console message, and we can also access the index of the current element in our selection.

Example for an anonymous function that passes the data value and index:


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      
      <script>
         const provinces = ['AB', 'BC', 'MB', 'NB', 'NL', 'NT', 'NS', 'NU', 'ON', 'PE', 'QC', 'SK', 'YT'];

        const p = d3.select('body').selectAll('p')
            .data(provinces)
            .enter()
            .append('p')
            .text((d, index) => {
                console.log(index); // Debug variable
                return `element: ${d} at position: ${index}`;
            });
      </script>
   </body>
</html>
```

### HTML attributes and CSS properties

Data join not only update the data in the DOM elements, but we can get and set different properties and styles. This becomes very important when working with SVG elements.

In below code, we use D3 to set the paragraph content, the HTML class, the font-weight and as the last property, the font colour which depends on the individual array value.

If you want to assign specific styles to the whole selection (e.g., font weight), we recommend you to define an HTML class (`custom-paragraph` in below example) and add these rules to an external CSS file. Using classes and CSS styles will make your code more concise and reusable.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        
      
      <script>
         const provinces = ['AB', 'BC', 'MB', 'NB', 'NL', 'NT', 'NS', 'NU', 'ON', 'PE', 'QC', 'SK', 'YT'];

        // Append paragraphs and highlight one element
        let p = d3.select('body').selectAll('p')
            .data(provinces)
            .enter()
          .append('p')
            .text(d => d)
            .attr('class', 'custom-paragraph')
            .style('font-weight', 'bold')
            .style('color', d => {
              if(d == 'NB')
                return 'blue';
              else
                return 'red';
            });
      </script>
   </body>
</html>
```

## Scalable Vector Graphics (SVG)

SVG stands for Scalable Vector Graphics. SVG is an XML-based vector graphics format. It provides options to draw different shapes such as Lines, Rectangles, Circles, Ellipses, etc. Hence, designing visualizations with SVG gives you more power and flexibility.

We will use D3 to bind data values to visual marks and channels on a web page. D3's default rendering platform is SVG (Scalable Vector Graphics) that we will use throughout this course. D3 also supports HTML Canvas which is useful for displaying a larger number of objects but has limited flexibility.

Here are some key facts about SVG:
- SVG is defined using markup code similar to HTML.
- SVG is a vector based image format and it is text-based. SVG elements don't lose any quality when they are resized.
- SVG elements can be included directly within any HTML document or dynamically inserted into the DOM with JS.
- Before you can draw SVG elements, you have to add an `<svg>` element with a specific width and height to your HTML document, for example: `<svg width="500" height="500"></svg> `.
- The SVG coordinate system places the origin `(0/0)` in the top-left corner of the svg element.
- SVG has no layering concept or depth property. The order in which elements are coded determines their depth order.
- SVG properties can be specified as attributes.

Basic shape elements in SVG: `rect`, `circle`, `ellipse`, `line`, `text`, and `path`.

Below is the example to display some SVG shapes in HTML document. First, we have to create a SVG image and set width as 400 pixel and height as 50 pixel. The default unit of the SVG format is pixel.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
        <svg width="400" height="50">

            <!-- Rectangle (x and y specify the coordinates of the upper-left corner -->
            <rect x="0" y="0" width="50" height="50" fill="blue" />

            <!-- Circle: cx and cy specify the coordinates of the center and r the radius -->
            <circle cx="85" cy="25" r="25" fill="green" />

            <!-- Ellipse: rx and ry specify separate radius values -->
            <ellipse cx="145" cy="25" rx="15" ry="25" fill="purple" />

            <!-- Line: x1,y1 and x2,y2 specify the coordinates of the ends of the line -->
            <line x1="185" y1="5" x2="230" y2="40" stroke="gray" stroke-width="5" />

            <!-- Text: x specifies the position of the left edge and y specifies the vertical position of the baseline -->
            <text x="260" y="25" fill="red">SVG Text</text>

        </svg>
      
      <script>
        <style>
            body { font-family: Arial; }
        </style>
      </script>
   </body>
</html>
```

Now we try to make svg image by using D3.

**Step 1** − Create a container to hold the SVG image, for example: `<div id = "svgcontainer"></div>`

**Step 2** − Select the SVG container using the select() method and inject the SVG element using the append() method. Add the attributes and styles using the attr() and the style() methods.
`var width = 300;
var height = 300;
var svg = d3.select("#svgcontainer")
.append("svg").attr("width", width).attr("height", height);`

**Step 3** − Similarly, add the line element inside the svg element as shown below.
`svg.append("line")
   .attr("x1", 100)
   .attr("y1", 100)
   .attr("x2", 200) 
   .attr("y2", 200)
   .style("stroke", "rgb(255,0,0)")
   .style("stroke-width", 2);`
   
The complete code is as follows.


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
        var width = 300;
        var height = 300;
        var svg = d3.select("#svgcontainer")
           .append("svg").attr("width", width).attr("height", height);
            
        svg.append("line")
           .attr("x1", 100)
           .attr("y1", 100)
           .attr("x2", 200) 
           .attr("y2", 200)
           .style("stroke", "rgb(255,0,0)")
           .style("stroke-width", 2);
      </script>
   </body>
</html>
```

Let's take a look another example below.

It is crucial to set the SVG coordinates of visual elements. If we don't set the x and y values, all the rectangles will be drawn at the same position at (0, 0). By using the index, of the current element in the selection, we can create a dynamic x property and shift every newly created rectangle 60 pixels to the right.


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
        const numericData = [1, 2, 4, 8, 16];
        
        var width = 300;
        var height = 300;
        var svg = d3.select("#svgcontainer")
           .append("svg").attr("width", width).attr("height", height);
            
        svg.selectAll('rect')
            .data(numericData)
            .enter()
          .append('rect')
            .attr('fill', 'red')
            .attr('width', 50)
            .attr('height', 50)
            .attr('y', 0)
            .attr('x', (d, index) => index * 60);
      </script>
   </body>
</html>
```

source:
- https://www.tutorialspoint.com/d3js/
- https://github.com/UBC-InfoVis/447-materials/tree/22Jan/tutorials/1_D3_Tutorial_Intro
- https://gist.github.com/akirap3/84e5d55c85b224466f44a35297d42a4b


### LATIHAN 1

1. Buat satu projek D3 yang baru dan berisi folder js dan css. Contoh [template folder](https://drive.google.com/file/d/1yFRoKT5ihVnVKHwUBkxopGNEEDIzXzxy/view?usp=sharing).
2. Append elemen SVG ke dalam dokumen HTML dengan D3 (lebar: 500px, tinggi: 500px)
3. Buat lingkaran dengan D3.
4. Append unsur lingkaran SVG untuk setiap objek yang ada dalam array berikut ini:

`const sandwiches = [
	 { name: "Thesis", price: 7.95, size: "large" },
	 { name: "Dissertation", price: 8.95, size: "large" },
	 { name: "Highlander", price: 6.50, size: "small" },
	 { name: "Just Tuna", price: 6.50, size: "small" },
	 { name: "So-La", price: 7.95, size: "large" },
	 { name: "Special", price: 12.50, size: "small" }
];`

- Buat properties yang dinamis:
    - koordinat x dan y sehingga lingkaran tidak saling overlap
    - radius, ukuran large haruslah 2 kali lipat ukuran small
    - warna, gunakan 2 warna yang berbeda. Salah satu warna (fill) digunakan untuk produk berharga < 7.00 USD, warna lainnya untuk produk dengan harga >= 7.00 USD
    - beri border untuk setiap lingkaran (SVG property: stroke)

[![exc1](https://i.im.ge/2023/02/09/a6iZxY.exc1.png)](https://im.ge/i/a6iZxY)

Hasil akhirnya lebih kurang seperti di atas. Klik gambar di atas agar muncul tampilan penuhnya.



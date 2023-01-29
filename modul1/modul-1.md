# Modul 1 Visualisasi Data

D3 stands for Data-Driven Documents. D3.js is a JavaScript library for manipulating documents based on data. D3.js is a dynamic, interactive, online data visualizations framework used in a large number of websites. D3.js is written by Mike Bostock, created as a successor to an earlier visualization toolkit called Protovis. This tutorial will give you a complete knowledge on D3.jsframework. This is an introductory tutorial, which covers the basics of Data-Driven Documents and explains how to deal with its various components and sub-components.

Data visualization is the presentation of data in a pictorial or graphical format. The primary goal of data visualization is to communicate information clearly and efficiently via statistical graphics, plots and information graphics.

Data visualization helps us to communicate our insights quickly and effectively. Any type of data, which is represented by a visualization allows users to compare the data, generate analytic reports, understand patterns and thus helps them to take the decision. Data visualizations can be interactive, so that users analyze specific data in the chart. Well, Data visualizations can be developed and integrated in regular websites and even mobile applications using different JavaScript frameworks.

## What is D3.js?

D3.js is a JavaScript library used to create interactive visualizations in the browser. The D3.js library allows us to manipulate elements of a webpage in the context of a data set. These elements can be HTML, SVG, or Canvas elements and can be introduced, removed, or edited according to the contents of the data set. It is a library for manipulating the DOM objects. D3.js can be a valuable aid in data exploration, it gives you control over your data's representation and lets you add interactivity.

## Why Do We Need D3.js?

D3.js is one of the premier framework when compare to other libraries. This is because it works on the web and its data visualizations are par excellence. Another reason it has worked so well is owing to its flexibility. Since it works seamlessly with the existing web technologies and can manipulate any part of the document object model, it is as flexible as the Client Side Web Technology Stack (HTML, CSS, and SVG). It has a great community support and is easier to learn.

## D3.js Features

D3.js is one of the best data visualization framework and it can be used to generate simple as well as complex visualizations along with user interaction and transition effects. Some of its salient features are listed below: 
- Extremely flexible.
- Easy to use and fast.
- Supports large datasets.
- Declarative programming.
- Code reusability.
- Has wide variety of curve generating functions.
- Associates data to an element or group of elements in the html page.

## D3.js Benefits
D3.js is an open source project and works without any plugin. It requires very less code and comes up with the following benefits: 
- Great data visualization.
- It is modular. You can download a small piece of D3.js, which you want to use. No need to load the whole library every time.
- Easy to build a charting component.
- DOM manipulation.

## What do you need for this laboratory?

You need to prepare:
- D3.js library
- Editor (you can use Notepad++, Visual studio code, Eclipse, Sublime or other editor you preferred)
- Web browser
- Web server (for Windows, you can use Xampp)

You can use D3.js library in various ways.

1st way.
For D3.js library, go to this website: https://d3js.org/ then download the library. The latest version is 7.8.2.
Create a folder in your web server working directory. For example, you can name it 'PrakVD'. Then inside that folder, create a 'js' folder. This is to better organize your javascript working files.
Unzip the D3 downloaded file then put the d3.min.js file into folder js that you created previously. I renamed the d3.min.js to d3.v7.min.js to differentiate which d3 version we're working on.
An example of working directory
`prakVD/
	index.html
	css/
		style.css
	js/
		d3.min.js
		main.js`

2nd way.
Link directly to the latest release
`    <script src="https://d3js.org/d3.v7.min.js"></script>`





```python
#How to use it:
<!DOCTYPE html>
<html lang = "en">
   <head>
      <script src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <script>
         // write your d3 code here.. 
      </script>
   </body>
</html>
```

## A little bit of concept

D3.js is an open source JavaScript library for:
- Data-driven manipulation of the Document Object Model (DOM).
- Working with data and shapes.
- Laying out visual elements for linear, hierarchical, network and geographic data.
- Enabling smooth transitions between user interface (UI) states.
- Enabling effective user interaction.

We need to be familiar with some basic concepts required for further D3 knowledge.

### HyperText Markup Language (HTML)

HTML is used to structure the content of the webpage. It is stored in a text file with the extension “.html”. This is the minimal knowledge for you to create a simple website.


```python
<!DOCTYPE html>
<html>
   <head>
      <title>My Document</title>
   </head>

   <body>
      <div>
         <h1>Greeting</h1>
         <p>Hello World!</p>
      </div>
   </body>
</html>
```

### Document Object Model (DOM)

When a HTML page is loaded by a browser, it is converted to a hierarchical structure. Every tag in HTML is converted to an element / object in the DOM with a parent-child hierarchy. It makes our HTML more logically structured. Once the DOM is formed, it becomes easier to manipulate (add/modify/remove) the elements on the page. 

The document object model of the above HTML document is as follows:

[![document_object_model](https://i.im.ge/2023/01/29/sGvwRp.document-object-model.md.png)](https://im.ge/i/sGvwRp)


### Cascading Style Sheets (CSS)

While HTML gives a structure to the webpage, CSS styles makes the webpage more pleasant to look at. CSS is a Style Sheet Language used to describe the presentation of a document written in HTML or XML (including XML dialects like SVG or XHTML). CSS describes how elements should be rendered on a webpage.

### JavaScript

JavaScript is a loosely typed client side scripting language that executes in the user's browser. JavaScript interacts with HTML elements (DOM elements) in order to make the web user interface interactive. JavaScript knowledge is a prerequisite for D3.js.

### Scalable Vector Graphics (SVG)

SVG is a way to render images on the webpage. SVG is not a direct image, but is just a way to create images using text. As its name suggests, it is a Scalable Vector. It scales itself according to the size of the browser, so resizing your browser will not distort the image. All browsers support SVG except IE 8 and below. Data visualizations are visual representations and it is convenient to use SVG to render visualizations using the D3.js.


```python
# Save this code in your web server working directory. You can use whatever filename-you-want.html. 
# Then test running it in your browser, for example http://localhost/visdata/tes.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Testing SVG</title>
    <!--<link rel="stylesheet" href="css/style.css">-->
</head>
<body>
    <script src="js/d3.v7.min.js"></script>
    <!--<script src="js/main.js"></script>-->

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
</body>
</html>
```

## How to Select Elements in D3

Selections is one of the core concepts in D3.js. It is based on CSS selectors. It allows us to select one or more elements in a webpage. In addition, it allows us to modify, append, or remove elements in a relation to the pre-defined dataset. In this chapter, we will see how to use selections to create data visualizations.

D3.js helps to select elements from the HTML page using the following two methods:
- select() − Selects only one DOM element by matching the given CSS selector. If there are more than one elements for the given CSS selector, it selects the **first one** only.
- selectAll() − Selects all DOM elements by matching the given CSS selector. If you are familiar with selecting elements with jQuery, D3.js selectors are almost the same.

The select() method selects the HTML element based on CSS Selectors. In CSS Selectors, you can define and access HTML-elements in the following three ways:
- Tag of a HTML element (e.g. div, h1, p, span, etc.,)
- Class name of a HTML element
- ID of a HTML element

The selectAll() method is used to select multiple elements in the HTML document. The select method selects the first element, but the selectAll method selects all the elements that match the specific selector string. In case the selection matches none, then it returns an empty selection. 


```python
# Selection by Tag example
# Here, we're going to select the “div” tag element
# note: making comment in hmtl is <!-- -->
<!DOCTYPE html>
<html lang = "en">
   <head>
      <script src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div>
         Hello World!    
      </div>
      
      <script>
         d3.select("div").text();
      </script>
   </body>
</html>
```


```python
# Selection by Class name example

<!DOCTYPE html>
<html lang = "en">
   <head>
      <script src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div class = "myclass">
         Hello World!
      </div>
      
      <script>
         ad3.select(".myclass").text();
      </script>
   </body>
</html>
```


```python
# Selection by ID of HTML element example

<!DOCTYPE html>
<html lang = "en">
   <head>
      <script src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div id = "hello">
         Hello World!
      </div>
      
      <script>
         d3.select("#hello").text();
      </script>
   </body>
</html>
```


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
		<h2 class = "myclass">Message</h2>
      <div class = "myclass">
         Hello World!
      </div>
      
      <script>
         d3.selectAll(".myclass").attr("style", "color: red");
      </script>
   </body>
</html>
```

## Adding DOM Elements

The D3.js selection provides the append() and the text() methods to append new elements into the existing HTML documents. 

### The append() Method
The append() method appends a new element as the last child of the element in the current selection. This method can also modify the style of the elements, their attributes, properties, HTML and text content.



```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div class = "myclass">
         Hello World!
      </div>
      
      <script>
         d3.select("div.myclass").append("span").text("from D3.js");
      </script>
   </body>
</html>
```

The above script performs a chaining operation. D3.js smartly employs a technique called the chain syntax, which you may recognize from jQuery. By chaining methods together with periods, you can perform several actions in a single line of code. It is fast and easy. The same script can also access without chain syntax as shown below.

`var body = d3.select("div.myclass");
var span = body.append("span");
span.text("from D3.js");`


## Modifying Elements

D3.js provides various methods, html(), attr() and style() to modify the content and style of the selected elements. 

### The html() Method
The html() method is used to set the html content of the selected / appended elements.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div class = "myclass">
         Hello World!
      </div>
      
      <script>
         d3.select(".myclass").html("Hello World! <span>from D3.js</span>");
      </script>
   </body>
</html>
```

### The attr() Method
The attr() method is used to add or update the attribute of the selected elements.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div class = "myclass">
         Hello World!
      </div>
      
      <script>
         d3.select(".myclass").attr("style", "color: red");
      </script>
   </body>
</html>
```

### The style() Method
The style() method is used to set the style property of the selected elements.


```python
<!DOCTYPE html>
<html>
   <head>
      <script type = "text/javascript" src = "js/d3.v7.min.js"></script>
   </head>

   <body>
      <div class = "myclass">
         Hello World!
      </div>
      
      <script>
         d3.select(".myclass").style("color", "blue");
      </script>
   </body>
</html>
```

source:
- https://www.tutorialspoint.com/d3js/
- https://www.freecodecamp.org/news/d3js-tutorial-data-visualization-for-beginners/
- https://github.com/UBC-InfoVis/447-materials/tree/22Jan/tutorials/1_D3_Tutorial_Intro

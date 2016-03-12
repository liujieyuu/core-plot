The code-a-thon is split into 4 streams, which are in turn split into sub-projects and tasks. Each stream has a project lead.

# 1. Applications Development #

**Project Lead:** Brad Larson

Demo apps will show off the framework, and drive development by indicating where we need more work.

It would be good if the apps developed ended up being of release quality, and useful, because it would advertise the Core Plot framework. They needn't be complex apps, just basic plotting utilities.

The apps should include a few cool core animation tricks that show off what this framework can do that others can't.

## Subproject A: Mac App 'Drop Plot' ##
Drop any column delimited data onto the app, and have it plot automatically. User can change the plot type between line/scatter plot, bar plot, and pie chart.

It is important that the app looks great, and has some cool animations. In short, even though the app is simple, the user should get a kick out of using it. But they should also be able to do something useful with it (ie create a very quick graph of some data, and print or export it).

### Tasks ###
  1. Setup Xcode project
  1. Add parsing of delimited data. Handle delimitation by any mix of spaces, commas, semicolons, tabs. Use NSScanner.
  1. Use IB to add a main window, including a graph view and some way to switch between chart types
  1. Add code to handle a text data file dropped on the app icon or the main window.
  1. Add code to create charts. Include a build-in animation for each graph type.
  1. Add printing of graph.
  1. Add graph export as PDF or images (png, jpeg, tiff)
  1. Add drag-and-drop of graph as PDF/image into other apps.
  1. Add button with Core Plot icon somewhere. When pressed, takes user to Core Plot site.

## Subproject B: iPhone App 'AAPL Technicals' ##
Plot AAPL stock data, and include some basic technical analysis plots.

### Tasks ###
  1. Setup a single view 'widget-like' app
  1. Add three charts: one for price and Bollinger Bands; one for moving averages; one for trading volume.
  1. Write code to retrieve CSV stock data from Yahoo! Finance.
  1. Write classes to calculate financial technical indicators. These take the stock data as input, and produce a single value as output for any given day. The indicators should be 'Bollinger Band' and 'Simple Moving Average'. This code can be based on information at http://ta-lib.org/ and http://tadoc.org/.
  1. Adapt or subclass CPScatterPlot to draw Open-High-Low-Close (OHLC) plots.
  1. Setup the main graph to draw an OHLC plot of the price, and a high and low Bollinger plot.
  1. Setup a smaller graph under that to draw a histogram of trading volume.
  1. Setup a small graph under that to draw a short and long moving average.
  1. Add a control to change the time scale. Choose between 3 months, 6 months, 1 year, 2 years, and 5 years. This could be on the back of the graph layer, presented with a flip animation.
  1. Add button with Core Plot icon to back side. When pressed, takes user to Core Plot site.
  1. If time permits, this app could be extended to allow the user to enter their favorite apps (ala the Stocks app), and swipe between them.


# 2. Fundamentals #

**Project lead:** Drew McCormack

Core parts of the framework are not yet finished, and should be given a high priority. Once these are done, the framework will actually be useful to developers in their apps, even if the framework is not complete.

### Tasks ###

  1. Update CPPlotRange class to use NSDecimalNumber throughout. Fix things that break.
  1. Add a CPTextStyle class, similar to CPLineStyle. Should store the font name, size, color, shadow, etc. Refactor CPTextLayer to use this. Use it as a property of CPAxis for labelStyle.
  1. Add margin properties for the CPPlotArea class.
  1. Add data source method to CPScatterPlot that gets the plot symbol at a given record index, rather than the setSymbol:atIndex: method that is currently used.
  1. Add a continuous rectangular border to the CPXYAxisSet class. This can be hidden by setting the line style to nil. It can have rounded corners. User can set line style of individual axis' to nil to hide them.
  1. Add shadows to line styles and fills
  1. Add grid lines to CPPlotSpace class.
  1. Get dashed lines working in CPLineStyle class.
  1. Add logarithmic scales in CPPlotSpace and CPAxis
  1. Add an automatic axis labeling option, in addition to the existing ad hoc and fixed interval options. In the automatic option the user sets a target number of ticks on the axis, and the axis calculates a nice round number that approximately gives that number of ticks. Examples of how to do this can be found in the Narrative project (http://narrative.cvs.sourceforge.net/viewvc/narrative/narrative/) in the class NRTFloatRangeDiscretizer, and in the 'Rounding.zip' file in the Core Plot downloads section (http://code.google.com/p/core-plot/downloads/list).
  1. Refactor the CPTestApp project so that it can display many different examples in a single window. Each example plot should be setup in an NSViewController, and the user should be able to switch between the examples via a popup (or similar) in the main window.
  1. Add CPAnnotation class that can be used for labels and callouts. Should be capable of displaying text, an image, or a custom layer.
  1. Add an annotation to the CPGraph class for the graph title.
  1. Add an annotation to the CPAxis class for the axis title. And consider whether the annotation class should be used for the axis labels.
  1. Write a CPLegend class for representing legends on graphs. It should basically be a two column table of layers, with a description on one side, and plot style on the other. To get the plot style, the CPLegend class could temporarily change the data source of each CPPlot, or a method could be added to CPPlot to draw a swatch.
  1. Add options for using smoothed lines in CPScatterPlot (ie bezier paths). This is discussed in a document/post by Charles Parnot on the web site.


# 3. Graph Types #

**Project Lead:**

There are many different graph/plot types that will eventually need to be supported, but the following are the most common: line/scatter, pie charts, and bar/histogram plots. Only the line/scatter plot has been implemented to date.

More obscure plot types can also be undertaken if someone has a particular desire to take them on, eg, contour plots, image plots, error-bar/range plots.

### Tasks ###

  1. Implement a class (subclass of CPScatterPlot?) that shows time series, with dates/times on the x-axis. Introduce a CPXYGraph subclass that sets up axes etc for time series.
  1. Implement the CPBarPlot class. Support both data source and bindings. Use CPScatterPlot class as a template. Preferably support both horizontal and vertical plots. Allow multiple bar plots to be plotted on the same graph, with an offset that facilitates grouping.
  1. Implement a CPPiePlot class, as well as support classes (eg CPPolarGraph)
  1. Implement a CPContourPlot class. This could be based on CPScatterPlot, because a contour plot is just a scatter plot with some reduction of the 3D data to 2D contours.
  1. Implement a CPDiscreteImagePlot class. This shows a discrete rectangular grid of images, with a finite number of states at each point. Each state is represented by a symbol or image.
  1. Implement a CPImageMapPlot class. This shows 'height' at each point in grid by a color.
  1. Implement a CPRangePlot class. This shows a range in Y. A common example is error bars, but there are other possibilities.


# 4. Animations #

**Project Lead:**

One aspect of Core Plot that distinguishes it from other plotting frameworks is that animation is built-in from the beginning. By adopting the Core Animation framework, a lot of the heavy lifting has already been done. Nonetheless, the raw possibilities that Core Animation offers have to be packaged into easy to use animation classes, that make it trivial for developers to display beautiful and dynamic graphics.

### Tasks ###

  1. Finish the implementation of the incomplete CPAnimation, CPAnimationKeyFrame, and CPAnimationTransition classes.
  1. Add an option in CPAnimation to hide all layers initially. The animation should make them visible after giving the CPAnimationTransition an opportunity to setup the transtion (eg move layers offscreen).
  1. Implement the 'explosion' animation in the CPTestApp as a CPAnimationTransition subclass, and make use of the new class in the CPTestApp.
  1. Implement a 'build-in' type animation (CPAnimationTransition subclass) in which axis labels fly in and align along the axis.
  1. Implement a CPAnimationTransition subclass for fading in/out individual plots.
  1. Implement a CPAnimationTransition subclass for swiping in/out individual plots.
  1. Implement a CPAnimationTransition subclass for blurring plots (use layer filters)
  1. Implement a CPAnimation subclass that uses the blurring of plots transitions to focus on one plot at a time. (This type of animation would be useful in a presentation, for example, where you want the audience to focus on one plot line at a time.)

# 5. Testing #

**Project Lead:** Barry Wark

Automated testing to verify behavior and to prevent regressions are extremely important in a framework such as Core Plot. Because Core Plot is intended to be used in domains where correctness is a requirement (e.g. scientific, financial, medical, etc.), having confidence in the correctness of Core Plot output is a project goal. Testing helps us gain this confidence. Similarly, a framework that is used by many projects (we sure hope Core Plot will be used by lots of people) must protect against regressions being added as new features are developed. Automated testing again provides an other layer of security against adding regressions to the framework.  Although we have a strong testing infrastructure in Core Plot, the test coverage is not what we would like. We would appreciate help in writing tests or improving our test infrastructure.

### Tasks ###
  1. Add a coverage build to the Core Plot unit tests and automate generation of a coverage report for our unit tests.
  1. Write unit tests for untested code. In particular, the CPAxis`*` and CPAnimation`*` classes are currently very under tested.
  1. Add testing targets to the CorePlot-CocoaTouch project to run unit tests on the iPhone
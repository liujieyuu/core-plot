# Core Plot Design Overview #

This document describes the main classes of Core Plot, and how they work together.

## Design Considerations ##

Before delving into the classes that make up Core Plot, it is worth considering the design goals of the framework. Core Plot has been developed to run on both Mac OS X and iOS. This places some restrictions on the technologies that can be used: AppKit drawing is not possible, and view classes like NSView and UIView can only be used as host views. Drawing is instead performed using the low-level Quartz 2D API, and Core Animation layers are used to build up the various different aspects of a graph.

It's not all bad news, because utilizing Core Animation also opens up a whole range of possibilities for introducing 'eye-candy'. Graphs can be animated, with transitions and effects. The objective is to have Core Plot be capable of not only producing publication quality still images, but also stunning graphical effects and interactivity.

Another objective that is influential in the design of Core Plot is that it should behave as much as possible from a developer's perspective as a built-in framework. Design patterns and technologies used in Apple's own frameworks, such as the data source pattern, delegation, and bindings, are all supported in Core Plot.

## Anatomy of a Graph ##

This diagram shows a standard bar graph with two data sets plotted. Below, the chart has been annotated to show the various components of the the chart, and the naming scheme used in Core Plot to identify them.

![http://core-plot.googlecode.com/files/GraphAnatomy.png](http://core-plot.googlecode.com/files/GraphAnatomy.png)

## Class Diagram ##

This standard UML class diagram gives a static view of the main classes in the framework. The cardinality of relationships is given by a label, with a '1' indicating a to-one relationship, and an asterisk (`*`) representing a to-many relationship.

![http://core-plot.googlecode.com/files/ClassDiagram.png](http://core-plot.googlecode.com/files/ClassDiagram.png)


## Objects and Layers ##

This diagram shows run time relationships between objects (right) together with layers in the Core Animation layer tree (left). Color coding shows the correspondence between objects and their corresponding layers.

![http://core-plot.googlecode.com/files/ObjectAndLayerDiagram.png](http://core-plot.googlecode.com/files/ObjectAndLayerDiagram.png)



## Layers ##
Core Animation's layer class, CALayer, is not very suitable for producing vector images, as required for publication quality graphics, and provides no support for event handling. For these reasons, Core Plot layers derive from a class called CPTLayer, which itself is a subclass of CALayer. CPTLayer includes drawing methods that make it possible to produce high quality vector graphics, as well as event handling methods to facilitate interaction.

The drawing methods include

```
-(void)renderAsVectorInContext:(CGContextRef)context;
-(void)recursivelyRenderInContext:(CGContextRef)context;
-(NSData *)dataForPDFRepresentationOfLayer;
```

When subclassing CPTLayer, it is important that you don't just override the standard `drawInContext:` method, but instead override `renderAsVectorInContext:`. That way, the layer will draw properly when vector graphics are generated, as well as when drawn to the screen.

## Graphs ##

The central class of Core Plot is CPTGraph. In Core Plot, the term 'graph' refers to the complete diagram, which includes axes, labels, a title, and one or more plots (eg histogram, line plot). CPTGraph is an abstract class from which all graph classes derive.

A graph class is fundamentally a factory: It is responsible for creating the various objects that make up the graphic, and for setting up the appropriate relationships. The CPTGraph class holds references to objects of other high level classes, such as CPTAxisSet, CPTPlotArea, and CPTPlotSpace. It also keeps track of the plots (CPTPlot instances) that are displayed on the graph.

```
@interface CPTGraph : CPTBorderedLayer {
    @private
    CPTPlotAreaFrame *plotAreaFrame;
    NSMutableArray *plots;
    NSMutableArray *plotSpaces;
    NSString *title;
    CPTTextStyle *titleTextStyle;
    CPTRectAnchor titlePlotAreaFrameAnchor;
    CGPoint titleDisplacement;
    CPTLayerAnnotation *titleAnnotation;
    CPTLegend *legend;
    CPTLayerAnnotation *legendAnnotation;
    CPTRectAnchor legendAnchor;
    CGPoint legendDisplacement;
}

@property (nonatomic, readwrite, copy) NSString *title;
@property (nonatomic, readwrite, copy) CPTTextStyle *titleTextStyle;
@property (nonatomic, readwrite, assign) CGPoint titleDisplacement;
@property (nonatomic, readwrite, assign) CPTRectAnchor titlePlotAreaFrameAnchor;

@property (nonatomic, readwrite, retain) CPTAxisSet *axisSet;
@property (nonatomic, readwrite, retain) CPTPlotAreaFrame *plotAreaFrame;
@property (nonatomic, readonly, retain) CPTPlotSpace *defaultPlotSpace;
@property (nonatomic, readwrite, retain) NSArray *topDownLayerOrder;

@property (nonatomic, readwrite, retain) CPTLegend *legend;
@property (nonatomic, readwrite, assign) CPTRectAnchor legendAnchor;
@property (nonatomic, readwrite, assign) CGPoint legendDisplacement;

-(void)reloadData;
-(void)reloadDataIfNeeded;

-(NSArray *)allPlots;
-(CPTPlot *)plotAtIndex:(NSUInteger)index;
-(CPTPlot *)plotWithIdentifier:(id <NSCopying>)identifier;

-(void)addPlot:(CPTPlot *)plot; 
-(void)addPlot:(CPTPlot *)plot toPlotSpace:(CPTPlotSpace *)space;
-(void)removePlot:(CPTPlot *)plot;
-(void)removePlotWithIdentifier:(id <NSCopying>)identifier;
-(void)insertPlot:(CPTPlot *)plot atIndex:(NSUInteger)index;
-(void)insertPlot:(CPTPlot *)plot atIndex:(NSUInteger)index intoPlotSpace:(CPTPlotSpace *)space;

-(NSArray *)allPlotSpaces;
-(CPTPlotSpace *)plotSpaceAtIndex:(NSUInteger)index;
-(CPTPlotSpace *)plotSpaceWithIdentifier:(id <NSCopying>)identifier;

-(void)addPlotSpace:(CPTPlotSpace *)space; 
-(void)removePlotSpace:(CPTPlotSpace *)plotSpace;

-(void)applyTheme:(CPTTheme *)theme;

@end
```

CPTGraph is an abstract superclass; subclasses like CPTXYGraph are actually responsible for doing most of creation and organization of graph components. Each subclass is usually associated with particular subclasses of the various layers that make up the graph. For example, the CPTXYGraph creates an instance of CPTXYAxisSet, and CPTXYPlotSpace. This is a classic example of the Factory design pattern, as described in the GoF Design Patterns book (Gamma, et al).

## Plot Area ##

The plot area is that part of a graph where data is plotted. It is typically bordered by axes, and grid lines may also appear in the plot area. There is only one plot area for each graph, and it is represented by the class CPTPlotArea. The plot area is surrounded by a CPTPlotAreaFrame, which can be used to add a border to the area.

## Plot Spaces ##

Plot spaces define the mapping between the coordinate space, in which a set of data exists, and the drawing space inside the plot area.

For example, if you were to plot the speed of a train versus time, the data space would have time along the horizontal axis, and speed on the vertical axis. The data space may range from 0 to 150 km/hr for the speed, and 0 to 180 minutes for the time. The drawing space, on the other hand, is dictated by the bounds of the plot area. A plot space, represented by a descendant of the CPTPlotSpace class, defines the mapping between a coordinate in the data space, and the corresponding point in the plot area.

It is tempting to use the built in support for affine transformations to perform the mapping between the data and drawing spaces, but this would be very limiting, because the mapping does not have to be linear. For example, it is not uncommon to use a logarithmic scale for the data space.

To facilitate as wide a range of data sets as possible, values in the data space can be stored internally as NSDecimalNumber instances. It makes no sense to store values in the drawing space in this way, because drawing coordinates are represented in Cocoa by floating point numbers (CGFloat), and any extra precision would be lost.

A CPTPlotSpace subclass must implement methods for transforming from drawing coordinates to data coordinates, and for converting from data coordinates to drawing coordinates.

```
-(CGPoint)plotAreaViewPointForPlotPoint:(NSDecimal *)plotPoint;
-(CGPoint)plotAreaViewPointForDoublePrecisionPlotPoint:(double *)plotPoint;
-(void)plotPoint:(NSDecimal *)plotPoint forPlotAreaViewPoint:(CGPoint)point;
-(void)doublePrecisionPlotPoint:(double *)plotPoint forPlotAreaViewPoint:(CGPoint)point;
```

Data coordinates --- represented here by the 'plot point' --- are passed as an C array of NSDecimals or doubles. Drawing coordinates --- represented here by the 'view point' --- are passed as standard CGPoint instances.

Whenever an object needs to perform the transform from data to drawing coordinates, or vice versa, it should query the plot space to which it corresponds. For example, instances of CPTPlot (discussed below) are each associated with a particular plot space, and use that plot space to determine where in the plot area they should draw.

It is important to realize that a single graph may contain multiple plots, and that these plots may be plotted on different scales. For example, one plot may need to be drawn with a logarithmic scale, and a separate plot may be drawn on a linear scale. There is nothing to prevent both plots appearing in a single graph.

For this reason, a single CPTGraph instance can have multiple instances of CPTPlotSpace. In the most common cases, there will only be a single instance of CPTPlotSpace, but the flexibility exists within the framework to support multiple spaces in a single graph.

## Plots ##

A particular representation of data in a graph is known as a 'plot'. For example, data could be shown as a line or scatter plot, with a symbol at each data point. The same data could be represented by a bar plot/histogram.

A graph can have multiple plots. Each plot can derive from a single data set, or different data sets: they are completely independent of one another.

Although it may not seem like it at first glance, a plot is analogous to a table view. For example, to present a simple line plot of the speed of a train versus time, you need a value for the speed at different points in time. This data could be stored in two columns of a table view, or represented as a scatter plot. In effect, the plot and the table view are just different views of the same model data.

What this means is that the same design patterns used to populate table views with data can be used to provide data to plots. In particular, you can either use the data source design pattern, or you can use bindings. To provide a plot with data using the data source approach, you set the `dataSource` outlet of the `CPTPlot` object, and then implement the data source methods.

```
@protocol CPTPlotDataSource <NSObject>

-(NSUInteger)numberOfRecords; 

@optional

// Implement one of the following
-(NSArray *)numbersForPlot:(CPTPlot *)plot field:(NSUInteger)fieldEnum recordIndexRange:(NSRange)indexRange;
-(NSNumber *)numberForPlot:(CPPlot *)plot field:(NSUInteger)fieldEnum recordIndex:(NSUInteger)index; 

@end 
```

You can think of the field as being analogous to a column identifier in a table view, and the record index being analogous to the row index. Each type of plot has a fixed number of fields. For example, a scatter plot has two: the value of for the horizontal axis (x) and the value for the vertical axis (y). An enumerator in the CPTScatterPlot class defines these fields.

```
typedef enum _CPTScatterPlotField {
    CPTScatterPlotFieldX,
    CPTScatterPlotFieldY
} CPTScatterPlotField;
```

A record is analogous to the row of a table view. For a scatter plot, it corresponds to a single point on the graph.

Plot classes not only support the data source design pattern, but also Cocoa bindings, as a means of supplying data. This is again very similar to the approach taken with table views: each field of the plot --- analogous to a table column --- gets bound to a key path via an NSArrayController.

```
CPTGraph *graph = ...;
CPTScatterPlot *boundLinePlot = [[[CPTScatterPlot alloc] initWithFrame:CGRectZero] autorelease];
boundLinePlot.identifier = @"Bindings Plot";
boundLinePlot.dataLineStyle.lineWidth = 2.f;
[graph addPlot:boundLinePlot];
[boundLinePlot bind:CPTScatterPlotBindingXValues toObject:self withKeyPath:@"arrangedObjects.x" options:nil];
[boundLinePlot bind:CPTScatterPlotBindingYValues toObject:self withKeyPath:@"arrangedObjects.y" options:nil];
```

The superclass of all plot classes is CPTPlot. This is an abstract base class; each subclass of CPTPlot represents a particular variety of plot. For example, the CPTScatterPlot class is used to draw line and scatter plots, while the CPTBarPlot class is used for bar and histogram plots.

A plot object has a close relationship to the CPTPlotSpace class discussed earlier. In order to draw itself, the plot class needs to transform the values it receives from the data source into drawing coordinates. The plot space serves this purpose.

## Axes ##

Axes describe the scale of the plotting coordinate space to the viewer. A basic graph will have just two axes, one for the horizontal direction (x) and one for the vertical direction (y), but this is not a constraint in Core Plot --- you can add as many axes as you like. Axes can appear at the sides of the plot area, but also on top of it. Axes can have different scales, and can include major and/or minor ticks, as well as labels and a title.

Each axis on a graph is represented by an object of class descendant from CPTAxis. CPTAxis is responsible for drawing itself, and accessories like ticks and labels. To do this it needs to know how to map data coordinates into drawing coordinates. For this reason, each axis is associated with a single instance of CPTPlotSpace.

A graph can have multiple axes, but all axes get grouped together in a single CPTAxisSet object. An axis set is a container for all the axes belonging to a graph, as well as a factory for creating standard sets of axes (eg CPTXYAxisSet creates two axes, one for x and one for y).

Axis labels are usually textual, but there is support in Core Plot for custom labels: any core animation layer can be used as an axis label by wrapping it in an instance of the CPTAxisLabel class.

## Animations ##

A unique aspect of Core Plot is the integration of animation, which facilitates dynamic effects and interactivity. The Core Animation framework provides the mechanisms for positioning and transforming layers in time. In general you can access layers directly to apply animation. Be aware that transforming some layers, such as plots or the plot area, could invalidate the correspondence of data and axes.


# Using Core Plot Within an Application #

Core Plot works on both Mac and iPhone / iPod touch.  The framework must be integrated into a project in different ways for each platform.

## Mac Application ##

Core Plot on the Mac is a standard framework.  After pulling down the latest source code for the framework, there are only a few steps required to insert it into one of your projects.

First, you will want to make sure that Core Plot is compiled alongside your application, so drag the CorePlot.xcodeproj bundle into your application's Xcode project (without copying the file).  Then go to the Targets tab in Xcode, select your application's target, and bring up the inspector.  Go to the General settings page and add the CorePlot framework from CorePlot.xcodeproj as a direct dependency.

The framework will need to be copied into your app bundle, so add a new build phase to your application's target by Ctrl-clicking on it and selecting Add | New Build Phase | New Copy Files Build Phase.  Within the inspector that appears, change the destination to Frameworks.  Drag CorePlot.framework from within the CorePlot.xcodeproj group into this build phase.

To link Core Plot to your target application, drag CorePlot.framework from inside the CorePlot.xcodeproj group into the Link Binary with Libraries build phase in your app's target.

Because Core Plot is based on Core Animation, you need to add the QuartzCore framework to your application project as well.

To import all of the Core Plot classes and data types, add the following to the appropriate source files within your project:

```
#import <CorePlot/CorePlot.h>
```

## iPhone, iPod Touch, and/or iPad Application ##

### Dependent Project Install ###

Because frameworks cannot be used in Cocoa Touch applications in the same way as on the Mac, the means of including Core Plot within an iPhone application are slightly different.

First, drag the CorePlot-CocoaTouch.xcodeproj file into your iPhone application's Xcode project.  Show the project navigator in the left-hand list and click on your project.

Select your application target from under the "Targets" source list that appears.  Click on the "Build Phases" tab and expand the "Target Dependencies" group.  Click on the plus button, select the CorePlot-CocoaTouch library, and click Add.  This should ensure that the Core Plot library will be built with your application.

Core Plot is built as a static library for iPhone, so you'll need to drag the libCorePlot-CocoaTouch.a static library from under the CorePlot-CocoaTouch.xcodeproj group to the "Link Binaries With Libraries" group within the application target's "Build Phases" group you were just in.

You'll also need to point to the right header location.  Under your Build settings, set the Header Search Paths to the relative path from your application to the framework/ subdirectory within the Core Plot source tree.  Make sure to make this header search path recursive.  You need to add `-ObjC` to Other Linker Flags as well (as of Xcode 4.2, `-all_load` does not seem to be needed, but it may be required for older Xcode versions).

Core Plot is based on Core Animation, so if you haven't already, add the QuartzCore framework to your application project. Beginning with release 2.0, the Accelerate framework is also required.

Finally, you should be able to import all of the Core Plot classes and data types by inserting the following line in the appropriate source files within your project:

```
#import "CorePlot-CocoaTouch.h"
```

You can see some examples of plot types and features we might want to have in Core Plot at PlotExamples.

### Static Library Install ###

You can also just copy the Core Plot library directly into your project in binary form.

1. Copy the CorePlotHeaders directory to your Xcode project

2. Copy the Core Plot library to your Xcode project.

3. Open your apps Target Build Settings, and for Other Linker Flags include this:

```
-ObjC
```

(`-all_load` used to be required as a linker flag, but this is no longer needed in Xcode 4.2)

4. Add the QuartzCore framework to the project.

5. Add the Accelerate framework to the project (release 2.0 and later).

6. Change your C/C++ Compiler in the project build settings to LLVM GCC 4.2 or LLVM 1.6.
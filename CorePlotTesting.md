# Introduction #

Because Core Plot is intended to be used in scientific, financial and other domains where correctness is paramount, unit testing is integrated into the framework. Good test coverage protects developers from introducing accidental regressions and frees them to experiment and refactor without fear of breaking things.

To these ends, we encourage developers to add unit tests for all non-trivial code changes. In addition, we hope that you will add to the CPTestApp to both demonstrate the new functionality you have contributed, and to provide sample data for its use.

We make use of the SenTesting? testing framework that is bundled with the OS X developer tools and several valuable unit testing extensions provided by the Google Toolbox for Mac (GTM). In particular, we make extensive use the the GTM's rendering testing facilities to unit test rendering of the various graph elements (see below). For a general introduction to the SenTesting? framework in Xcode, see this tutorial on ADC. Read on below for information on writing unit tests specifically for Core Plot.

# Writing a new unit test module #

All unit test modules should inherit from `CPTestCase`. `CPTestCase` is a `GTMTestCase` subclass that handles setting the output folder for rendering/state test output (see below). Modules testing Core Plot classes that require a data source may wish to subclass `CPDataSourceTestCase` instead of `CPTestCase`. `CPDataSourceTestCase` provides an implementation of the data source protocol. To create a new test module:

  1. Select "New File..." in Xcode and add a file using the "Objective-C test case" Xcode template. In Core Plot, test modules are named after the module they test, with "Tests" appended to the name. For example the tests for `CPTextLayer` are in `CPTextLayerTests.h/.m`.
  1. Add the new test module's .h/.m files to the `CorePlot-UnitTests` target if this is a unit test module or the `CorePlot-PerformanceTests` target if this is a test module for verifying performance or stress testing in CorePlot (i.e. tests that may take too long to run on a regular basis).
  1. Move the new test module's .h/.m files to the "Tests" group under the group containing the module they test in the Xcode Groups & Files tree. Please create a new Tests group if none exists.
  1. Modify the `.h` of your test module to match this template:
```

#import "CPTestCase.h"


@interface CP[Module]Tests : CPTestCase {

}

@end
```

# Writing unit tests #

The SenTesting framework test runner will automatically run all test methods beginning with `test`, so the following template is a good start for writing a test method:

```
- (void)testBlah
{
    STFail(@"This test has not been implemented yet.");
}
```

Obviously, you'll want to replace the `STFail` macro with your test code.

Testing algorithmic code requires will likely require the standard `STAssert*` macros. See

## Using Google Toolbox for Mac's rendering test infrastructure ##

### NSView and CALayer state and rendering tests ###

The key benefit of GTM for the CorePlot project is in making it relatively easy to test UI (e.g. control) state and to test rendered output of NSView/UIView and/or CALayer on both the Mac and iPhone. I'll discuss state and rendering testing separately below, but both rely on saving a file that describes either the state or rendered output of a view/layer hierarchy. I will thus describe this general system first.

State and rendering tests are run by asserting that the actual state or rendering matches a saved state or rendering of a given name. The system locates the saved state or rendered output by name (see below), and the actual state or rendered output is compared against this saved version. Discrepancies indicate test failure. If no file of the given name exists yet, the current (i.e. produced by the test) file is saved.

The GTM test code searches for state/rendering files by name in the test class' bundle (i.e. the unit test bundle containg the test code). Files, indentified by "name" to the test methdods are searched for in the following order:

  1. name.extension"
  1. name.arch.extension"
  1. name.arch.OSVersionMajor.extension"
  1. name.arch.OSVersionMajor.OSVersionMinor.extension"
  1. name.arch.OSVersionMajor.OSVersionMinor.OSVersion.bugfix.extension"
  1. name.arch.OSVersionMajor.extension"
  1. name.OSVersionMajor.arch.extension"
  1. name.OSVersionMajor.OSVersionMinor.arch.extension"
  1. name.OSVersionMajor.OSVersionMinor.OSVersion.bugfix.arch.extension"
  1. name.OSVersionMajor.extension"
  1. name.OSVersionMajor.OSVersionMinor.extension"
  1. name.OSVersionMajor.OSVersionMinor.OSVersion.bugfix.extension"

Thus, multiple states/renderings can be saved corresponding to different systems etc.

When there is no existing state or rendering, the state/rendering files produced by the test are saved to saveToDirectory. Similarly, if a test fails, the actual state/rendering is saved to the same directory. By default this directory is `${BUILT_PRODUCTS_DIR}/CorePlot-UnitTest-Output` for the `CorePlot-UnitTests` target. This default is set by the `-[CPTestCase invoke]` method.

Developers can replace the saved state/rendering with the current one by simply copying the saved file from saveToDirectory into the test bundle's Resources directory. TestMerge.app provides a simple GUI for this process and makes things even easier (see below).

So, now that we know how test system locates the state or rendering to compare agains, how do we code these tests and how are they implemented by GTM?

In general, tests will verify both state and rendering out put at the same time using the **`GTMAssertObjectEqualToStateAndImageNamed(obj, name, description, ...)`** macro, which calls the state and image assertion macros described below.

NOTE: NSView, UIView and CALayer already provide state and rendering test-capability via class categories in the GTMAppKit+UnitTesting, GTMUIView+UnitTesting and GTMCALayer+UnitTesting modules respectively.


### Rendering tests ###

In order to be rendering testable, classes must implement the `GTMUnitTestImaging` protocol. NSView, UIView and CALayer already do so via GTM categories, as noted above. Basically, conformant instances can render themselves to an image via -`gtm_createUnitTestImage`. A rendering test can verify that the image is the same as a saved image via:

```
GTMAssertObjectImageEqualToImageNamed(obj, name, description, ...)
```

Really, that's it; there's no more to it.

Classes may provide more control over the rendered image by implementing the `GTMUnitTestViewDrawer` protocol. In this case tests can call:

```
GTMAssertDrawingEqualToImageNamed(obj, size, name, contextInfo, description, ...)
```
This macro instantiates a GTMUnitTestView, which calls obj to draw its unit test image.

Classes overriding `gtm_createUnitTestImage` (it's unlikely you will have to do so) may use `- (CGContextRef)[NSObject(GTMUnitTesting) gtm_createUnitTestBitmapContextOfSize:(CGSize)size data:(unsigned char**)data]` to create a bitmap context for drawing the image, lock focus on the context, then draw themselves, etc.

Like the state tests, NSView, UIView and CALayer subclasses do not have to do anything to provide rendering support.

### State tests ###

In order to be state-testable, classes must implement the `GTMUnitTestEncoding` protocol (`-(void)gtm_unitTestEncodeState:(NSCoder*)coder`). GTM provides implementations of this protocol for NSViews, UIViews and CALayers. These implementations recursively encode the state of the callee and its subviews/sublayers.

Classes may override `-(BOOL)gtm_shouldEncodeStateForSublayers` or `-(BOOL)gtm_shouldEncodeStateForSublayersOfLayer:(CALayer``*)layer` (for layer delegate) or `-(BOOL)gtm_shouldEncodeStateForSubviews` (for NSView) to indicate whether their subviews/sublayers should be encoded.

Tests may check a tree's (rooted at obj) state against the saved state with:

```
GTMAssertObjectStateEqualToStateNamed(obj, name, description,...)
```

Really, that's it; there's no more to it.

### Other nice testing tools ###

The GTM includes several other nice testing tools. Among them:

running the GTM/UnitTesting/RunMacOSUnitTests.sh as the test harness enables memory-error detecting environment variables such as MallocScribbling, MallocGuardEdges, NSAutoreleaseFreedObjectCheckEnabled, etc. and uses the Cocoa debug libraries, if present on the system. It behaves just as the standard OCUnit test harness (e.g. with failures presented as build errors in Xcode etc.).

The GTMUnitTestDevLog class allows you to test the logged output from a test. Much like a mock object, you set up the GTMUnitTestLog instance with a set of log messages that you expect to be produced, then run the test, then assert that no unexpected log messages are produced:

```
[GTMUnitTestDevLog enableTracking];
[GTMUnitTestDevLog expectString:my_expected_log_string]; // for exact string matches
[GTMUnitTestDevLog expectPattern:my_expected_log_regex]; // for regex pattern matches

... // run test code

[GTMUnitTestDevLog disableTracking];
```

The expected logs are reset with:

```
[GTMUnitTestDevLog resetExpectedLogs]; //e.g. in -setUp
```

Any unexpected log messages become failures.

Inheriting from CPTestCase (a GTMTestCase subclass) instead of SenTestCase gives automatic support for GTMUnitTestDevLog by automatically asserting that no expected logs failed to be emitted during a test.

GTMDebugSelectorValidation.h provides macros that verify via assert (in DEBUG mode only) that a selector passed into a method, e.g. as a callback selector, matches the expected form (return type and parameter types).

Bindings can be automatically tested. This is useful for testing, e.g. the bindings of a UI widget such as our plots. GTMDoExposedBindingsFunctionCorrectly() automatically exercies the exposed bindings of a class, testing the getters and setters. Classes can override -(NSMutableArray**)gtm\_unitTestExposedBindingsToIgnore to exclude bindings from this automated test. Classes can override -(NSMutableDictionary**)gtm\_unitTestExposedBindingsTestValues:(NSString**)binding to provide particular values to test for a given binding:**

```
- (NSMutableDictionary*)gtm_unitTestExposedBindingsTestValues:(NSString*)binding {
  NSMutableDictionary *dict = [super unitTestExposedBindingsTestValues:binding];
  if ([binding isEqualToString:@"myBinding"]) {
    [dict setObject:[[[MySpecialBindingValueSet alloc] init] autorelease]
             forKey:[[[MySpecialBindingValueGet alloc] init] autorelease]];
    ...
  else if ([binding isEqualToString:@"myBinding2"]) {
    ...
  }
  return dict;
}
```

Finally, classes can override `-(BOOL)gtm_unitTestIsEqualTo:(id)value` to test whether two bindings values are equal (in cases where standard isEqualTo: isn't sufficient; by it default calls isEqualTo:).
Obviously, these overrides would probably be added by a class category.


## Managing GTM test output with TestMerge ##

TestMerge should automatically launch following the test run. The purpose of TestMerge is to help you reconcile new and/or failed GTM output against the reference rendering (or state encoding) files in the test bundle. TestMerge lets you select the new reference file for each failed test. When you quit, TestMerge will ask you whether to commit the changes. If you choose commit, any new output files selected will be added to the project's TestResources/ folder and any failed output files that you selected over the original reference file will replace the existing reference file. See [this](http://blog.physionconsulting.com/?p=23) blog post by Barry for more information on using TestMerge in Core Plot or in your own project.

To build TestMerge, you will have to install the BWToolkit IB plugin, which is used by TestMerge's UI. Open the TestMerge?/TestMerge?.xcodeproj and then the BWToolkit.xcodeproj (referenced from TestMerge?.xcodeproj). Build the BWToolkit plugin (Release configuration). In Interface Builder, select Preferences->Plugins and add the newly built BWToolkit plugin.
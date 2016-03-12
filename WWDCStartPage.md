# Welcome #

Welcome to the Core Plot [Science Coding Project](http://developer.apple.com/wwdc/sciencemedicine/) (a.k.a. "Code-A-Thon") at [WWDC 2009](http://developer.apple.com/wwdc/). Thanks for helping!

This page details or links to instructions for getting started, details on coding standards, how to use our testing features in Core Plot, and how to get your changes incorporated into the Core Plot trunk.

# Version Control #

The Mercurial version control system will be used for the Core Plot code-a-thon. All development at the WWDC code-a-thon will be carried out in a [repository](http://bitbucket.org/drewmccormack/core-plot-wwdc/) at http://www.bitbucket.org, rather than in the Core Plot repository at [Google Code](http://code.google.com/p/core-plot/).

Developers that would like to contribute changes back to the project should:
  * sign up  for a  [free account](http://bitbucket.org/account/signup/) at BitBucket
  * create a [personal fork](http://bitbucket.org/help/Collaborating) of the code-a-thon [repository](http://bitbucket.org/drewmccormack/core-plot-wwdc/).
  * install and configure [Mercurial](http://www.selenic.com/mercurial/wiki/BinaryPackages#Mac_OS_X) (and perhaps [Murky](http://bitbucket.org/snej/murky/wiki/Home))
  * "clone" your fork onto your local machine so you can work on one of the [WWDCProjects](WWDCProjects.md)
  * periodically push stable versions back up to your BitBucket repository
  * When your fork is ready for merging back into the main branch, alert the Core Plot team using BitBucket's 'Pull Request' feature.

Full details of all these procedures are given below.

Several of the existing Core Plot committers have agreed to act as editors and coordinators to merge these personal branches into the main branch. After WWDC, we'll be merging all of these changes back into the main Core Plot repository at Google Code.

# Getting started #

## Installing and learning Mercurial ##

The machines set up by Apple for the code-a-thon at WWDC should already have Mercurial installed. If you don't have Mercurial installed on your system, you can download binaries for OS X 10.4 and 10.5 [here](http://www.selenic.com/mercurial/wiki/BinaryPackages#Mac_OS_X).

The UsingMercurial page has links for resources to help familiarize yourself with Mercurial and distributed version control systems if you're not familiar with how they work ([this](http://www.selenic.com/mercurial/wiki/UnderstandingMercurial) page and [this](http://betterexplained.com/articles/intro-to-distributed-version-control-illustrated/) page provide useful high-level overviews of distributed version control systems like Mercurial).

## Forking and Cloning the Mercurial repository ##

To begin hacking Core Plot, you need to create a personal fork of the respository at BitBucket. Go to http://www.bitbucket.org and sign up for a free account. Then visit [the Core Plot WWDC repository page](http://bitbucket.org/drewmccormack/core-plot-wwdc/) and click the fork button. Fill in the details to create your fork.

Now you need to make a local clone of your forked repository. Details for cloning your fork are found on the web page of your forked repository. To clone the repository, enter a command like this in Terminal

```
hg clone https://<bitbucket username>@bitbucket.org/<bitbucket username>/core-plot-wwdc/
```

You may want to edit ~/.hgrc so that your name (and email) address are automatically included in your commits. If you don't set your username, _we won't necessarily be able to credit you in the changelog_. To set your user name and email, add the following to ~/.hgrc

```
[ui]
username = Your Name <your_email>
```

## Contributing Changes ##

While you make changes in your local copy of Core Plot, you should make regular commits, using the `hg commit` command. These commits go into your local repository, not the server. When you are finished a set of useful changes, you should push everything up to your personal BitBucket repository like this:

```
hg push
```

If all goes well, your changes will have been uploaded to BitBucket.

Whenever you have something that you think is ready to be shared, please ask us to merge them into the main Core Plot repository.

To do this:
  * go to the [main Core Plot repository page at BitBucket](http://bitbucket.org/drewmccormack/core-plot-wwdc/)
  * click on 'pull request'.
  * In the description, enter a short description of the changes you have made, include any [Issue numbers](http://code.google.com/p/core-plot/issues/list) from the Google Code site that your branch addresses
  * enter your BitBucket user name
  * Finally, issue the pull request

From there, one of the editors will look into how best to integrate your code.

## Pulling Updates ##
{Warning: this is a new section, and has not been fully tested.}

From time to time, you may want to pull in the latest changes from the main WWDC branch on BitBucket. This requires several steps:
```
hg ci -m "Be sure to commit my latest changes before merging upstream diffs"
hg push # Push your latest version back up to your bitbucket branch
hg pull https://<username>@bitbucket.org/drewmccormack/core-plot-wwdc
hg update
hg ci -m "Commit changes pulled down from master branch"
hg push # Push merged results back up to your bitbucket branch
```

## Keeping in Contact ##

Throughout the code-a-thon, Core Plotters will be maintaining virtual contact via an IRC channel: #coreplot (irc.oftc.net). Those not at WWDC are also welcome to partake in discussions on this channel. Use it to ask questions, or discuss design issues. Anything related to Core Plot is fair game.

## Learning about Core Plot ##

The best way to learn the code is, of course, to get in and get your hands dirty. We're working to have test coverage for much of Core Plot, so the test modules (labeled CP\*Tests.m in a Tests/ group below each of the groups in Source/ in the Core Plot.xcodeproj) are good examples of API usage. There is also the CPTestApp project source for examples of using the Core Plot classes.

Core Plot is based heavily on Core Animation technologies. Each plot element is (for the most part) a subclass of `CPLayer`, which is a `CALayer` subclass. HighLevelDesignOverview has a high-level overview of Core Plot including an illustration showing the class and layer hierarchy for a typical graph. This illustration is a good roadmap for exploring the code and for understanding how to construct and manipulate graph elements.

## Pick a project ##
Several project ideas are listed [here](WWDCProjects.md). Any of the issues in the Core Plot [tracker](http://code.google.com/p/core-plot/issues/list) are also in need of attention. Of course, any effort is valuable. If you have some new ideas, you may want to run them by folks in the room or on #coreplot (irc.oftc.net) for feedback before you get started. Some of the local coordinators will try to direct folks to projects that need the most help so that we don't duplicate efforts unnecessarily.

# Coding & Testing standards for Core Plot #

Since we want Core Plot to be both easy _and_ safe (as in correct) to use in many contexts, by many developers, for many different types of applications, we're working hard to make the code and API easy to read and use and to make sure that the functionality of Core Plot is well tested to verify correctness and to prevent regressions. Code that follows our standards for coding style and testing are more likely to get integrated into the main repository.

## Coding style ##
Try to make your code follow the existing style. More detailed style description is available in [documentation/Coding Style.mdown](http://code.google.com/p/core-plot/source/browse/documentation/Coding%20Style.mdown) in the Core Plot repository.


## Testing ##
Because Core Plot is intended to be used in scientific, financial and other domains where correctness is paramount, unit testing is integrated into the framework. Good test coverage protects developers from introducing accidental regressions and frees them to experiment and refactor without fear of breaking things.

To these ends, we encourage developers to add unit tests for all non-trivial code changes. In addition, we hope that you will add to the CPTestApp to both demonstrate the new functionality you have contributed, and to provide sample data for its use.

To get started with the Core Plot unit tests, **you will have to install the BWToolkit IB plugin**, which is used by TestMerge, our GUI tool for managing test output. Open the TestMerge/TestMerge.xcodeproj and then the BWToolkit.xcodeproj (referenced from TestMerge.xcodeproj). Build the BWToolkit plugin (Release configuration). In Interface Builder, select Preferences->Plugins and add the newly built BWToolkit plugin or double-click on the built plugin to install it.

We make use of the SenTesting testing framework that is bundled with the OS X developer tools and several valuable unit testing extensions provided by the Google Toolbox for Mac (GTM). In particular, we make extensive use the the GTM's rendering testing facilities to unit test rendering of the various graph elements (see below). For a general introduction to the SenTesting framework in Xcode, see [this](https://developer.apple.com/mac/articles/tools/unittestingwithxcode3.html) introduction and these [guidelines](http://developer.apple.com/DOCUMENTATION/DeveloperTools/Conceptual/UnitTesting/Articles/UTGuidelines.html)  on [ADC](http://developer.apple.com).

Here's a general outline for adding unit tests for code that you add or modify:

  1. If you are modifying or adding to a module that already has an associated test module (e.g. you are working on `CPScatterPlot.h/.m` which already has an associated `CPScatterPlotTests.m`, add your unit tests to the existing module.
  1. If you need to create a new test module:
    1. Add an "Objective-C test case class" to the appropriate group in the Xcode project, named `CP[Module]Tests`.
    1. Add the new class to the "Core Plot-UnitTests" target (**not** to the "Core Plot" target).
    1. Change the superclass of your test class to `CPTestCase` (or `CPDataSourceTestCase` if you're testing a unit that requires a data source delegate). Y
    1. Add an `#import "CPTestCase.h"` or `#import 'CPDataSourceTestCase.h` to your new `.h` file.
  1. Write your unit tests.
    * Methods that have a signature like `-(void)test*` (e.g. `-(void)testMyNewFeature`) will be automatically detected by the SenTesting/GTM test harness.
    * GTM provides convenient methods to verify that the rendered output of the unit under test matches a previously generated output. If you're testing a component that renders to the screen, at least one of your unit tests should make use of `GTMAssertObjectImageEqualToImageNamed` or `GTMAssertObjectEqualToStateAndImageNamed`. See existing tests, such as `CPTextLayerTests.m` for examples of usage of these assertions. You may also want to read `GTMNSObject+UnitTesting.h` and `GTMCALayer+UnitTesting.h` in the google-toolbox-for-mac folder at the root of the project working directory for more information on these macros. In brief, both will generate a rendered image of a CALayer, NSWindow, UIView or NSView and compare that rendered image to a saved image of the given name. Failures (i.e. mismatches) will be saved in XXX. Similarly, the encoded state of the object will be compared to a previously saved version. If either the image or endoded state do not currently exist in the test bundle's Resoures/ folder, a new one is saved to XXX. In either case, the TestMerge app will launch automatically to help you verify and select the new reference rendering or state. If you add a new image or state test, you should add an appropriate reference image/state to the unit test target by adding the image/state to the project under Testing/TestResources (TestMerge will place the file in framework/TestResources) and adding the files to the "Core Plot-UnitTests" target (**not** the "Core Plot" target).
    * You can use any of the standard `STAssert*` macros as well.


To run tests, set the current target of the Core Plot project to "Core Plot-UnitTests" and build. Test failures will show up as build errors. If you want to debug your tests or code while it's being run in the test harness, select `Debug` (option-cmd-Y) from the "Run" menu in Xcode.

You may notice that the GTM test harness changes the color space of your monitor during tests. This is to standardize rendering so that rendered images can be compared across development machines. The color space will be changed back to the original color space when all tests finish. If you stop the tests mid-run, or the color space is not reset, you can manually reset it using the "Colors" tab System Preference's "Displays" panel.
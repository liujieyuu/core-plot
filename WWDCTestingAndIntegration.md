# Coordinator policies (Integrating submissions) #

We are grateful for your help with the Core Plot project. We would like to take all contributions, in any form; as they say, "code is better than no code." However, the Core Plot project will benefit most from contributions that produce maintainable code and help move the project forward efficiently. Thus, several coordinators will be managing the process of reviewing, accepting, and merging contributions. Their goal is _not_ to hinder participation, but to manage the chaos so that the product of all of our efforts is the best plotting framework possible.

The onsite and offsite coordinators will be both triaging and reviewing contributions from patches or pull requests on BitKeeper. Triaging means prioritizing which contributions to review first. The criteria for triaging are as follows (contributions meeting more criteria will be reivewed first):

  1. Contribution relates to an open issue in the Core Plot [tracker](http://code.google.com/p/core-plot/issues/list) (please create an issue for your work if one doesn't already exist!).
  1. Contribution follows coding style guidelines. Minor deviations are OK, but the coordinator will likely request that you fix them before final acceptance.
  1. Contribution is adequately documented if appropriate (see documentation guidelines above)
  1. Contribution has adequate unit tests if appropriate. Adequate unit tests cover any non-trivial code/methods, verify correct behavior of any algorithms (including edge cases where appropriate) and test rendered output of any on-screen renderings produced by the unit under test. Testing is especially important for changes to the CorePlot.framework itself. See CorePlotTesting for more detailed examples of writing tests for Core Plot.
  1. Contribution can be applied to wwdc2009 head without major conflicts. Please do a pull/merge/commit and resolve any conflicts before generating your patch. You're the best person to solve any merge conflicts. It's your code, after all.
  1. Contribution is a single, self-contained patch (please submit multiple patches if you work on multiple issues) or a pull request from a branch that has a single, self-contained set of changes.



Coordinators will publish the triaged order and review patches in that order. Review criteria generally mirror the triaging criteria. Contributors will be asked to revise contributions until they meet _all_ of the above criteria if possible. Coordinators will communicate their reviews as comments on the issue/pull request. Hopefully one coordinator will manage a contribution from submission to acceptance so that you can contact them directly if you have questions/issues/etc.

If you do not want to continue to work on your submission after review, we will still gladly accept your help. Please understand that this will require Core Plot developers to continue to work on your patch until it meets the above criteria. For this reason, there may be a delay in incorporating your work into Core Plot. Coordinators may review other patches first in this case, if the contributors of those patches are willing to revise them if necessary.

# Testing Guidelines #

CorePlotTesting has examples and templates for writing unit tests for Core Plot. In general, we try to write tests for any non-trivial code in the Core Plot framework. We are particularly concerned with verifying correct behavior of any algorithms that affect plot output (such as transformations between data and screen space) and rendering behavior. The former is more appropriately tested using the standard SenTesting.framework's `STAssert*` macros. The later is easily tested using Google Toolbox For Mac's `GTMAssertObjectImageEqualToImageNamed` or `GTMAssertObjectEqualToStateAndImageNamed` macros. See CorePlotTesting for examples of using these macros.

We've worked very hard to make writing and running the unit tests easy so that the barrier to writing tests is low. One of the tools we've developed to aid unit testing is a tool to help you manage the output produced by the GTM rendering and state assertion macros. This tool, TestMerge, will open after all tests are run and will manage the process of viewing and resolving new output or failed output from the GTM tests. See UsingTestMerge for more info.
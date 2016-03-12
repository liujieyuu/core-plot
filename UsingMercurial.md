# Introduction #

To enable parallel development, Core Plot has opted to use the Mercurial for our Source Code Control System.  Mercurial is a Distributed Version Control System (DVCS), similar to Git and Bazaar, with the specific advantage of being supported by Google Code.

# Installing Mercurial #

Mercurial is not part of Mac OS X or the Xcode Developer Tools, so you need to install it manually.

## Command-Line ##

You can build it yourself using Fink and Mac Ports, but there is also a pre-packaged binary available at:

> http://mercurial.berkwood.com/

(see the bottom half of the page).   This will install mercurial into "/usr/local/bin/hg".  Type "hg" into the Terminal application to verify.  Note that if you are using the C-shell, you may need to "% rehash" for it to show up.

## GUI ##

There is also a graphical front-end for Mercurial on the Mac known as **Murky**:

> http://bitbucket.org/snej/murky/wiki/Home

Note that you must **first** install the command-line version for this to work.

# Using Mercurial #

> http://code.google.com/p/core-plot/source/checkout

The key concept behind DVCS is that you **clone** a personal repository on your local drive as part of your working directory, which you can then use to incrementally check in your own changes while you work.

Once you have something you are happy with you, you can **pull** down the latest version, work through any conflicts locally, then **push** the resolved diffs back up to trunk (or whatever branch you're working on).

# Resources #

## Learning Mercurial ##
  * http://betterexplained.com/articles/intro-to-distributed-version-control-illustrated/
  * http://www.selenic.com/mercurial/wiki/QuickStart
  * http://www.selenic.com/mercurial/wiki/UnderstandingMercurial
  * http://www.selenic.com/mercurial/wiki/Tutorial
  * http://bitbucket.org/jespern/help/wiki/GettingStartedWithMercurial

## Using Mercurial Effectively ##
  * http://bitbucket.org/help/Collaborating
  * [Mercurial: The Definitive Guide](http://hgbook.red-bean.com/)

## Cheatsheets ##
  * http://edong.net/2008v1/docs/dongwoo-Hg-PDF.pdf
  * http://www.ivy.fr/mercurial/ref/v1.0/Mercurial-Usage-v1.0.pdf
  * http://nedbatchelder.com/text/hgsvn.html



## Mercurial at Google Code ##
  * http://code.google.com/p/support/wiki/MercurialStatus
  * http://google-code-updates.blogspot.com/2009/05/mercurial-now-available-to-all-open.html
  * http://google-code-updates.blogspot.com/2009/04/mercurial-support-for-project-hosting.html
  * [Why Mercurial?](http://code.google.com/p/support/wiki/DVCSAnalysis)
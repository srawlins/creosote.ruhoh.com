---
title: Checklist for the Benevolent Open Source Maintainer
date: '2012-09-20'
description: Checklist for the Benevolent Open Source Maintainer
tags: []

type: draft
---

It's easy to whip up a quick mass of code that does something useful for
yourself or your development team, throw it on GitHub, and call it open source.
But when it comes to effectively sharing your work and providing to the
community, you need to do a little more work.

Supporting your open source project can take many different forms, and being
the perfect benevolent maintainer doesn't have to be the goal. But you should
certainly consider this list of TODOs that will get your code out there, help
your users understand your project, and enable your project to grow.

### The Basics

I consider these tasks to be the bare minimum whenever you want to share your
code with the world, and make it minimally easy for anyone to use. The basic idea behind these tasks is to give people a starting place. People wanting to _hack_ on your project need a starting place just as the _users_ of your code do.

#### Source

At a minimum, you need to make your source code easily accesible. This can be
done a variety of ways:

The best solution is likely [GitHub](https://github.com/features/projects) which, at a glance, provides:

* [Collaboration Features](https://github.com/features/projects/collaboration),
* [Git-Powered Wikis](https://github.com/features/projects/wikis),
* [Basic Issue Tracking](https://github.com/features/projects/issues), and
* [Code Review](https://github.com/features/projects/codereview), ("Futuristic Code Review", Commit Comments, Branch Analysis, and Compare View).

Other solutions include

* [RubyForge](http://rubyforge.org/),
* [SourceForge](http://sourceforge.net/), which has seriously modernized (examples from KeyPass: [File Downloads](http://sourceforge.net/projects/keepass/files/), [Forums](http://sourceforge.net/projects/keepass/forums/forum/329220), Trackers such as the [Bug Tracker](http://sourceforge.net/tracker/?group_id=95013&atid=609908), and [Code Repositories](http://sourceforge.net/projects/keepass/develop)),
* or just a URL for anonymous access to your own hosted code repository, like many GNU projects ([GMP's repository information page](http://gmplib.org/devel/repo-usage.html)).

#### README

A README is an absolute minimum. Not all projects have full-fledged manuals or
generated docs, but a README serves as a great introduction to your project.
You get this for free when you go with GitHub, and many projects use their
README as their primary documentation. Excellent examples include:

* [Capybara](https://github.com/jnicklas/capybara) has an ~800 line README hosted at GitHub. They also have a [GitHub page](http://jnicklas.github.com/capybara/) that points to 4 different resources.
* [Sinatra](http://www.sinatrarb.com/intro) has a _2000_ line README! They do have other documentation resources, but this README is boss.

#### Tests

Honestly, tests will vary from programming community to programming community. The Ruby community is crazy awesome at this concept. It is now somewhat expected to see:

* [How](https://github.com/jnicklas/capybara#development) [to](https://github.com/chneukirchen/rack#running-the-tests) [run](https://github.com/cucumber/gherkin#hacking-installing-the-toolchain) the tests.
* [Continuous](https://github.com/chneukirchen/rack#rack-a-modular-ruby-webserver-interface--) [Integration](https://github.com/cucumber/gherkin) build status (more on that later).

In my experience, the Perl community and regions of the C community are also
really good about testing their code, and publishing/documenting tests. The
JavaScript community is also getting pretty good about this stuff. Awesome.

I think that providing a tiny bullet in your README showing how to run tests is
perfectly adequate here. Lots of projects have various gotchas regarding your
testing environment, so it is very helpful to spend 10 minutes and write out
these issues.

#### Packaging / Distribution

Whether your open source project is Perl, C, Java, Ruby, Python, Javascript,
etc., there are likely one ore more common distribution platforms for similar
packages. Here are a few great examples to follow:

* [Shotwell](http://yorba.org/shotwell/), by Yorba, is the best photo organizer
  for Linux. They provide a slick little [install
  page](http://yorba.org/shotwell/install.html) that give detailed installation
  steps, whether you are on Ubuntu, Fedora, or something else. They provide
  their own update repository, so you can stay up to date. You can also build
  from source.
* Gherkin is the language behind Cucumber. The
  [fast lexer and parser](https://github.com/cucumber/gherkin) that they
  developed is available through the following channels:
  * RubyGems.org, for Ruby and JRuby,
  * npm, for Node.js,
  * Maven Central for Java,
  * NuGet, for .NET.
* [VLC](http://www.videolan.org/vlc/index.html#download) provides about a
  million download links for every Linux flavor, mainstream OS, and more.

No matter your environment, you likely have _some sort_ of package distribution.

* Ruby has [RubyGems.org](http://rubygems.org/) (nearing 1 billion downloads!)
* For Perl, you of course have [The Comprehensive Perl Archive Network](http://www.cpan.org/).
* For TeX, there is [The Comprehensive TeX Archive Network](http://www.ctan.org/).
* For Python, you have [The Python Package Index](http://pypi.python.org/pypi).
* For Java, you are probably using [Maven Central](http://search.maven.org/).

### Good Practices

These tasks will require some more work, but will be very helpful to your
community (or rather... help to _create_ a community around your project). Some
of these are honestly very easy to complete, so you should look at all of these
before groaning and clicking away :).

Additionally, some of these ideas will be helpful for _you_. Documenting your
code will help your team to use and extend the code. Writing a manual or a
Getting Started document will save you time whenever someone wants to try your
code out.

#### GitHub Page

If your code is already hosted as a GitHub project, a great way to create a
one-stop homepage is a **GitHub Page**. I'm going to let GitHub
[explain](http://pages.github.com/) these themselves, and I'll just say that
they work great as READMEs, and multiple pages can form a great mini manual.
Here are some examples:

* [Bundler](http://gembundler.com/)
* [Jekyll](http://jekyllrb.com/)
* [YAJL](http://lloyd.github.com/yajl/)

#### Auto-Generated Documentation

This one definitely varies from language to language. Not a lot of people use this kind of thing with C or C++ projects, but there are systems like

* [Doxygen](http://www.stack.nl/~dimitri/doxygen/), "a documentation system for C++, C, Java, Objective-C, Python, IDL (Corba and Microsoft flavors), Fortran, VHDL, PHP, C#, and to some extent D.", and
* [Docco](http://jashkenas.github.com/docco/), "Docco is a quick-and-dirty, hundred-line-long, literate-programming-style documentation generator. It produces HTML that displays your comments alongside your code. Comments are passed through Markdown, and code is passed through Pygments syntax highlighting.", which can document CoffeeScript, JavaScript, Ruby, Python, or TeX.

For other languages, however, like Ruby and Java, have very standard and very popular auto-generating documentation engines. Some projects take crazy advantage of this, such as Ruby's [Sequel](http://sequel.rubyforge.org/rdoc/classes/Sequel/Model/Associations/ClassMethods.html), and [Jira's Soap API](http://docs.atlassian.com/rpc-jira-plugin/4.4/com/atlassian/jira/rpc/soap/JiraSoapService.html).

#### Manual

To put it simply: a manual is important if your project is large. Project's like Ruby's [Headless](https://github.com/leonid-shevtsov/headless) gem don't need a manual (Headless exposes a grand total of 5 methods, I think: `Headless.ly`, `Headless.new`, `Headless#start`, `Headless#destroy`, and `Headless#video`). Apache Commons's [StringUtils](http://commons.apache.org/lang/api-2.5/org/apache/commons/lang/StringUtils.html) exposes quite a few static methods, but there is no complicated structure or procedure that needs to be explained. The JavaDoc is quite sufficient.

If, however, you have an open source project that has a vast array of classes, methods, interactions, interfaces, etc., then it would behoove you to write some semblance of a manual. I'm using the word "manual" pretty loosely here. This can be brief or long, on one page or many. Here are a few great examples of manuals, some of which are not very different from a README (these things definitely overlap):

* Sequel has an HTML [manual](http://sequel.rubyforge.org/documentation.html), with pages linking to each other. There is a a page for every substantial topic, such as ["Sequel for SQL Users"](http://sequel.rubyforge.org/rdoc/files/doc/sql_rdoc.html) and ["Migrations"](http://sequel.rubyforge.org/rdoc/files/doc/migration_rdoc.html) and ["Model Validations"](http://sequel.rubyforge.org/rdoc/files/doc/validations_rdoc.html).
* Bundler has a _minimal_ [manual](http://gembundler.com/), as a GitHub Pages project. It serves its purpose, but is a little hard to navigate. This is a crazy simple way to write a manual, though. Just write up a bunch of documents in a git repo, and link them to each other. [Here](https://github.com/carlhuda/bundler/tree/gh-pages) is the source for Bundler's GitHub Pages.
* [GMP](http://gmplib.org/) provides a manual in two different formats: single-page HTML, and PDF. They are able to do this by writing all of their documentation in [Texinfo](http://www.gnu.org/software/texinfo/).

#### A Getting Started Document

**Getting Started** will of course vary from project to project. I think that these are important for:

* a project that has many concepts that build on eachother, such as [Sequel](http://sequel.rubyforge.org/documentation.html),
* a project that requires some boilerplate code that is written just once per implementation. Sequel is a good example of this, as is [Nokogiri](http://nokogiri.org/), which provides a nice Synopsis on their home page.
* a project that has a complicated API, or introduces a whole new programming style, such as [EventMachine](https://github.com/eventmachine/eventmachine#getting-started).

### Taking it Seriously

#### Extensive Manual

Beyond the basic manual, you can create something more extensive. This can be as massive as a beautiful PDF, explaining everything there is to know about your project. GMP, MPFR, and MPC are great examples. Each of these projects have documentation written in texi, which they compile into both [HTML](http://gmplib.org/manual/) and [PDF](http://gmplib.org/gmp-man-5.0.5.pdf). GMP's latest PDF is 1.1MB, at 145 pages, and FLINT's [PDF](http://www.flintlib.org/flint-2.2.pdf) is 1.3MB, at 192 pages.

I absolutely recommend writing your manual in some format that can be published to multiple others. [Pandoc](http://johnmacfarlane.net/pandoc/) is my current absolute favorite for this kind of work. Anything you hope that Pandoc supports, it does. I use it to translate a Markdown document into HTML and PDF.

#### Public Continuous Integration

OK, if you have some tests written and you like Continuous Integration _or_ if you _don't_ like Continuous Integration, you should totally absolutely consider a _Public_ Continuous Integration process. Right now, uhh... I think the only service for this is [Travis CI](https://travis-ci.org/). This service is amazing. You can go to the homepage and poke around, and see all of the projects currently using Travis CI. Their [GitHub page](https://github.com/travis-ci/travis-ci) is a nice _actual_ introduction to the service. And here's an [excellent screencast](http://www.youtube.com/watch?v=mNOwCJhjWAw). Here's a quote from their Goals:

> Travis is an attempt to create an open-source, distributed build system for the OSS community that:
> 
> 1. Allows open-source projects to effortlessly register their GitHub repository and have their test suites run after pushes
> 2. Allows users to contribute build capacity by connecting a machine that runs Travis workers and VMs they use on their underused servers
> 
> With Travis CI our vision is to become for builds (i.e. tests, for starters) what services like rubygems.org or Maven Central are for distribution of libraries. We strive to build a rock solid, but dead-easy to use open-source continuous integration service for the open source community.

Travis does require GitHub for their integration (I think...). If you have a DevOps dude sitting around, doing nothing (yeah... right, we all have extra labor lying around), you could try to build it yourself as well. The entire freaking service is open source.

#### License

No one goes into software development for the fun legal reasons, but it is important to license your code. For some resources on various open sources licenses, you can view the Open Source Initiative's list of [Open Source Licenses by Category](http://opensource.org/licenses/category). They have some great (but opinionated) advice on choosing a license, and good definitions:

* [How to choose a license for your own work](http://www.gnu.org/licenses/license-recommendations.html)
* [Why you shouldn't use the Lesser GPL for your next library](http://www.gnu.org/licenses/why-not-lgpl.html)
* [What is copyleft?](http://www.gnu.org/copyleft/copyleft.html)

Also, if this is a Ruby gem, consider documenting the license in your [gemspec](http://docs.rubygems.org/read/chapter/20#license).

If you are writing a (Java) project managed by Maven, you might want to look into [Sonatype's Insight](http://www.sonatype.com/Products/Why-Sonatype/License-Compliance) (or similar). From their website:

> Hidden license obligations can leave your organization exposed to legal and
> business risk. To eliminate this risk, you need visibility into how
> components are licensed. And not just the licenses for the components you
> included, but the entire dependency tree.

.

> Are you aware of what your license obligations are? For the top level
> dependencies alone, this is an insurmountable task. With our report you will
> know exactly what those obligations are and where to find them within the
> tree.

[Here](http://www.sonatype.com/var/ezflow_site/storage/images/dependency-diagram_licenses/8244-1-eng-US/dependency-diagram_licenses.jpg) is an excellent graphs showing the legal ramifications of depending on / including various software in your project.

#### CONTRIBUTING Document

The need for a CONTRIBUTING document is perhaps most obvious when your project is on GitHub and anyone can clone your repository with a single mouse click. But I think that this document is important in every project you make available. Anyone who downloads your source code from your SVN repository, or your SourceForge page, etc., might want to contribute to your project. Some people will want to fix a typo in the documentation. A common experience I have is Windows users taking it upon themselves to fix incompatibilities that the primary developers never had a chance to test.

A CONTRIBUTING document doesn't need to be very long, but should provide the information that you may be asked over and over, and tips for your adoring fans, on how to get their contributions accepted into your code base. GitHub recently formalized the idea of [Contributing Guidelines](https://github.com/blog/1184-contributing-guidelines), and they point to [Puppet](https://github.com/puppetlabs/puppet/blob/master/CONTRIBUTING.md) and [factory_girl_rails](https://github.com/thoughtbot/factory_girl_rails/blob/master/CONTRIBUTING.md) as two projects with great CONTRIBUTING docs. Here's a list of what you should discuss in your CONTIBUTING document:

* How to initially make your contribution known, if it's an issue, such as opening an issue on the project's tracker,
* How to properly commit and tag your changes,
* How to run your tests,
* Whether a potential needs to sign a CLA, such as [Puppet Labs'](https://projects.puppetlabs.com/contributor_licenses/sign), or the [CLA for jQuery](http://blog.jquery.com/2012/09/10/jquery-licensing-changes/),
* Rules for new contributions.
* What versions of the language or VM need to be tested against,
* Syntax guidelines or tips.

As far as "Rules for new contributions, I like 'factory-girl-rails's:

> "Add a test for your change. Only refactoring and documentation changes require no new tests. If you are adding functionality or fixing a bug, we need a test!"

And [Mail's](https://github.com/thoughtbot/factory_girl_rails/blob/master/CONTRIBUTING.md)

> "Include tests that fail without your code, and pass with it"

#### Extensive Tests

Many projects take testing _very_ seriously. Good testing practices, code
coverage, and platform compatibility can look very good to someone evaluating
your project. I know that I hold Ruby's [Mail gem](https://github.com/mikel/mail)
in very high regard when I see the following in the README:

> Basically... we do BDD on Mail. No method gets written in Mail without a
> corresponding or covering spec. We expect as a minimum 100% coverage measured
> by RCov. While this is not perfect by any measure, it is pretty good.
> Additionally, all functional tests from TMail are to be passing before the
> gem gets released.

#### Presentations

Conference Presentations are a great way to get word out about your project.
Now that there are 53424581 conferences for every programming language,
paradigm, and web framework out there, you should be able to get a slot if your
project is rather general purpose, or if you can extrapolate general lessons
from your time creating this project. Local user groups and regional
conferences can lead to bigger conferences, and better presentations. I think a
great example of this is [Cucumber](http://cukes.info), where they have a few
presentation videos all on the homepage.

#### Screencasts

Screencasts are fun! Fun for both you and the users. It is possible to create
screencasts rather cheaply these days. You likely have a laptop with a
microphone and a camera. Screencasting software is available and free for
Windows, Mac OS X, and Linux.

#### irc Channel

We're now definitely getting into the dedicated, benevolent maintainer arena
here. Dedicating an irc channel, and your time as support, to your project, is
a big step.

However, this can be an _invaluable_ resource for your adoring fans. Having a
_conversation_ with the _authors_ of a project, as well as more experienced
users, is crazy useful. I've talked to Wayne at `#rvm` for help using RVM. I
think I've chatted in a ruby chat, a ruby developers chat, a scintilla chat,
etc.

#### Mailing List

A mailing list is certainly an easier tool to implement. There are pretty
standard places to build a mailing list (ie _group_), such as Google Groups,
Yahoo Groups, and I think even SourceForge will build a mailing list concept
for you.

#### Wiki

A wiki is another form of documentation, and again is not mutually exclusive with other forms, like the README, and GitHub page, and manual. Wikis you can also get free from a number of resources. The simplest that probably jives with other work you've been up to, is [GitHub](https://github.com/blog/699-making-github-more-open-git-backed-wikis) [Wikis](https://github.com/blog/774-git-powered-wikis-improved) (backed by [Gollum](https://github.com/github/gollum) (backed by Git)).

#### Support Tracker

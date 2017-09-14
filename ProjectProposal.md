![](assets/markdown-img-paste-20170909112804883.png)

- [**Open source project description**](#--open-source-project-description--)
  * [Group Member Names](#group-member-names)
  * [What is it?](#what-is-it)
  * [Activity](#activity)
  * [Use](#use)
  * [Popularity](#popularity)
  * [Languages used](#languages-used)
  * [Platform](#platform)
  * [Documentation sources](#documentation-sources)
  * [Discuss License](#discuss-license)
  * [Procedures for making contributions](#procedures-for-making-contributions)
  * [Contributor agreements](#contributor-agreements)
  * [Security related history](#security-related-history)
  * [Functional security requirements for the software](#functional-security-requirements-for-the-software)
  * [Motivation for selecting this project](#motivation-for-selecting-this-project)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# **Open source project description**

### Group Member Names
* Larry Singleton

* Chad Crowe

* Daniel Ritter

* James Percival

## What is it?
### Overview
 Jenkins is a better way to test and deploy software.  It performs these tasks by automating much of the process.  This automation is setup via Jenkins helpful UIs that visualize building, testing, and deploying your software.  Upon setup, Jenkins serves as an invaluable quality control tool by allowing users to automatically monitor their repositories, build software, and run relevant tests.

 The impressive Jenkins website https://jenkins.io/index.html endorses open source, even touting itself as the leading open source automation server and boasting about its hundreds of tacitly equipped and exceptionally handy plugins which encourage a deftly build and deploy for any project.  More specifically, Jenkins highlights many of its core features like its continuous integration, easy installation, configuration, and extensibility while also being distributed across many machines.  With such impressive features, its wide adoption, and its openness to the open-source community highlighted Jenkins as an appropriate project for our group.

 Our group sought out greater detail on Jenkins from openhub https://www.openhub.net/p/jenkins and its GitHub site https://github.com/kohsuke/jenkins to ascertain insights on this innovative open source project.  GitHub further highlighted Jenkin's static code analysis features.  We're hopeful that we might adapt such static code analysis on Jenkins itself,  which seemed like a fun and original idea.  The openhub site mentioned the large number of outside contributions to Jenkins, lauding some 1300 plugins within the Jenkins library to supplement project builds, tests, logging, analysis, graphing, and notifications with your project needs.  

 Founded in 2004 by Kawaguchi while at the somewhat infamous Sun, the Hudson project was forked and within a few years, Jenkins was born. Humorously, the community around Oracle's Hudson project moved to work on Jenkins, though Kawaguchi was also the founder of Hudson, which I'm sure played into the 213 vs 13 vote to fork the project from Hudson.  That's some serious karma for Oracle, who sought to trademark Hudson and vainly insisted it was deployed exclusively on Oracle servers.


### Activity

Since its founding, Jenkins has skyrocketed into a leading provider for continuous integration.  

The project itself has near 100,000 commits by 2,000 contributors and over a million lines of code.  As one might expect, due to its beginnings from a Sun developer and Oracle's support, most of Jenkins is written in Java with plenty of JavaScript supporting its UI.  
* https://openhub.net/p/jenkins/commits/summary

![](assets/markdown-img-paste-20170909112134478.png)

You can see a peak in Jenkin's development in 2011 when it was officially released.  In coming years, the development pace has stabilized and continued.  In just the last thirty days, an incredible 433 contributors have added to Jenkins.  More surprisingly, 309 of those 433 developers were new contributors.  That statistic was incredibly influential in our decision to chose Jenkins, as Jenkins encourages new users to contribute to the overall project.   
* https://openhub.net/p/jenkins/analyses/latest/languages_summary

![](assets/markdown-img-paste-2017090911553318.png)

The number of commits, as well as the percentage growth of commits, within the last year shows a growing embracement of continuous integration by the developer community.  
* https://openhub.net/p/jenkins/contributors/summary

![](assets/markdown-img-paste-20170909114858928.png)

### Use

Companies that make use of Jenkins include eBay, Cloudera, GitHub, ITA software by Google, LinkedIn, NASA, Netflix, Sony, the UK Government, Yahoo, and many more.  Open source projects currently utilizing Jenkins include Django CMS, Apache, JBoss, Mozilla, SQLAlchemy, and Twitter4J.

### Popularity

Using Google Trends, we can see that Jenkins has continued to gain popularity over the last few years.
![](assets/markdown-img-paste-20170909123303852.png)

Regionally, its popularity has increased overseas with the US being Country #4 in searches including Jenkins.
![](assets/markdown-img-paste-20170909123424538.png)

### Languages used

The application itself has also grown in the last year, adding Groovy, shell script, DOS batch script, JavaScript, and CSS to its arsenal of tools. The top languages used by lines of code are Java, XML, JavaScript & CSS.
* https://openhub.net/p/jenkins/analyses/latest/languages_summary

### Platform

Jenkins largely runs on the JVM, so Jenkins is able to run on any operating system capable of running either Java 8 or Docker.

### Documentation sources

The [Jenkins Documentation Home Page](https://jenkins.io/doc/) provides a great deal of easily accessible information, including a guided tour, user handbook, tutorials, and plenty of other resources.

## License

The Jenkins core is under the [MIT license](https://opensource.org/licenses/MIT), so its software can be used publically or privately, as long as the copyright notice is included in all copies or substantial portions of the software.  This assumes that the client must publically disclose that they've utilized Jenkins within their software project.

## Procedures for making contributions

The options for participating and making contributions to the Jenkins project are well defined at their [participate](https://jenkins.io/participate/) page. A beginner’s guide for new contributors can be found on their [wiki](https://wiki.jenkins.io/display/JENKINS/Beginners+Guide+to+Contributing).

In summary, Jenkins is an open community and encourages newcomers to make contributions.  Jenkins even encourages newcomers to join their bi-weekly project meetings on IRC.

From their website, "Want to get more involved and commit directly to the repository? Just ask! We generally give away the push access very liberally. This process hasn't been fully automated yet, but the intention is that you just need to ask to get the push access."  Given the provided links and website content, our group feels confident in our ability to cnotribute to the project during the course of this semester.
## Contributor agreements
As a contributor, "you activiely help improve Jenkins and plugins by contributing code, documentation, translations, or tests."
* https://jenkins.io/participate/

Committers must sign a contributor’s license agreement (CLA). This provides certain rights to contributors, such as the right to contribute, copyright ownership, and a patent grant. If a company is placing an employee on the project, the company must sign a Corporate CLA.

## Security related history

And while the codebase is considered well established and mature, its history contains more than a few vulnerabilities.  Many of these vulnerabilities can be traced to its rapid development, huge codebase, and possibly superfluous plugins. One of the more recent severe examples was a remote code injection vulnerability affecting the entire Jenkins community earlier this year. Bugs like these can really hurt a community if not handled well because both people and companies falter in their trust for the software.

* https://jenkins.io/security/advisories/
* https://openhub.net/p/jenkins/security
* https://www.cvedetails.com/vendor/15865/Jenkins.html

![](assets/markdown-img-paste-20170909115438746.png)

## Functional security requirements for the software

Jenkins provides a [Standard Security Setup](https://wiki.jenkins.io/display/JENKINS/Standard+Security+Setup) to aid in project configuration. Our group brainstormed [functional security requirements for Jenkins](https://github.com/cpluspluscrowe/SoftwareAssurance/projects/2) during the selection process to ensure Jenkins would be a good choice.

* CSRF Mitigations - Since Jenkins is used through a web interface. Proper precautions should be taken to prevent malicious websites from taking actions on the users behalf. 

* User Authentication - Jenkins can contain sensitive information and allow access to critical systems. Proper authentication mechanisms should be in place to prevent unauthorized actions. 

* Jenkins should not allow deployment of malicious builds - Jenkins needs to be able to detect malicious and vulnerable code. If either of these issues are detected, automatic deployment should not be completed.

* Jenkins should be able to protect Jenkins master from malicious builds - Anyone running Jenkins from the master node can make malicious changes to the project. This may allow undesired Jenkins activity, and potentially malicious changes across an entire organization. 

* Jenkins should allow users to share markdown and html without allowing malicious scripts to run - Scripts unintentionally running is almost always a security risk. Users should be able to access the files they need without fear of running scripts unintentionally.
 

## Motivation for selecting this project

The use of Jenkins-like software is a growing trending for many software engineers.  Such projects are deeply integrated within the software development process (often Agile).  This makes Jenkins a prime candidate for our group to learn from and interact within.  Jenkins is also a colossal code base with a recent history of bugs.  It seems likely that our group will be able to identify security vulnerabilities and push fixes or suggestions to the project. Many in our group are new to software security and look forward to applying newly learned methods of software security engineering to a large project.   

## Team Links

* Project board: https://github.com/cpluspluscrowe/SoftwareAssurance/projects/2
* Jenkins forked: https://github.com/larrysingleton/jenkins

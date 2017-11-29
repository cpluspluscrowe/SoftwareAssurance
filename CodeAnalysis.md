# Jenkins Code Analysis - Team JAX

2-3 page report that describes the following:
* Code review strategy
* Manual code review of critical security functions identified in mis-use cases and threat models.
* Automated code scanning (if available). Include links to full reports.
* Summary of key findings from manual and/or automated scanning. This may include: Categorization, Mappings to CWEs, CAPECs, Risk Levels etc.
* Links to any pull requests, issues, discussion, etc. from the team to the original project and any follow-up interactions.

# Code Review Strategy
The code review strategy being used for this report will be a light weight peer code review process using a Weakness Analysis Review.


# Manual Code Review
The code being reviewed related to CSRF vulnerabilities which related directly back to our mis-use cases and threat models.

## CSRF Code Review
During manual code review for CSRF, our main objective was to find where they did the CSRF token validation for jenkins. This code was found and is located [here](https://github.com/jenkinsci/jenkins/blob/master/core/src/main/java/hudson/security/csrf/DefaultCrumbIssuer.java). The code in question is below:

![CSRF validation](assets/validate_csrf_code.png)

This code brings in the request and checks to see if the current CSRF token string matches the one from the request. If it does, return true, otherwise return false. If the callee returns false, the caller will raise an exception and the request to the server will be cancelled.

## XSS Code(ish) review
XSS prevention is an important part of any solid web app. If XSS prevention falls apart, many other attacks that were mitigated earlier are now null and void becuase XSS can work around them. Jenkins uses a plugin called "jelly" to do its XSS filtering in their templates. Below is a template that they use on their "About Us" page:

![About Us](assets/xss_protection_jelly.png)

This filters variables coming into the template from either user or server side input, assuming 'jelly' is turned on at the top of the document. 

# Summary of Key Findings

## Jenkins.io
During a manual scan of the code and associated documentation, it was discovered that the existing documentation was out of date and needed a little touch up. In this case, the documentation made reference to a CSRF feature being disabled by default. This isn't entirely accurate however. This feature is only disabled by default in Jenkins v1.x and when upgrading to v2.x. If a use initially installed v2.x however, the feature is enabled by default.

* jenkins-infra/jenkins.io pull request [#1233](https://github.com/jenkins-infra/jenkins.io/pull/1233) was submitted on Nov 17, 2017
* The request was accepted and merged into the master branch on Nov 18th, 2017 after a suggested additional change was submitted
* A mention on the course project page has been made: [2017 Hall of Fame](https://robinagandhi.github.io/swa/pages/halloffame.html)
* The updated documentation including the above approved change can be found here:
    * https://jenkins.io/doc/book/system-administration/security/

## Jenkins.core
The jenkinsci/jenkins github project contains the core codebase.
[There's a lot more to the Jenkins project than just code.](https://github.com/jenkinsci/jenkins/blob/master/CONTRIBUTING.md)
The codebase for this project was cloned locally and the project built using maven. Many tests failed, which is to be expected due to specific environmental factors required for some of these cases to be successful.

A small maven script was written so that static code analysis could be run and uploaded to Sonar Cloud. This bypasses all the test runs and configures the sonar plugin to upload the report results to Sonar Cloud for further review and analysis.

```maven
mvn org.jacoco:jacoco-maven-plugin:prepare-agent package sonar:sonar \
    -DskipTests \
    -Dmaven.test.skip=true \
    -Dsonar.host.url=https://sonarcloud.io \
    -Dsonar.organization=larrysingleton-github \
    -Dsonar.login=[insert private sonar cloud token here] \
    -Dsonar.branch=master \
    -Dsonar.cfamily.build-wrapper-output.bypass=true
```

The full [Sonar Cloud Report](https://sonarcloud.io/dashboard?id=org.jenkins-ci.main%3Apom%3Amaster) of the core codebase shows 645 Bugs and 361 Vulnerabilities.

![Sonar Cloud Overview](/assets/SonarCloudOverview.png)

Further investigation into these vulnerabilities, shows that only 9 of them are critical,
however only 1 is related to a java file, the other 8 to javascript eval calls.

![Filtered Report](/assets/FilteredReport.png)

A single item of interest from a vulnerability perspective remains:
![Single Item of Interest](/assets/SingleItemOfInterest.png)

The issue is referenced as [squid:S2976](https://sonarcloud.io/organizations/larrysingleton-github/rules#rule_key=squid%3AS2976) and is contained in [FilePath.java.](https://github.com/jenkinsci/jenkins/blob/master/core/src/main/java/hudson/FilePath.java)
The critical issue is tagged as belonging to [owasp-a9](https://www.owasp.org/index.php/Top_10_2013-A9-Using_Components_with_Known_Vulnerabilities) which is labelled as the **"Top 10 of 2013 - A9 - Using Components with Known Vulnerabilities"**.

After a full review of the vulnerability, it was decided a pull request could be made:
* jenkinsci/jenkins pull request [#3161](https://github.com/jenkinsci/jenkins/pull/3161) was submitted Nov 26, 2017

Additionally, a [PMD](https://cpluspluscrowe.github.io/SoftwareAssurance/pmd_scan.html) scan was run on Jenkins. This scan looked for issues relating to Sun Microsystems security recommendations (now Oracle recommendations).

One of the two primary issues was that arrays were stored directly in other objects. In other words, an array is essentially stored in some newly created objects. The second issues has to do with returning arrays directly. In both cases, the data referenced in the objects can be modified outside the object, and the changes will be reflected in the object. If this is not intended and accounted for, it can lead to bugs and/or security flaws.  

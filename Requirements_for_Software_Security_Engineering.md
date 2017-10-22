![](assets/markdown-img-paste-20170909112804883.png)

- [**Requirements for Software Security Engineering**](#--requirements-for-software-security-engineering--)
  * [Security Requirement Claim 1 and 4](#security-requirement-claim-1-and-4)
    + [Part 1: Assurance Claims](#part-1--assurance-claims)
    + [Part 2: Describe the security requirements for the project captured using mis-use case diagrams.](#part-2--describe-the-security-requirements-for-the-project-captured-using-mis-use-case-diagrams)
    + [Part 3: Review OSS project documentation for alignment of security requirements with advertised features.](#part-3--review-oss-project-documentation-for-alignment-of-security-requirements-with-advertised-features)
    + [Part 4: Summarize your observations](#part-4--summarize-your-observations)
    + [Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.](#part-5--review-oss-project-documentation-for-security-related-configuration-and-installation-issues-summarize-your-observations)
  * [Security Requirement Claim 2](#security-requirement-claim-2)
    + [Part 1: Assurance Claim:](#part-1--assurance-claim-)
    + [Part 2: Describe the security requirements for the project captured using mis-use case diagrams.](#part-2--describe-the-security-requirements-for-the-project-captured-using-mis-use-case-diagrams-1)
    + [Part 3: Review OSS project documentation for alignment of security requirements with advertised features.](#part-3--review-oss-project-documentation-for-alignment-of-security-requirements-with-advertised-features-1)
    + [Part 4: Summarize your observations](#part-4--summarize-your-observations-1)
    + [Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.](#part-5--review-oss-project-documentation-for-security-related-configuration-and-installation-issues-summarize-your-observations-1)
  * [Security Requirement Claim 3](#security-requirement-claim-3)
    + [Part 1: Assurance Claim:](#part-1--assurance-claim--1)
    + [Part 2: Describe the security requirements for the project captured using mis-use case diagrams.](#part-2--describe-the-security-requirements-for-the-project-captured-using-mis-use-case-diagrams-2)
    + [Part 3: Review OSS project documentation for alignment of security requirements with advertised features.](#part-3--review-oss-project-documentation-for-alignment-of-security-requirements-with-advertised-features-2)
    + [Part 4: Summarize your observations](#part-4--summarize-your-observations-2)
    + [Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.](#part-5--review-oss-project-documentation-for-security-related-configuration-and-installation-issues-summarize-your-observations-2)
  * [Security Requirement Claim 5](#security-requirement-claim-5)
    + [Part 1: Assurance Claim:](#part-1--assurance-claim--2)
    + [Part 2: Describe the security requirements for the project captured using mis-use case diagrams.](#part-2--describe-the-security-requirements-for-the-project-captured-using-mis-use-case-diagrams-3)
    + [Part 3: Review OSS project documentation for alignment of security requirements with advertised features.](#part-3--review-oss-project-documentation-for-alignment-of-security-requirements-with-advertised-features-3)
    + [Part 4: Summarize your observations](#part-4--summarize-your-observations-3)
    + [Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.](#part-5--review-oss-project-documentation-for-security-related-configuration-and-installation-issues-summarize-your-observations-3)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# **Requirements for Software Security Engineering**

Lucidchart link to mis-use case:  [Mise-use case link](https://www.lucidchart.com/documents/edit/fd7c6a2d-548b-40f9-8d09-45d134f69ed8/0)

## Security Requirement Claim 1 and 4 
* Larry S and James P

### Part 1: Assurance Claims
* Jenkins provides an acceptable level of protection from Cross Site Request Forgery (CSRF) attacks
* Jenkins adquately filters user input to prevent reflected XSS 

### Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

### Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

### Part 4: Summarize your observations

### Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.



## Security Requirement Claim 2
* Chad Crowe

### Part 1: Assurance Claim:

* Jenkins software provides sufficient support (out of the box) to adequately isolate the master node from unwanted access and malicious scripts 

### Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

### Part 3: Review OSS project documentation for alignment of security requirements with advertised features.

From the OSS website, "Historically, Jenkins master and slaves behaved as if they altogether form a single distributed process. This means a slave can ask a master to do just about anything within the confinement of the operating system, such as accessing files on the master or trigger other jobs on Jenkins.
This has increasingly become problematic, as larger enterprise deployments have developed more sophisticated trust separation model, where the administators of a master might take slaves owned by other teams. In such an environment, slaves are less trusted than the master."

"Starting 1.587 (and 1.580.1), Jenkins added a subsystem to put a wall between a master and a slave to safely allow less trusted slaves to be connected to a master."

"This subsystem is turned off by default for backward compatibility, but if you fit the "larger enterprise deployment" description above, then we highly recommend you turn this mode on. "

Example of when I would want this security feature enabled: "You have some jobs that are configured to run on a specific slave because it is sensitive"

Security Exceptions Jenkins throws for this feature:

    java.lang.SecurityException: slave may not create /var/lib/jenkins/foo/.bar/...
    See http://jenkins-ci.org/security-144 for more details

    java.lang.SecurityException: Sending org.jenkinsci.plugins.gitclient.CliGitAPIImpl$GetPrivateKeys from slave to master is prohibited.
    See http://jenkins-ci.org/security-144 for more details
    
There are three ways to enable this security setting.  

* web UI at http://jenkins/configureSecurity

* Through file system, create or edit the file $JENKINS_HOME/secrets/slave-to-master-security-kill-switch so that it contains false

* Using a Groovy Hook Script and doing something like this:

        import jenkins.security.s2m.AdminWhitelistRule
        import jenkins.model.Jenkins
        Jenkins.instance.getInjector().getInstance(AdminWhitelistRule.class).setMasterKillSwitch(false)
    
File access rules
File access request from slaves is tested against the rules you specify. Each rule is a tuple that consists of:
allow/deny: if the following two parameters match the current request being considered, an "allow" entry would allow the request to be carried out and a "deny" entry would deny the request to be rejected, regardless of what later rules might say.
operation: the type of the operation requested. The following 6 values exist. You can also list them separating with ',' or use "all" to indicate a match to all operations:
read: read file content or list directory entries
write: write file content
mkdirs: create a new directory
create: create a file in an existing directory
delete: delete a file or directory
stat: read metadata of a file/directory, such as timestamp, length, file access modes.
file path: regular expression that specifies file paths that matches this rule. In addition to the base regexp syntax, it supports the following tokens:
<JENKINS_HOME> can be used as a prefix to match your $JENKINS_HOME directory
<BUILDDIR> can be used as a prefix to match your build record directory, such as /var/lib/jenkins/job/foo/builds/2014-10-17_12-34-56
<BUILDID> matches the timestamp-formatted build IDs, like 2014-10-17_12-34-56.
The rules are ordered, and applied in that order. The earliest match wins. So for example, the following rules allow access to $JENKINS_HOME except its secrets folders:

When marking Callable for slave → master, a care has to be taken to ensure that the implementation is not exploitable by malicious slaves.
A malicious slave controls the Java serialization payload, so when your Callable gets deserialized on the master, all the serialized fields are controlled by the slave.
A slave does not control class definitions on the master, so you can trust all the classes and methods to behave as it is written. It is not possible for a malicious slave to change the code executed on the master.

For example, the following SlaveToMasterCallable is exploitable. Callable itself is not public, but a malicious slave can send in arbitrary path, so it can be used to read any file on the master:

	// UNSAFE
	class SomeCodeThatRunsOnSlave {
	    void readBackSomeFileFromMaster() {
	        final String path = "...";
	        channel.call(new SlaveToMasterCallable<String,IOException>() {
	            public String call() {
	                return FileUtils.readFileToString(new File(path));
	            }
	        });
	    }
	}
Callable that delegates execution to deserialized object is dangerous and needs to be carefully examined, because a malicious slave can designate unintended Runnable object:

	// UNSAFE
	class MyCallable extends SlaveToMasterCallable<Void> {
	    Runnable r;
	    public Void call() {
	        r.run();
	        return null;
	    }
	}
To avoid this hassle entirely, consider rewriting your code not to call back to a master from a slave. Instead, when a master first sends a command to a slave, you can carry all the data you'll need with you. This may not be always possible or practical, but it's a lot easier to secure.

whitelisting commands: Currently whitelisted commands: See above for what this field means.
Currently rejected commands: This section lists unclassified commands that Jenkins has actually rejected. You can check boxes and submit them to have Jenkins write them into the "currently whitelisted commands" section. Be careful when you do this, though. See the command whitelisting discussion above for the implications.
File access rules: See above for what this field means.

Whitelisting a command requires not only verifying that the command is intended to be used in this direction, but also that the command implementation is not exploitable by malicious slaves. This requires careful analysis of the source code, taking such things into account as all possible serializable fields.

### Part 4: Summarize your observations

To avoid getting affected by file access rules, have the master work on files of a slave, instead of the other way around.

So when a slave requests a master to execute a command and if it is not classified explicitly as intended for slave → master, Jenkins will err on the side of caution and refuses to execute the command.

### Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

* plugin issues

For the access control to work without requiring manual intervention by users, plugins need to classify their Callable and FileCallable objects whether they are meant to be run on a master or on a slave.
For this purpose, the remote library has added the RoleSensitive interface that has a new checkRoles() method. Callable, FileCallable, and other similar interfaces now extend from this interface. So if you are directly implementing Callable, you will get an error saying that you have unimplemented abstract methods.
The easiest way to fix this is by extending from MasterToSlaveCallable, to indicate that your Callable is only meant to be sent from a master to a slave, or SlaveToMasterCallable, to indicate that your Callable is meant to be sent from a slave to a master. Note that SlaveToMasterCallable can still be executed on a slave, as slaves do not perform this access control check. FileCallable similarly has MasterToSlaveFileCallable and SlaveToMasterFileCallable.

## Security Requirement Claim 3
* Dan R

### Part 1: Assurance Claim:

* Jenkins authentication mechanisms are sufficient to prevent malicious users from gaining access to the system

### Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

### Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

### Part 4: Summarize your observations

### Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

## Security Requirement Claim 5 
* all


### Part 1: Assurance Claim:

* Jenkins adequately secures files to prevent unauthorized file accesses

### Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

### Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

### Part 4: Summarize your observations

### Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.


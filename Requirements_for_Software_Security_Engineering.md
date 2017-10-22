![](assets/markdown-img-paste-20170909112804883.png)

- [- **Requirements for Software Security Engineering**](#----requirements-for-software-security-engineering--)
  * [Security Requirement Claim 1](#security-requirement-claim-1)
      - [(Larry Singleton)](#-larry-singleton-)
  * [Security Requirement Claim 2](#security-requirement-claim-2)
      - [(Chad Crowe)](#-chad-crowe-)
  * [Security Requirement Claim 3](#security-requirement-claim-3)
      - [(Dan R)](#-dan-r-)
  * [Security Requirement Claim 4](#security-requirement-claim-4)
      - [(James P)](#-james-p-)
  * [Security Requirement Claim 5](#security-requirement-claim-5)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


# - **Requirements for Software Security Engineering**

## Security Requirement Claim 1

#### (Larry Singleton)

Part 1: Assurance Claim:

Jenkins provides an acceptable level of protection from Cross Site Request Forgery (CSRF) attacks 

Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

![](assets/JAX_Misuse_Diagrams1And4.png)

Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

Part 4: Summarize your observations

Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

Lucidchart link to mis-use case:  [Mise-use case link](https://www.lucidchart.com/documents/edit/fd7c6a2d-548b-40f9-8d09-45d134f69ed8/0)

## Security Requirement Claim 2

#### (Chad Crowe)

Part 1: Assurance Claim:

Jenkins software provides sufficient support (out of the box) to adequately isolate the master node from unwanted access and malicious scripts 

Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

![](assets/JAX_Misuse_Diagrams2.png) 

Part 3: Review OSS project documentation for alignment of security requirements with advertised features.

Jenkin's nodes behave as a single distributed process.  As a consequence, the slave and master both perform similar and widely-varying processes, e.g. accessing files and triggering jobs.  This works within smaller projects.  Larger models require a more administration, i.e. a separation of trust. In these cases, the master is controlled by an administrator and slaves are designated to teams.  This designates the master as more trustworthy than any slave.  In Jenkins, this is a feature that can be enabled.

There are multiple reasons for enabling this feature. Consider the scenario where sensitive information needs is running on the master.  Without such a separation, malicious slaves might access this information. Once this setting is enabled, the slave will be unable to execute code on the master node.

If enabled, when a slave -> master request occurs, unless the command has been explicitly classified as intended for slave -> use, Jenkins will err on the side of caution and refuses to execute the command.

Jenkins even throws security exceptions to prevent undesirable node/information access.   

    java.lang.SecurityException: slave may not create file on the master
    See http://jenkins-ci.org/security-144 for more details

    java.lang.SecurityException: Sending org.jenkinsci.plugins.gitclient.CliGitAPIImpl$GetPrivateKeys from slave to master is prohibited.
    See http://jenkins-ci.org/security-144 for more details

As of version 1.587 Jenkins has added an optional security wall between the master and slaves.  Unfortunately, the subsystem is turned off by default (for backward compatibility).  Fortunately, there are a few ways to enable this setting:

* web UI at http://jenkins/configureSecurity

* Through file system, create or edit the file $JENKINS_HOME/secrets/slave-to-master-security-kill-switch so that it contains false

* Using a Groovy Hook Script and doing something like this:

        import jenkins.security.s2m.AdminWhitelistRule
        import jenkins.model.Jenkins
        Jenkins.instance.getInjector().getInstance(AdminWhitelistRule.class).setMasterKillSwitch(false)
    
The OSS documentation mentioned File access rules.  Jenkins lets user specify file access rules.  These rules are specified via tuples for read, write, creating directories/files, deleting directories/files, and retrieving node stats. These rules are then applied to files.  Regular expressions are used to map each rule to certain files.  

Slaves should not have access to the master's files.  Otherwise, the slave might access and even alter sensitive/imperative data on the mater.  Instead, the master should work on files of a slave.  

Slave -> master code execution is also discouraged, since it opens up possibilities of slave -> master exploitation.  Unfortunately, by default Jenkins allows slave -> master execution. Extra caution must be taken in such cases, because this configuration makes slave -> master exploitations more likely.  If a malicious slave controls a Java serialization payload, then when this code is deserialized on the master each field will be controlled by the slave, i.e. the slave is now changing code within the master node.  A vulnerability could also appear if the slave is allowed to execute certain portions of code on the mater.  In the below code a slave could supply any code to the master's function and therefore read any file on the master:

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
To avoid such hassles, one may rewrite code to not call back to a master from a slave. Instead, supply the slave with all the data it needs so no callbacks are necessary. This hierarchy of calling from master -> slave may not always be possible/practical, but it's much more secure.

Jenkins allows for command whitelisting to guarantee only certain commands are run on its nodes.  One can also specify currently rejected commands.  Whitelisting still requires a lot of verification that such a command it not exploitable by malicious slaves.  This requires careful analysis of what source code is being run by such commands.  One must also take into account all possible serialization fields (which the slave controls in slave -> master execution).  

Part 4: Summarize your observations

Slave -> master code access/execution provides many opportunities for exploitation. Security must be set to prevent any slave -> master file access or code execution. It is best to give the slave no permissions on the master. 

Jenkins provides a security feature which prevents slave -> master file access/code execution.  Yet, this feature is disabled by default for backward compatibility.  This feature is also disabled because it largely hinders usability on smaller/one-node projects. 

Jenkins provides node-to-node permissions at the file granularity.  This can be used to secure sensitive information and prevent malicious local file execution.


Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

plugin issues:

For the access control to work without requiring manual intervention by users, plugins need to classify their Callable and FileCallable objects whether they are meant to be run on a master or on a slave.
For this purpose, the remoting library has added the RoleSensitive interface that has a new checkRoles() method. Callable, FileCallable, and other similar interfaces now extend from this interface. So if you are directly implementing Callable, you will get an error saying that you have unimplemented abstract methods.
The easiest way to fix this is by extending from MasterToSlaveCallable, to indicate that your Callable is only meant to be sent from a master to a slave, or SlaveToMasterCallable, to indicate that your Callable is meant to be sent from a slave to a master. Note that SlaveToMasterCallable can still be executed on a slave, as slaves do not perform this access control check. FileCallable similarly has MasterToSlaveFileCallable and SlaveToMasterFileCallable.

Lucidchart link to mis-use case:  [Mise-use case link](https://www.lucidchart.com/documents/edit/fd7c6a2d-548b-40f9-8d09-45d134f69ed8/1)

## Security Requirement Claim 3

#### (Dan R)

Part 1: Assurance Claim:

Jenkins authentication mechanisms are sufficient to prevent malicious users from gaining access to the system

Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

![](assets/JAX_Misuse_Diagrams3.png) 

Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

Part 4: Summarize your observations

Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

Lucidchart link to mis-use case:  [Mise-use case link](https://www.lucidchart.com/documents/edit/fd7c6a2d-548b-40f9-8d09-45d134f69ed8/2)

## Security Requirement Claim 4 
#### (James P)

Part 1: Assurance Claim:

Jenkins adquately filters user input to prevent reflected XSS 

Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

![](assets/JAX_Misuse_Diagrams1And4.png)

Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

Part 4: Summarize your observations

Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

Lucidchart link to mis-use case:  [Mise-use case link](https://www.lucidchart.com/documents/edit/fd7c6a2d-548b-40f9-8d09-45d134f69ed8/0)

## Security Requirement Claim 5 
#### (all)

Part 1: Assurance Claim:

Jenkins adquately secures files to prevent unauthorized file accesses

Part 2: Describe the security requirements for the project captured using mis-use case diagrams. 

![](assets/JAX_Misuse_Diagrams5.png) 

Part 3: Review OSS project documentation for alignment of security requirements with advertised features. 

Part 4: Summarize your observations

Part 5: Review OSS project documentation for security related configuration and installation issues. Summarize your observations.

Lucidchart link to mis-use case:  [Mise-use case link](https://www.lucidchart.com/documents/edit/fd7c6a2d-548b-40f9-8d09-45d134f69ed8/3)


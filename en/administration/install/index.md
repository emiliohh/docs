% Installation

### System Architecture

Rundeck is a server application you host on a system you designate 
a central administrative control point. Internally, Rundeck stores job
definitions and execution history in a relational database. Output
from command and job executions is saved on disk but can be forwarded
to remote stores like S3 or Logstash. 

Rundeck distributed command execution is performed using a pluggable
node execution layer that defaults to SSH but plugins allow you
to use other means like MCollective, Salt, WinRM, or your custom method. 
Rundeck server configuration includes settings to define the outbound
user allowed by the remote hosts. Remote machines
are not required to make connections back to the server.

![Rundeck architecture](../../figures/fig0001.png)

The Rundeck application itself is a Java-based webapp. The application provides both
graphical interface and network interfaces used by the Rundeck shell
tools. 

Access to the Rundeck application requires a login and
password. The default Rundeck installation uses a flat file user
directory containing a set of default logins. Logins are defined in
terms of a username and password as well as one or more user
groups. An alternative configuration to the flat file user directory,
is LDAP (e.g., ActiveDirectory) but Rundeck authentication and authorization
is customizable via [JAAS](https://en.wikipedia.org/wiki/Java_Authentication_and_Authorization_Service).
Users must also be authorized to perform actions like define a job
or execute one. This is controlled by an access control facility that reads
policy files defined by the Rundeck administrator. Privilege is
granted if a user's group membership meets the requirements of the policy.

Two installation methods are supported:

* System package: RPM and Debian packaging is intended for managed installation and provides
  robust tools that integrate with your environment, man pages, shell
  tool set in your path, init.d startup and shutdown.
  
* Launcher: The launcher is intended for quick setup, to get you
  running right away.  Perfect for bootstrapping a project or trying
  a new feature.  

Rundeck can also install as a WAR file into an external container like Tomcat.


Assuming the system requirements are met, Rundeck can be installed
either from source, system package or via the launcher.

### System Requirements

The following operating systems are known to support Rundeck:

* Linux: Most recent distributions are likely to work 
* Windows: XP, Server and above
* Mac OS X 10.4 or later

Root (or Administrator on Windows) is not required or recommended. We
recommend using a dedicated user account such as "rundeck".

If there is need for root access, please set up the Rundeck user
to have access via [sudo].

[sudo]: https://en.wikipedia.org/wiki/Sudo

FIXME: needs cleanup

* __Supported Operating Systems:__
	* Red Hat Enterprise Linux
	* CentOS
	* Ubuntu
	* Windows Server
* __A currently supported version of Mozilla Firefox or Google Chrome__
	* Other HTML5 compliant web browsers may work but are not fully tested or supported.
* __2 CPUs__
	* 2 CPUs per member
* __4 GB RAM__
	* other notes about memory sizing
* __20 GB hard disk__
	* other notes about disk sizing
* __Database__
	* Mysql version
	* Mariadb version
	* Postgres version
	* Oracle version
* __Log store__
	* File system
	* S3 compatible object store
* __Amazon EC2__
	* Instance size of m3.medium or larger
	* An instance size of m3.xlarge or larger if there are more than 100 hosts

#### Java

Rundeck is a Java-Servlet based server and therefore requires the Java
runtime.

The install process requires that the latest version of Java 8
be installed. Both the [Open JDK](http://openjdk.java.net/) and [Sun/Oracle](https://java.com/) JVMs can be used.
You must have the JAVA_HOME environment variable defined
in your environment before running the launcher.  The RPM will use the java found on your path. See [Setting JAVA_HOME](../maintenance/startup-and-shutdown.html#setting-java_home) if you want to run a different version of java.

Verify your Java version to check it meets the requirement:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.bash}
$ java -version
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 

#### Network access

When the server starts, it binds to several TCP ports by default:

*  4440 (http) 
*  4443 (https)

To check if the ports are free on a Unix host, run:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.bash}
$ netstat -an | egrep '4440|4443' 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the ports are in use on the server, you will see output similar to below:

    tcp46      0      0  *.4440                 *.*                    LISTEN

The installation procedures describe how to choose different ports, if
there is a conflict.
    
In addition, TCP port 22 (by default) needs to be open on the clients for SSH.
    
Clients should be set up to allow the Rundeck server user to connect to
the clients using SSH via public-key authentication. It should not
prompt for a password. See
[Configure remote machine for SSH](../../manual/node-execution/ssh-node-execution.html#configuring-remote-machine-for-ssh)
in the Administration chapter.

There are various ways for installing SSH on Windows; we recommend
[Cygwin].

[Cygwin]: https://www.cygwin.com/
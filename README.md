[![Build Status](https://travis-ci.org/alexey-kolonitsky/komander.svg?branch=master)](https://travis-ci.org/alexey-kolonitsky/komander) [![codecov](https://codecov.io/gh/alexey-kolonitsky/komander/branch/master/graph/badge.svg)](https://codecov.io/gh/alexey-kolonitsky/komander) [![Javadocs](https://www.javadoc.io/badge/com.kolonitsky/komander.svg)](https://www.javadoc.io/doc/com.kolonitsky/komander) 

Komander is a simple easy to use framework to develop command line application
with a solution for common issues.

Komander designed to help developer split functionality to different commands
inside the application and decouple them from each other. The main tool of 
decoupling is a Dependency Injection and Command pattern. So if you don't like 
Design patterns don't use Komander.

Advanages
---------
1. Decoupling the code of differenct commands - each command line sub-command 
  invoke own Command class in application.
  
2. Processing command line arguments -- Komander create instance of Command only 
  if requested sub-command has been found and input parameters will be matched to 
  the command input. Otherwise Komander will print friendly message about mistake 
  in command input.
  
3. Documentation automaticaly generated based on commands input declatation.

4. Common approach for configurations

This is a four aims I chased when creaed this framework. 

Definitions
-----------
**sub-command** -- is first argument of the command line application. As example 
`git pull` is command there are `pull` is a sub-command.
 
**argument** -- means sub-command argument, is a parameters which user must define
when call the command. 

**flag** or **switcher** -- means special type of input parameter without value
used only to switch on/off some additional functionality or switch command workflow.
As example one of the popular switcher available in lots of command line tools
`--verbose` which turn on more logs to the command output.

This is the simplies way to generalize command line interface and this siplitity 
allow to siplify **Komander** usage and extension.

Setup
=====
To integrate Komander to your application just add maven dependency 
```       
 <dependency>
   <groupId>com.kolonitsky</groupId>
   <artifactId>komander</artifactId>
   <version>1.0-SNAPSHOT</version>
 </dependency>
```

or download and put ``komander.jar`` file to the CLASSPATH

How to Use
==========

1. First of all you have to write your code in command classes which implements
IKommand interface.
2. Then you have to create instance of `Komander` class, with collection of all
commands which your will use.
3. At least you have to redirect command line input to the `Kommander.run()` input.

Configuration
=============
On of the advantages using Komander is a configuration from external file out of 
the box. A lot of small command line tools used hardcoded values to speedup 
programming process. Komander suggest extreamly easy to use confiration.

  1. Define file with the properties somewhere.
```properties
project.id=myProject
project.version=1.0.0
```

  2. Create instance of configurator which will load your file.
  3. Register configurator in Komander
  4. Inject your property to the any command

```java
import com.kolonitsky.komander.*;

public class Main {
    public static void main(String[] args) throws Exception {
        // 2.
        Configurator c = new Configurator();
        c.readConfig("tool.properties");
        
        Komander k = new Komander();
        k.addConfigurator(c);
        k.register(Echo.class);
        k.run("echo");
    }
}

class Echo implements BaseKomand {
    // 4.
    @Injected(config="project.id")
    public String id;
    
    public Echo() {
        _name = "echo";
    }
    
    public void run(){
        System.out.print("Hello world: " + id);
    }
}
```

One registered configurator you can inject any property to any command you want.


How to Extends
==============

Input
=====
Command line allow to create to various input interface and a lot of tools use 
their own format 

* Arguments
* Flags


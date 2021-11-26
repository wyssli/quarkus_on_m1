# quarkus_on_m1
A tutorial for getting Quarkus running on M1 Mac

## What's the goal
The goal is to go trough the Quarkus Tutorial on a M1 Mac.
https://quarkus.io/get-started/
and
https://quarkus.io/guides/getting-started

## Why a tutorial when it's only a few steps
When it took me 10 minutes to do step 2 of the tutorial: "You need a a JDK 11+ (any distribution)" I thought it might be helpful for others (and for future me) to write down my steps.

## My style: 
- I try to do this as standard and as clean as possible.
- most importantly I want everything to be native M1
- I love brew, because I think it's a great way to get a reproducable install on an other machine and have an orderly way for updating your packages
- however I'm a minimalist and a pracgatic mind. 
  - The first one prevents me from creating brew packages (I'm sop not interessted in doing this that I don't even know if it is actually called a 'package') 
  - The second one makes me belive that for example intelliJ is better installed directly from JetBrains as it has it's own update mechanism built in and I fear that this will cause trouble with brew
  - as the last point shows I don't have to know things for certain, a lot of times I follow my 30+ years experience with computers
  - as the last point shows: I try to be as honest as possible and therefore:

> ***DISCLAIMER***
> - I take no responsability for anything that happens if you follow this tutorial!
> - If you follow this guide you are responsible for all that happens
> - Don't switch off your mind
> - I promise that I also document things that did not work *on my installation*
> - all information is too my best knowledge. if you find any errors please open an Issue on githu
> - if I write "M1" I actually mean "Apple Silicon"

# Preconditions
1. `brew` (The Missing Package Manager for macOS) https://brew.sh
2. an IDE of you choice (e.g. there is a M1 dmg on https://www.jetbrains.com/idea/download/#section=mac)
3. ***very important*** iI assume there is no Java installed on your machine
4. knowledge on Java and how to handle your IDE and zsh in a `Terminal`
5. a directory "Development" in your home (`cd` and `mkdir Development`)

# Step 1: "You need an IDE"
As said above: it is a precondition that you already have one. If not and you are lost: get IntelliJ (see above)

# Step 2: "You need a a JDK 11+ (any distribution)"
The situation on 2021.11.26 is that there is currently no M1 native JVM from Oracle (HotSpot) or from Eclipse (OpenJ9). If there was one I would take one of them, probably OpenJ9. But thanks to the nice people at azul.com we have a solution:

Zulu: 17.30.15
https://www.azul.com/downloads/?os=macos&architecture=arm-64-bit&package=jdk

- Download the DMG Installer (trust me on this one, like this java will be on the path and it will be like this even if brew installs an additional java)
- use `Finder` to move it from the Downloads directory to an other directory (e.g. a directory in your home directory)
- open `Temrinal` and `cd` to that directory  
- check the SHA
```
shasum -a 256 zulu17.30.15-ca-jdk17.0.1-macosx_aarch64.dmg
```
- double click the DMG
- follow the instructions to install azul
- on the `Temrinal` check if the right java is on the path:
```
$ java --version
openjdk 17.0.1 2021-10-19 LTS
OpenJDK Runtime Environment Zulu17.30+15-CA (build 17.0.1+12-LTS)
OpenJDK 64-Bit Server VM Zulu17.30+15-CA (build 17.0.1+12-LTS, mixed mode, sharing)
```


Note: I'm in for the newest so I take the newest version of Java 17 (LTS) and you could also take 15, 13 or 11 LTS.

# Step 2b: "Optionally get GraalVM 21.3.0 for native compilation"
[TBD] currently there is no M1 version, from other comments on the net I guess the AMD64 will run.

# Step 3: "You need Apache Maven 3.8.1+ or Gradle"
I'll go with maven
```    $ brew install maven 
```

Note: this will install an additional `openjdk` (I asume it's a AMD64 Version) and we have to make sure that maven doesn't take this:
- on the `Temrinal` set JAVA_HOME to azul:
```
➜  ~ nano ~/.zshenv
```
- add the following content `export JAVA_HOME=$(/usr/libexec/java_home)` and press control-o and control-x to write and exit.
- source the file and check $JAVA_HOME
```
➜  ~ source ~/.zshenv

➜  ~ echo $JAVA_HOME 
/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home

```
- on the `Temrinal` check if maven uses the right java:
```
$ mvn --version
Apache Maven 3.8.4 (9b656c72d54e5bacbed989b64718c159fe39b537)
Maven home: /opt/homebrew/Cellar/maven/3.8.4/libexec
Java version: 17.0.1, vendor: Azul Systems, Inc., runtime: /Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home
Default locale: en_GB, platform encoding: UTF-8
OS name: "mac os x", version: "12.0.1", arch: "aarch64", family: "mac"
```

# Step 4: "Start Coding with Quarkus"

we jump now to the tutorial at https://quarkus.io/guides/getting-started
You can follow from the beginning but I jump to paragraph 4

## 4 Bootstrapping the project
- on the `Terminal` create the tutorial project
```
➜  ~ cd
➜  ~ cd Development 
➜  Development mkdir quarkus_tutorials
➜  Development cd quarkus_tutorials 
➜  quarkus_tutorials mvn io.quarkus.platform:quarkus-maven-plugin:2.5.0.Final:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=getting-started \
    -DclassName="org.acme.getting.started.GreetingResource" \
    -Dpath="/hello"
[INFO] Scanning for projects...
Downloading from central: https://rep
[... things deleted...]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  16.161 s
[INFO] Finished at: 2021-11-26T23:43:09+01:00
[INFO] ------------------------------------------------------------------------
➜  quarkus_tutorials cd getting-started 
```
## 5 Running the application
- build and run the project in dev mode
```
➜  getting-started ./mvnw compile quarkus:dev
```
- press `r`vto run the tests
- open a second `Terminal` window and test if the REST endpoint is working:
```
➜  ~ curl -w "\n" http://localhost:8080/hello
Hello RESTEasy
```
- yippee-ki-yay it's working (see https://theweek.com/articles/462065/brief-history-yippeekiyay)
- NOTE: Quarkus ships with a Dev UI, which is available in dev mode only at http://localhost:8080/q/dev/.
- Now open the project in your IDE
  - open IntelliJ
  - click the `Open` Button
  - navigate to `~/Development/quarkus_tutorials/getting-started`
  - and click the `Open` Button
- open everything in the Project view and peek around
- find `GreetingResource.java` and edit the returned String from `Hello RESTEasy` to `Hello World` and save
- marvel at how quickly it recompiles and gets the changes running once you do another `curl -w "\n" http://localhost:8080/hello`

#THE END

from here go and have fun


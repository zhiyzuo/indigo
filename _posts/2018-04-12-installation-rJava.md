---
title: "Installing rJava on macOS"
layout: post
date: 2018-04-12 23:55
headerImage: false
tag:
- macOS
- r
- java
category: blog
hidden: false
author: zhiyzuo
description: A brief note on how to install rJava on macOS
---

[`rJava`](http://www.rforge.net/rJava/) is often needed for tasks like reading Excel files or connecting to databases. However, installation via `install.packages('rJava')` on macOS often leads to the following problems when trying to use it:

```r
Error: package or namespace load failed for ‘rJava’:
 .onLoad failed in loadNamespace() for 'rJava', details:
  call: dyn.load(file, DLLpath = DLLpath, ...)
  error: unable to load shared object '/Library/Frameworks/R.framework/Versions/3.4/Resources/library/rJava/libs/rJava.so':
  dlopen(/Library/Frameworks/R.framework/Versions/3.4/Resources/library/rJava/libs/rJava.so, 6): Library not loaded: @rpath/libjvm.dylib
  Referenced from: /Library/Frameworks/R.framework/Versions/3.4/Resources/library/rJava/libs/rJava.so
  Reason: image not found
```

### `rJava` works in R but not RStudio
In this case, try the following code in RStudio:
```r
> dyn.load('/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home/jre/lib/server/libjvm.dylib')
```

Note that you need to confirm the version of your JVM first. You can do this by running the following in [terminal](https://support.apple.com/guide/terminal/access-the-shell-apd5265185d-f365-44cb-8b09-71a064a42125/mac):

```bash
~$ ls /Library/Java/JavaVirtualMachines/
```

Mine gives me the following output because I have both Java 8 and 9:
```bash
jdk-9.0.1.jdk    jdk1.8.0_162.jdk
```

After running `dyn.load()` in RStudio, trying loading `rJava` and it should work now. If not, try the following steps.


### Installing Java, `jenv`, and `rJava` from scratch
The following steps may help to resolve this if you do not have Java installed. ___The final solution is inspired by [@tractatus](https://github.com/tractatus)'s answer to [this issue](https://github.com/velocyto-team/velocyto.R/issues/2).___

0. Open the [terminal](https://support.apple.com/guide/terminal/access-the-shell-apd5265185d-f365-44cb-8b09-71a064a42125/mac)

1. Install [Homebrew](https://brew.sh/) if you haven't. Run the following in terminal:
```bash
~$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
 It will ask you to enter your password to the computer. Nothing will show up when you type for security reasons. After you enter the password, just hit enter to continue. After installation, run the following to install a compiler system [`gcc`](https://github.com/gcc-mirror/gcc):
```bash
~$ brew install gcc
```

2. Run `java -version` to check Java version. It will produce results similar to the following:
```bash
~$ java -version
java version "1.8.0_162"
Java(TM) SE Runtime Environment (build 1.8.0_162-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.162-b12, mixed mode)
```
 If Java version is above 8 (e.g., 9 or 10), I recommmend installing Java 8 and Java environment management tool [`jenv`](http://www.jenv.be/) via brew:
```bash
~$ brew tap caskroom/versions
~$ brew cask install java8
~$ brew install jenv
~$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
~$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile
~$ source ~/.bash_profile
```
 The next thing we need to do is to use Java 8. Here we will set Java 8 ___gloablly___ so that the Java home (or path) will be pointing to the one we just installed via brew. Suppose your path to Java 8 is located at `/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/` (you can just check your own and edit the version here) and do:
```bash
~$ jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home
oracle64-1.8.0_162 added
~$ jenv global oracle64-1.8.0_162
```
 Finally, check Java version again in terminal by `java -version` to make sure we set it correctly.

3. Now we change the compiler that R uses:
```bash
~$ mkdir .R
~$ cd .R
~$ touch Makevars
~$ open Makevars
```
 TextEdit will pop up. Copy the following contents and save:
```
CC=/usr/local/Cellar/gcc/7.2.0/bin/gcc-7
CXX=/usr/local/Cellar/gcc/7.2.0/bin/g++-7
CXX11=/usr/local/Cellar/gcc/7.2.0/bin/g++-7
CXX14=/usr/local/Cellar/gcc/7.2.0/bin/g++-7
cxx17=/usr/local/cellar/gcc/7.2.0/bin/g++-7
cxx1X=/usr/local/cellar/gcc/7.2.0/bin/g++-7
LDFLAGS=-L/usr/local/Cellar/gcc/7.2.0/lib
```
 Note that you may need to change the version from `7.2.0` to something else depending on what your `gcc` version is. You can check this by running `ls /usr/local/Cellar/gcc/` in terminal (this may not be the best way but it works :eyes:).

4. Now we will install `rJava` in R. Before that, run `R CMD javareconf` in terminal. If it asks you to enter `y/n`, just type `y` and hit <kbd>enter</kbd>. Now go to R/RStudio, run `install.packages('rJava', type='source')`. The installation should run smoothly.

5. Finally, we can test if it works. A simple way here (in R/RStudio) NOT terminal :
```r
> library(rJava)
> .jinit()
```

6. If this only works in R but not RStudio, try to run the command `dyn.load` as mentioned above.

If the error mentioned above does not show up, then we are good!

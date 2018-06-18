---
title: "Installing Rattle on macOS"
layout: post
date: 2018-01-20 22:00
headerImage: false
tag:
- macOS
- rattle
category: blog
hidden: false
author: zhiyzuo
description: A brief note on how to install rattle/RGtk2 on macOS
---

### Installing Rattle on MacOS 10.11 (or above)

Guys, great news! Follow [Yihui's comment](http://disq.us/p/1t8amd6) below and use the 20-line code to install `Rattle` without any effort! Just copy and paste the codes in R/RStudio - it just works like a charm!

I copy Yihui's code below:

```R
system('brew install gtk+')

local({
  if (Sys.info()[['sysname']] != 'Darwin') return()

  .Platform$pkgType = 'mac.binary.el-capitan'
  unlockBinding('.Platform', baseenv())
  assign('.Platform', .Platform, 'package:base')
  lockBinding('.Platform', baseenv())

  options(
    pkgType = 'both', install.packages.compile.from.source = 'always',
    repos = 'https://macos.rbind.org'
  )
})

install.packages(c('RGtk2', 'cairoDevice', 'rattle'))
```

<div class="breaker"></div>

This document describes steps to successfully install `Rattle` [R Analytic Tool To Learn Easily](https://cran.r-project.org/web/packages/rattle/index.html) on macOS 10.11 or above. If you have a macOS lower than 10.11, I recommend you upgrade your system to the newest one ([10.12 macOS Sierra](https://itunes.apple.com/us/app/macos-sierra/id1127487414?mt=12) using `App Store`. Without macOS 10.11 or above, you cannot install `R 3.4`, let alone `RGtk2`, which now only supports the newest `R` version.

Throughout the steps to get your `Rattle` working, we will be using `Terminal`. You can open it by using [`Spotlight Search`](https://support.apple.com/en-us/HT204014) (you can open it by <kbd>command</kbd>+<kbd>space</kbd>, which is the default shortcut). Type `terminal` and you will be able to find it and open it.

___Note that starting from `step 7`, I mainly followed instructions on `@williamtellme123`'s [comment](https://gist.github.com/sebkopf/9405675) (the second to last)___

1. Enter the following command in terminal to check your macOS version. Expected output is as below the dashed line `---`.
```bash
~$ sw_vers
------------------------
ProductName:	Mac OS X
ProductVersion:	10.12.6
BuildVersion:	16G29
```

2. If your system is above 10.11, continue. Otherwise, upgrade it to `Sierra`.

3. Install [`homebrew`](https://brew.sh/), which is a very convenient package manager for `macOS`. Copy the following command in terminal and hit <kbd>Enter</kbd>:
```bash
~$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
Follow the instructions to get `brew` ready. When inputing your password, nothing will show up for security reasons. Just hit <kbd>Enter</kbd> when you're finished.

4. When `brew` is finished, copy the following command in terminal and hit <kbd>Enter</kbd>:
```bash
~$ touch ~/.bash_profile
~$ echo "export PATH=/usr/local/bin:$PATH
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:/usr/local/lib/pkgconfig/gtk+-2.0.pc:/opt/X11/lib/pkgconfig" >> ~/.bash_profile
~$ source ~/.bash_profile
```

5. Next, check your `R` version:
```bash
~$ R --version
R version 3.4.1 (2017-06-30) -- "Single Candle"
Copyright (C) 2017 The R Foundation for Statistical Computing
Platform: x86_64-apple-darwin16.6.0 (64-bit)
```

6. If yours is `3.4`, continue. Otherwise, upgrade it.

7. Enter the following into your terminal:
```bash
~$ brew uninstall --force cairo --ignore-dependencies
~$ brew cask install xquartz
~$ brew install --with-x11 cairo
```

8. Next, we need to tell `brew` to change the way it wants to install `gtk+`. A text editor window will pop up. Note that if you are not familiar with [`vim`](https://vim.sourceforge.io/), run `export EDITOR=emacs` in terminal before the line above to force using [`emacs`](https://www.gnu.org/software/emacs/), where you can use it as other common text editors.
```bash
~$ brew edit gtk+
```
Find where there is something like this:
```
def install
	args = [
            "--disable-dependency-tracking",
			"--disable-silent-rules",
			"--prefix=#{prefix}",
			"--disable-glibtest",
			"--enable-introspection=yes",
			# "--disable-visibility",
			# "--with-gdktarget=quartz",
			"--with-gdktarget=x11",
			"--enable-x11-backend"
		   ]
```
The original version that you just opened will be exactly the same, except the bottom two lines. Replace the whole chunk by this one. Then hit <kbd>ctrl</kbd>+<kbd>x</kbd> <kbd>ctrl</kbd>+<kbd>c</kbd>, followed by <kbd>y</kbd> to quit `emacs`.

9. Now we can install `gtk+` by:
```bash
~$ brew install --build-from-source --verbose gtk+
```
After installation succeeds, run this in terminal: `echo "export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:/usr/local/lib/pkgconfig/gtk+-2.0.pc:/opt/X11/lib/pkgconfig" >> ~/.bash_profile`. Otherwise, `gtk+` will not be recognized.

10. Download the newest source file for `RGtk2` from https://cran.r-project.org/web/packages/RGtk2/index.html.

11. Assume that the path to this file is `~/Downloads`. Run the following in terminal:
```bash
~$ cd ~/Downloads
~/Downloads$ R CMD INSTALL RGtk2_2.20.33.tar.gz
```
Note that versions may vary depending on when you download the source file. Use the exact filename that sits in the directory. (Tip: use <kbd>tab</kbd> to help autocomplete.)

12. When it finishes, open `XQuartz` by using `spotlight search`.

13. A window that's very similar to terminal will pop up. Type `r` and hit <kbd>enter</kbd> to open `R`.

14. In `R`, run:
```r
>> install.packages("https://togaware.com/access/rattle_5.0.14.tar.gz", repos=NULL, type="source")
```

15. Then try `rattle` library:
```r
>> library(rattle)
>> rattle()
```

16. `Rattle` will show up and it may ask you whether to install `XML` and `cairoDevice`. Click <kbd>yes</kbd> for both and you should be good to go now.

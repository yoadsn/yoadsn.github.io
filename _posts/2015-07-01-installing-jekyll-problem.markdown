---
layout: post
title: "Problems installing jekyll on windows"
date:   2015-07-01 00:00:00
categories: Ruby Jekyll
author: Yoad Snapir
---

Downloaded ruby 2.2.2 x64 installer from here
http://rubyinstaller.org/downloads/

Checking in a command line that ruby is installed:

D:\Development\Blog\yoadsn.github.io>ruby --version
ruby 2.2.2p95 (2015-04-13 revision 50295) [x64-mingw32]

installed Bundler

D:\Development\Blog\yoadsn.github.io>gem install bundler
Fetching: bundler-1.10.5.gem (100%)
Successfully installed bundler-1.10.5
Parsing documentation for bundler-1.10.5
Installing ri documentation for bundler-1.10.5
Done installing documentation for bundler after 6 seconds
1 gem installed


After creating a GemFile with the following contents

source 'https://rubygems.org'
gem 'github-pages'

Running the bundler to install the github-pages gem:

D:\Development\Blog\yoadsn.github.io>bundle install
Fetching gem metadata from https://rubygems.org/..........
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Installing RedCloth 4.2.9 with native extensions

Gem::InstallError: The 'RedCloth' native gem requires installed build tools.

Please update your PATH to include build tools or download the DevKit
from 'http://rubyinstaller.org/downloads' and follow the instructions
at 'http://github.com/oneclick/rubyinstaller/wiki/Development-Kit'
An error occurred while installing RedCloth (4.2.9), and Bundler cannot
continue.
Make sure that `gem install RedCloth -v '4.2.9'` succeeds before bundling.

To solve that, downloaded the `DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe` dev kit from here:

http://rubyinstaller.org/downloads/

Extracted to some folder and **from within** the cmd shell in the blog root folder rant the `devkitvars.bat` file.

D:\Development\Blog\yoadsn.github.io>D:\Development\Experiments\Ruby\DevKit\devkitvars.bat
Adding the DevKit to PATH...

Then, running again:

D:\Development\Blog\yoadsn.github.io>bundle install

Succeeded.

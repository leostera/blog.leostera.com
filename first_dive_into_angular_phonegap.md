# First Dive into AngularJS and PhoneGap
I was yet to find a comprehensive first dive into AngularJS and PhoneGap. So I decided to write one myself. This is not a definitive guide and it's definitely both opinionated and might have, well, some gaps. I'm learning this myself too. 

## Table of Contents:
1. Motivation
* Your first PhoneGap App
* Running a PhoneGap App
* The AngularJS Part
* The ExpressJS Part 
* Conclusion

## Motivation

## Your first PhoneGap App

The install process is very straight forward. Make sure you have [node.js](http://nodejs.org) installed and then open a terminal and run:

```
npm -g i phonegap                 # install phonegap cli tool globally
phonegap create [yourappname]     # bootstraps your application
```
Well that was pretty straight forward. You can now `cd [yourappname]` and you'll see a directory structure like this:

```
[yourappname]
 |- .cordova
 |- merges
 |- platforms
 |- plugins
 |- www
    |- css
    |- img
    |- js
    |- res
       |-  icon
       |-  screen
    |- spec
       |-  lib
    |- config.xml
    |- icon.png
    |- index.html
    |- spec.html
```

That `www` folder sure looks familiar. But before we dive into *what is all this stuff*, we'll try to run it.

## Running a PhoneGap App

So have your phone handy. I own a second batch iPhone 5 that should do the trick. Let's focus that terminal we have opened and run `phonegap` to see what options are available:

```
Usage: phonegap [options] [commands]

Description:

  PhoneGap command-line tool.

Commands:

  create <path>        create a phonegap project
  build <platform>     build a specific platform
  install <platform>   install a specific platform
  run <platform>       build and install a specific platform
  local [command]      development on local system
  remote [command]     development in cloud with phonegap/build
  help [command]       output usage information
  version              output version number

Options:

  -V, --verbose        allow verbose output
  -v, --version        output version number
  -h, --help           output usage information
  
// some more output here...
```

Aha! That's what I was looking for: `run <platorm>`. Let's try running `phonegap run ios`:

```
$ phonegap run ios
[phonegap] detecting iOS SDK environment...
[phonegap] using the local environment
[phonegap] adding the iOS platform...
[phonegap] missing library cordova/ios/3.1.0
[phonegap] downloading https://git-wip-us.apache.org/repos/asf
    ?p=cordova-ios.git;a=snapshot;h=3.1.0;sf=tgz...
[phonegap] compiling iOS...
[phonegap] successfully compiled iOS app
[phonegap] trying to install app onto device
[phonegap] no device was found
[phonegap] trying to install app onto emulator
 [warning] missing ios-sim
 [warning] install ios-sim from http://github.com/phonegap/ios-sim
   [error] An error occurred while emulating/deploying the ios project.
   === CLEAN TARGET CordovaLib OF PROJECT CordovaLib WITH CONFIGURATION Debug ===

Check dependencies...

#### Now here there's a lot of compiling output that we can ignore ####

** BUILD SUCCEEDED **

Error: ios-sim was not found. Please download, build and install version 1.7 or greater
 from https://github.com/phonegap/ios-sim into your path. Or 'npm install -g ios-sim'
 using node.js: http://nodejs.org/
```

Great, now we just need to install `ios-sim` with `npm`. Let's do that:

```
$ npm -g i ios-sim
#### lots of compiler output that we can ignore ####
** BUILD SUCCEEDED **

/usr/local/bin/ios-sim -> /usr/local/lib/node_modules/ios-sim/build/Release/ios-sim
ios-sim@1.8.2 /usr/local/lib/node_modules/ios-sim
```

Cool. Now we should be good to run our app –at least in the simulator–, right?

[Image: phonegap running sample app on iOS emulator with the console by it's side]

There we go. Now let's try to run it in *the actual device*. To do that we will have to make a build and run it with XCode, like this:

```
#### Assuming we are inside the projects folder
phonegap build ios
open platform/ios/[projectname].xcodeproj
#### Run on device
```

And after setting up XCode's signing certificates we get this:

[Image: phonegap app running on the iPhone]

## The AngularJS Part
Cool, now we have a running application on a phone. We can tweak it a bit and rerun to see the changes. It's plain old HTML5/CSS3/JavaScript.

## The ExpressJS Part
The server is usually not even mentioned in this kind of applications, and that's alright to some extent. People are expected to know. I'm about to see if this kind of applications require some extra server-side configuration and I'll talk about it here.

## Conclusion

This is a guide to use catch with cmake instead of the header file approach provided by Edugator. It was originally written for use with the University of Florida's COP3530 class but should work under any circumstances.

A [video version of this guide](https://youtu.be/DqE3UpOdBLw?si=rM44usul4MM0pDC6) is available.

**Note**:I use CLion, so the first part of this guide pulls screenshots from that interface. A VSCode addendum is provided at the bottom of the file with an additional tutorial video for getting set up - your CMakeLists.txt file and test.cpp should look the same, regardless of the IDE that you are using.

# Part 0: Why Bother?

Using the included catch file works fine, but each time you change test.cpp, it recompiles the entire catch header, making it take quite a while. Additionally, the catch.hpp provided on the project template is old and doesn&rsquo;t actually compile on Linux without adding the line `#define CATCH_CONFIG_NO_POSIX_SIGNALS` (<https://github.com/catchorg/Catch2/issues/2421>).

By instead using catch&rsquo;s newer cmake integration, it builds way quicker, lets you have a separate unit testing executable and main executable, and gives you access to the latest features of catch should you need them.


<a id="orgf5c7f84"></a>

# Part 1: Put your project on git/Github *now*
As a side-effect of pulling catch2 in as a Git repository, CLion will think your project is actually Catch2 and not whatever you're working on. It's easiest to address this beforehand and get your version control workflow set up with CLion so that you don't run into issues after the fact.

I find it easiest to click the "Version control" tab and then "Share Project On" to have pushes to my Github automatically set up, but feel free to do whatever is most comfortable for you.

# Part 2: Setting up cmake
This is mostly the same as from the video tutorial on each programming quiz (if you&rsquo;re using clion): navigate to test.cpp, click create a CMakeLists.txt, and accept the options to get a default file. From there, you need to add a couple lines to first pull in catch2 as a dependency and then link it to your compiled Tests executable. It&rsquo;s easier to show than to tell, so I&rsquo;ve attached a commented [CMakeLists.txt](./CMakeLists.txt) to explain.

Note that the CMakeLists.txt that is created by default doesn't include an executable for your Main without debug stuff by default. To create it, simply copy your Project1 or Tests `executable_add` block and replace test.cpp with main.cpp. catch.hpp is provided with the Project1 template and is by default included in the add_executable line for your default build. Be sure to remove any reference to catch.hpp in your CMakeLists.txt to actually use the faster Catch2 setup. The easiest way to do this would probably be to delete the catch.hpp file through Clion, which would then (hopefully) remove references to it elsewhere. Otherwise, you can just delete it yourself.


# Part 2.5: Changing the include in test.cpp
I forgot to include this when originally writing the guide, but it's an important step. First, make sure you don't have your `main.cpp` included in `test.cpp`. Additionally, you need to replace the original `#include "catch.h"` with `#include <catch2/catch_test_macros.hpp>` for it to actually use the cmake-based catch.
The top of my test.cpp looks like this:
```c++
#define CATCH_CONFIG_MAIN

#include <catch2/catch_test_macros.hpp>
#include <iostream>
#include "../src/AVL.h"
#include "../src/Parser.h"
#include "../src/StudentNode.h"
```

Keep in mind that you should include your own header files.


<a id="org390eeee"></a>

# Part 3: Integrating with clion

I think Clion should do this automatically, but just in case, here&rsquo;s some screenshots of the different setups for the main executable and the unit test executable.

![img](./images/Main.png)

![img](./images/Tests.png)

Once the two default builds are set up, I find it easiest to test specific things by duplicating the base configurations and changing flags. For example, to test the `[overloads]` catch flag only:
![img](./images/Catch_Flags.png)

You can also pass in certain files as stdin so you don&rsquo;t have to type test inputs manually:
![img](./images/Custom_cin.png)
Note that this can be done with the provided files as well as custom ones you write.

# Part 3 Alternate: Integrating with VSCode
Everything in the guide about the CMakeLists.txt file and test.cpp holds - the only difference with VSCode is how test running is integrated into the editor. You can also run a newer version of Catch2 if you want.

Here is a [video guide](https://youtu.be/yj8baGjXmTU) by TA Brian, although note that it doesn't use Project 1 as the example - you can modify the CMakeLists.txt provided by this repository in your own project. If you're interested in knowing why the CMakeLists.txt is set up like it is, you can watch the first part of the CLion-based video.

The text below is a writeup of the extra details from said video to get Catch2 set up with VSCode.

Note that this method will only work for code that is locally hosted on your computer such that git can "verify ownership" - you might run into problems if you store your code on a flash drive or a OneDrive folder, for instance.

## Extensions and Installations
Make sure that you have the following extensions installed on your VSCode.

- C/C++ (Microsoft)
- C/C++ Extension Pack (Microsoft)
- C++ TestMate (Mate Pek)
- CMake Tools (Microsoft)
- CMake Language Support (either twxs or Jose Torres)

You must also install CMake itself to your system. CLion bundles a version of CMake with itself so this step is unnecessary if on CLion. Note that the version you install may be older that what CLion would package - if you get an error in your CMakeLists.txt about your CMake being too old, simply change this line

``` cmake
cmake_minimum_required(VERSION 3.24)
```

to have whichever version of CMake that you have installed.

## Running and Compiling Tests
Once your CMakeLists.txt and test.cpp are in place, run the command `CMake: Configure` in the VSCode command palette to set CMake up.

![img](./images/VSCode_CMake.png)

Once this has run, you should be able to open the testing panel on the side of the window and see the name of your project. Click the "Refresh tests" button to reload your tests.


![img](./images/refresh_tests.png)

From here, you should be able to click on the drop down for TestMate C++ and then another for whatever you named your test executable in CMakeLists.txt. Click the play button to run the tests.


![img](./images/vsc_run_tests.png)

Note that each time you update your tests, you will have to click the "Refresh tests" button again for the changes to register. To get around this, you can add the following to the bottom of your CMakeLists.txt file:

```cmake
include(CTest)
include(Catch)
catch_discover_tests(Tests) # must be named the same as your test executable
```

Once you run "Cmake: Configure" again, CTests will be registered as well - now, you can click the play button next to its entry in the testing window and it will automatically rebuild your tests before running them.

![img](./images/CTests.png)

# Part 4: Using it
At this point, everything should work! You can just pick your run configuration in the menu at the top, and it'll compile and run your main or Tests or any of your debug configurations.

I hope this helps!

# Addendum
The link to the Catch2 documentation for setting this up is [here](https://github.com/catchorg/Catch2/blob/devel/docs/cmake-integration.md). It goes into more detail, but was a bit hard for me to parse at first, which was one of the reasons I wrote this guide.


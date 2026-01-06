

# BreezySLAM

<img src="breezyslam.png" align="center" width=700>

<p><p><p>
    
<a href="https://github.com/simondlevy/BreezySLAM">This repository</a> contains everything you need to
start working with 
<a href="http://en.wikipedia.org/wiki/Lidar">Lidar</a>
-based
<a href="http://en.wikipedia.org/wiki/Simultaneous_localization_and_mapping">SLAM</a> 
in Python. (There is also support for Matlab, C++, and Java; however, because of the popularity of
Python for this kind of work, I am no longer updating the code for those languages.)
BreezySLAM works with Python 3 on Linux and Mac OS X, and
with C++ on Linux and Windows.
By using Python C extensions, we were able to get the Python and Matlab versions to run
as fast as C++. For maximum efficiency on 32-bit platforms, we use Streaming
SIMD extensions (Intel) and NEON (ARMv7) in the compute-intensive part
of the code.
</p><p>
BreezySLAM was inspired by the <a href="http://home.wlu.edu/%7Elambertk/#Software">Breezy</a>
approach to Graphical User Interfaces developed by my colleague 
<a href="http://home.wlu.edu/%7Elambertk/">Ken Lambert</a>: an object-oriented
Application Programming Interface that is simple enough for beginners to use,
but that is efficient enough to scale-up to real world problems; for
example, the mapping of an entire floor of a house, shown in the image above-right,
made by a BreezySLAM 
<a href="https://www.linkedin.com/pulse/slam-your-robot-drone-python-150-lidar-chris-fotache">user</a>.

As shown in the following code fragment, the basic API is extremely 
simple: a constructor that accepts Lidar parameters and the size of 
the map (pixels) and mapping area (meters); a method for updating with the current scan; a method that returns
the current robot position; and a method for retrieving the current map as a byte
array.

<pre>
from breezyslam.algorithms import RMHC_SLAM

lidar = MyLidarModel()

mapbytes = bytearray(800*800)

slam = <b>RMHC_SLAM</b>(lidar, 800, 35) 

while True:

    scan = readLidar()

    slam.update(scan)

    x, y, theta = slam.<b>getpos</b>(scan)

    slam.getmap(mapbytes)
</pre>

If odometry is available, it can also be passed into the update method.  


</p><h3>Installing for Python</h3>

<p>
The BreezySLAM installation uses the popular
<a href="http://docs.python.org/2/distutils/introduction.html">distutils</a> 
approach to installing Python packages, so all you should have to do is
download and unzip the file, cd to <tt><b>BreezySLAM/python</b></tt>, and do 

<pre>
sudo python3 setup.py install
</pre>

For a quick demo, you can then cd to <tt><b>BreezySLAM/examples</b></tt> and do

<pre>
make pytest
</pre>


This will generate and display a PGM file showing the
map and robot trajctory for the Lidar scan and odometry data in the log file
<tt><b>exp2.dat</b></tt>.  If you have the 
<a href="http://www.pythonware.com/products/pil/">Python Imaging Library</a> installed,
you can also try the <b><tt>log2png.py</tt></b> script to generate a
a PNG file instead.

If you have installed [PyRoboViz](https://github.com/simondlevy/PyRoboViz),
you can see a &ldquo;live&rdquo; animation by doing

<pre>
make movie
</pre>

You can turn off odometry by setting the <b><tt>USE_ODOMETRY</tt></b>
parameter at the top of the Makefile to 0 (zero). You can turn off 
the particle-filter (Monte Carlo position estimation) by commenting-out
<b><tt>RANDOM_SEED</tt></b> parameter.

<p>

To see what other features are available, do 

<pre>
pydoc3 breezyslam
</pre>

By using the component classes <b>Map</b>, <b>Scan</b>, and
<b>Position</b> and the <b>distanceScanToMap()</b> method,
you can develop new algorithms and particle filters of your own.

<p><h3>Testing with the Hokuyo URG04LX</h3>

If you're running on Linux, you can install the <a href="http://home.wlu.edu/~levys/software/breezylidar/">BreezyLidar</a> package, the OpenCV Python package, and 
try the  <b>urgslam.py</b> example in the examples folder.

<p><h3>Testing with the GetSurreal XV Lidar</h3>

BreezySLAM includes Python support for the inexpensive 
<a href="https://www.getsurreal.com/product/xv-lidar-sensor-mount-package">XV Lidar</a> from GetSurreal.
To try it out, you'll also need the <a href="https://github.com/simondlevy/xvlidar">xvlidar</a> 
Python package.  Once you've installed
both packages, you can run the <b>xvslam.py</b> example in the <b>BreezySLAM/examples</b> folder.

<p><h3>Testing with the SLAMTEC RPLidar A1</h3>

BreezySLAM also includes partial Python support for the inexpensive 
<a href="http://www.slamtec.com/en/lidar/a1">RPLidar A1</a> from SLAMTECH.
To try it out, you'll also need the <a href="https://github.com/SkoltechRobotics/rplidar">rplidar</a> 
Python package.  Once you've installed that package, you can run the
<b>rpslam.py</b> example in the <b>BreezySLAM/examples</b> folder.

Unfortunately, I and several users have had trouble using BreezySLAM with the
RPLidar A1.  The issues are inconsistent enough that I cannot provide support
for this lidar unit if you run into trouble using it with BreezySLAM.

</p><h3>Installing for Matlab</h3>

<p>
I have run BreezySLAM in Matlab on 64-bit Windows, Linux, and Mac OS X. The <b>matlab</b> directory contains all the code you
need, including pre-compiled binaries for all three operating systems.  To try it out in Matlab, add this directory to your
path, then change to the <b>examples</b> directory and do

<pre>
  >> logdemo('exp2', 1)
</pre> 

If you modify the source code or want to build the binary for a different OS, you can change to the <b>matlab</b> 
directory and do

<pre>
  >> make
</pre>

For making the binary on Windows I found 
<a href="http://www.mathworks.com/matlabcentral/answers/95039-why-does-the-sdk-7-1-installation-fail-with-an-installation-failed-message-on-my-windows-system">these instructions</a> very helpful when I ran into trouble.

<h3>Installing for C++</h3>

Just cd to <tt><b>BreezySLAM/cpp</b></tt>, and do

<pre>
sudo make install
</pre>

This will put the <tt><b>libbreezyslam</b></tt> shareable library in your <tt><b>/usr/local/lib</b></tt>
directory.  If you keep your shared libraries elsewhere, just change the <tt><b>LIBDIR</b></tt>
variable at the top of the Makefile.  You may also need to add the following line to your <b>~/.bashrc</b>
file:

<pre>
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
</pre>


<p>

For a quick demo, you can then cd to <tt><b>BreezySLAM/examples</b></tt> and do

<pre>
make cpptest
</pre>

<p>

Again, you'll need to change the <tt><b>LIBDIR</b></tt> variable at the top of 
the Makefile in this directory as well, if you don't use <tt><b>/usr/local/lib</b></tt>.

</p><p>

<h3>Installing for Java</h3>

In <tt><b>BreezySLAM/java/edu/wlu/cs/levy/breezyslam/algorithms</b></tt> and
<tt><b>BreezySLAM/java/edu/wlu/cs/levy/breezyslam/components</b></tt>,
edit the <tt>JDKINC</tt> variable in the Makefile to reflect where you installed the JDK.
Then run <b>make</b> in these directories.

<p>

For a quick demo, you can then cd to <tt><b>BreezySLAM/examples</b></tt> and do

<pre>
make javatest
</pre>

<h3>Notes on Windows installation</h3>


Because of the 
<a href="http://stackoverflow.com/questions/2817869/error-unable-to-find-vcvarsall-bat">difficulties</a> that I and others have had installing Python extensions on Windows, I am no longer supporting 
the Python version of this package on Windows. If you want to try it yourself, <a href="https://docs.python.org/2/extending/windows.html">here</a> are some instructions.
<p>
To build and use the C++ library on Windows, I used MinGW. Whatever C++ compiler
you use, you'll have to add the location of the <tt><b>.dll</b></tt> file to your
<tt><b>PATH</b></tt> environment variable in the Advanced Systems Settings.

<h3>Adding new particle filters</h3>

Because it is built on top of the CoreSLAM (<a href="https://openslam.org/tinyslam.html">tinySLAM</a>) code base, BreezySLAM
provides a clean separation between
the map-building and particle-filtering (Monte Carlo position estimation)
components of SLAM.  To add a new particle filter, you can subclass 
<a href="doc/breezyslam.algorithms.html#CoreSLAM">breezyslam.algorithms.CoreSLAM</a> or 
<a href="doc/breezyslam.algorithms.html#SinglePositionSLAM">breezyslam.algorithms.SinglePositionSLAM</a>
classes, implementing the relevant methods.

<h3>Personnel</h3>

Suraj Bajracharya, Simon D. Levy, Matt Lubas, Alfredo Rwagaju

<h3>Acknowledgments</h3>

This work was supported in part by a  Commonwealth Research Commercialization Fund
grant from the Center for Innovative Technology (CRCF #MF14F-011-MS). We thank Michael Searing of Olin College for 
his help in debugging and testing this package.

# GNU LESSER GENERAL PUBLIC LICENSE

**Version 3, 29 June 2007**

 Copyright (C) 2007 Free Software Foundation, Inc. <http://fsf.org/>
 Everyone is permitted to copy and distribute verbatim copies
 of this license document, but changing it is not allowed.


  This version of the GNU Lesser General Public License incorporates
the terms and conditions of version 3 of the GNU General Public
License, supplemented by the additional permissions listed below.

  0. Additional Definitions.

  As used herein, "this License" refers to version 3 of the GNU Lesser
General Public License, and the "GNU GPL" refers to version 3 of the GNU
General Public License.

  "The Library" refers to a covered work governed by this License,
other than an Application or a Combined Work as defined below.

  An "Application" is any work that makes use of an interface provided
by the Library, but which is not otherwise based on the Library.
Defining a subclass of a class defined by the Library is deemed a mode
of using an interface provided by the Library.

  A "Combined Work" is a work produced by combining or linking an
Application with the Library.  The particular version of the Library
with which the Combined Work was made is also called the "Linked
Version".

  The "Minimal Corresponding Source" for a Combined Work means the
Corresponding Source for the Combined Work, excluding any source code
for portions of the Combined Work that, considered in isolation, are
based on the Application, and not on the Linked Version.

  The "Corresponding Application Code" for a Combined Work means the
object code and/or source code for the Application, including any data
and utility programs needed for reproducing the Combined Work from the
Application, but excluding the System Libraries of the Combined Work.

  1. Exception to Section 3 of the GNU GPL.

  You may convey a covered work under sections 3 and 4 of this License
without being bound by section 3 of the GNU GPL.

  2. Conveying Modified Versions.

  If you modify a copy of the Library, and, in your modifications, a
facility refers to a function or data to be supplied by an Application
that uses the facility (other than as an argument passed when the
facility is invoked), then you may convey a copy of the modified
version:

   a) under this License, provided that you make a good faith effort to
   ensure that, in the event an Application does not supply the
   function or data, the facility still operates, and performs
   whatever part of its purpose remains meaningful, or

   b) under the GNU GPL, with none of the additional permissions of
   this License applicable to that copy.

  3. Object Code Incorporating Material from Library Header Files.

  The object code form of an Application may incorporate material from
a header file that is part of the Library.  You may convey such object
code under terms of your choice, provided that, if the incorporated
material is not limited to numerical parameters, data structure
layouts and accessors, or small macros, inline functions and templates
(ten or fewer lines in length), you do both of the following:

   a) Give prominent notice with each copy of the object code that the
   Library is used in it and that the Library and its use are
   covered by this License.

   b) Accompany the object code with a copy of the GNU GPL and this license
   document.

  4. Combined Works.

  You may convey a Combined Work under terms of your choice that,
taken together, effectively do not restrict modification of the
portions of the Library contained in the Combined Work and reverse
engineering for debugging such modifications, if you also do each of
the following:

   a) Give prominent notice with each copy of the Combined Work that
   the Library is used in it and that the Library and its use are
   covered by this License.

   b) Accompany the Combined Work with a copy of the GNU GPL and this license
   document.

   c) For a Combined Work that displays copyright notices during
   execution, include the copyright notice for the Library among
   these notices, as well as a reference directing the user to the
   copies of the GNU GPL and this license document.

   d) Do one of the following:

       0) Convey the Minimal Corresponding Source under the terms of this
       License, and the Corresponding Application Code in a form
       suitable for, and under terms that permit, the user to
       recombine or relink the Application with a modified version of
       the Linked Version to produce a modified Combined Work, in the
       manner specified by section 6 of the GNU GPL for conveying
       Corresponding Source.

       1) Use a suitable shared library mechanism for linking with the
       Library.  A suitable mechanism is one that (a) uses at run time
       a copy of the Library already present on the user's computer
       system, and (b) will operate properly with a modified version
       of the Library that is interface-compatible with the Linked
       Version.

   e) Provide Installation Information, but only if you would otherwise
   be required to provide such information under section 6 of the
   GNU GPL, and only to the extent that such information is
   necessary to install and execute a modified version of the
   Combined Work produced by recombining or relinking the
   Application with a modified version of the Linked Version. (If
   you use option 4d0, the Installation Information must accompany
   the Minimal Corresponding Source and Corresponding Application
   Code. If you use option 4d1, you must provide the Installation
   Information in the manner specified by section 6 of the GNU GPL
   for conveying Corresponding Source.)

  5. Combined Libraries.

  You may place library facilities that are a work based on the
Library side by side in a single library together with other library
facilities that are not Applications and are not covered by this
License, and convey such a combined library under terms of your
choice, if you do both of the following:

   a) Accompany the combined library with a copy of the same work based
   on the Library, uncombined with any other library facilities,
   conveyed under the terms of this License.

   b) Give prominent notice with the combined library that part of it
   is a work based on the Library, and explaining where to find the
   accompanying uncombined form of the same work.

  6. Revised Versions of the GNU Lesser General Public License.

  The Free Software Foundation may publish revised and/or new versions
of the GNU Lesser General Public License from time to time. Such new
versions will be similar in spirit to the present version, but may
differ in detail to address new problems or concerns.

  Each version is given a distinguishing version number. If the
Library as you received it specifies that a certain numbered version
of the GNU Lesser General Public License "or any later version"
applies to it, you have the option of following the terms and
conditions either of that published version or of any later version
published by the Free Software Foundation. If the Library as you
received it does not specify a version number of the GNU Lesser
General Public License, you may choose any version of the GNU Lesser
General Public License ever published by the Free Software Foundation.

  If the Library as you received it specifies that a proxy can decide
whether future versions of the GNU Lesser General Public License shall
apply, that proxy's public statement of acceptance of any version is
permanent authorization for you to choose that version for the
Library.

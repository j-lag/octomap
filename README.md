OctoMap - RealTime Fork
===========================================================================

In this Fork we will try to accelerate octomap and try to provide realtime map update.

Test are done on:

**The Old One**
  * Name			Intel Core i7 920
  * Codename		Bloomfield
  * Specification		Intel(R) Core(TM) i7 CPU         920  @ 2.67GHz
  * Number of cores		4 (max 4)
  * Number of threads	8 (max 8)
  
**The New One**
  * TODO


Day 1:
--------
OccupancyOcTreeBase<NODE>::computeUpdate is multithreaded with openMP. This is good BUT
profiling reveal that locks cost a lot (50% of runtime is spent in RtlCriticalSection or RtlWaitForCriticalSection)

Modifications: 
- Remove critical section on  free_cells & occupied_cells updates.
- duplicate free_cells & occupied_cells -> one for each thread
- agregate result of each thread at the end

Test are done with this command: "graph2tree -i 0 *TEST_FILE* -o test.bt -res 0.4"


result on **The Old One**: 

| *TEST_FILE*         | Original OpenMp |without OpenMP | OpenMP with optimisation |
| ------------- | ------------- | ------------- | ------------- |
|  geb079_max50m.graph <br> Data points in graph: 5903426 | time to insert scans:<br> 12.095 sec<br> time to insert 100.000 points took:<br> 0.204881 sec (avg) |time to insert scans:<br> 6.667 sec<br>time to insert 100.000 points took:<br> 0.112934 sec (avg) | time to insert scans:<br> 3.452 sec <br>time to insert 100.000 points took:<br> 0.0584745 sec (avg) |
| new_college_dataset.graph <br> Data points in graph: 14468149| time to insert scans:<br> 184.516 sec<br>time to insert 100.000 points took:<br> 1.27533 sec (avg) | time to insert scans:<br> 84.289 sec<br>time to insert 100.000 points took:<br> 0.582583 sec (avg) |time to insert scans:<br> 139.228 sec<br>time to insert 100.000 points took:<br> 0.962307 sec (avg)|

*Results are unconclusive, have to test on other datasets/ computer...*

FIXME later: 
When resolution is high (-res 0.01) the bottleneck change: octomap::OccupancyOcTreeBase<octomap::OcTreeNode>::updateNode
Next optimisation should be done there.





Original README:
===========================================================================


OctoMap - An Efficient Probabilistic 3D Mapping Framework Based on Octrees.
===========================================================================

http://octomap.github.io

Originally developed by Kai M. Wurm and Armin Hornung, University of Freiburg, Copyright (C) 2009-2014.
Currently maintained by [Armin Hornung](https://github.com/ahornung).
See the [list of contributors](octomap/AUTHORS.txt) for further authors.

License: 
  * octomap: [New BSD License](octomap/LICENSE.txt)
  * octovis and related libraries: [GPL](octovis/LICENSE.txt)


Download the latest releases:
  https://github.com/octomap/octomap/releases

API documentation:
  http://octomap.github.com/octomap/doc/
  
Build status: 
  [![Build Status](https://travis-ci.org/OctoMap/octomap.png?branch=devel)](https://travis-ci.org/OctoMap/octomap)
  
Report bugs and request features in our tracker:
  https://github.com/OctoMap/octomap/issues

A list of changes is available in the [octomap changelog](octomap/CHANGELOG.txt)


OVERVIEW
--------

OctoMap consists of two separate libraries each in its own subfolder:
**octomap**, the actual library, and **octovis**, our visualization libraries and tools.
This README provides an overview of both, for details on compiling each please 
see [octomap/README.md](octomap/README.md) and [octovis/README.md](octovis/README.md) respectively.
See http://www.ros.org/wiki/octomap and http://www.ros.org/wiki/octovis if you 
want to use OctoMap in ROS; there are pre-compiled packages available.

You can build each library separately with CMake by running it from the subdirectories, 
or build octomap and octovis together from this top-level directory. E.g., to
only compile the library, run:

    cd octomap
    mkdir build
    cd build
    cmake ..
    make
  
To compile the complete package, run:

    cd build
    cmake ..
    make
  
Binaries and libs will end up in the directories `bin` and `lib` of the
top-level directory where you started the build.


See [octomap README](octomap/README.md) and [octovis README](octovis/README.md) for further
details and hints on compiling, especially under Windows.

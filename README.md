pcl
===

The Point Cloud Library (PCL) is a standalone, large scale, open project for 2D/3D image and point cloud processing.

PCL is released under the terms of the BSD license, and thus free for commercial and research use. We are financially supported by a consortium of commercial companies, with our own non-profit organization, Open Perception. We would also like to thank individual donors and contributors that have been helping the project.


PCL on Raspberry Pi (Depth-only)
===
*Rationale:* To operate Asus Xtion Pro with Raspberry Pi without taxing CPU too much. We decided to use depth without RGB.

Installation instruction for Raspberry Pi
===
1. Install OpenNI. See [this instruction](https://gist.github.com/chatchavan/990d3c0a5b085dc7bae1)
2. Install PCL dependencies. The easiest way to do this is to install `libpcl` from `jessie` (which wasn't compiled with OpenNI support). See [this instruction](https://gist.github.com/chatchavan/c758f1568d35bbf6dd75).
3. Clone this repository to a folder, let's say `~/pcl`
4. Configure:

  ```
  cd ~/pcl && mkdir build && cd build
  ccmake .. 
  ```
  In this step, ensure that you enabled OpenNI support and that the path of OpenNI library is correct. (For the latter, you need to turn on the advance mode. (Press "t") See [my configuration](https://gist.github.com/chatchavan/c758f1568d35bbf6dd75#file-cmakecache-txt) for reference. (I also turned many components that I don't need off.)
5. `make` and `sudo make install`.

Changes in `CMakeLists.txt` of your project
===
Our installation of OpenNI wasn't registered with `pkg-config`. Thus, we have to point each project to OpenNI manually. Manually adding OpenNI in `CMakeLists.txt` of your project. Add the following text just below the line starting with `set(PCL_BUILD_TYPE`

```
set(OPENNI_INCLUDE_DIRS_HINT "/usr/include/ni")
set(OPENNI_LIBRARY_DIRS "/usr/lib/") 
set(OPENNI_LIBRARY "/usr/lib/libOpenNI.so")
```

Difference from the example
===
If you use the [example from PCL website](http://pointclouds.org/documentation/tutorials/openni_grabber.php), you have both depth and RGB. We'll get only depth. So, we change the binding function to use only XYZ, i.e., replace everything with `XYZRGBA` to `XYZ` like the following:

```
boost::function<void (const PointCloud<PointXYZ>::ConstPtr&)> f =
			boost::bind(&grabberCallback, _1);
```

NOTE: May be the example in PCL source code is a better starting point (`pcl/io/tools/openni_grabber_depth_example.cpp`). I haven't tested it though.


Other troubleshooting
===

* [Device not found:](http://robotica.unileon.es/mediawiki/index.php/PCL/OpenNI_troubleshooting#No_device_found_.2F_No_devices_connected)* edit `sudo nano /usr/etc/primesense/GlobalDefaults.ini` and uncommented `;UsbInterface=2` (removing semi colon)

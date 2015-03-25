## Houdini native image formats ##

  * RAT - Random Access Texture: tiled, mip-mapped, pre-filtered and compressed, multi-raster images for efficient storing textures and environmental maps. Variable bit-depth per plane, half float, data windows, and arbitrary number of custom tags is also supported. Besides of a set of 2-dimensional rasters, RAT files can store a set of [deep images](http://en.wikipedia.org/wiki/Deep_image_compositing). Albeit common extension, these files are different beasts.

  * RAT - Deep Camera Maps: multi-raster, [deep image](http://en.wikipedia.org/wiki/Deep_image_compositing) format for storing multiply samples per pixel. Ideal for deep compositing, deep shadow maps and similar.

  * PIC - Houdini legacy native image format. Not to be underestimated, it's far beyond Softimage's PIC or Maya/Shake IFF files.  It supports almost every feature of OpenEXR: multi-raster, varying bit-depth per raster (8int, 16int, 32int, 16float, 32float), various compression types, custom tags. It also stores every raster separately, allowing to load images into memory much faster, than standard .exr files  (for the expense of being slightly bigger on disk).

## Build ##

  * export variable $NDKDIR pointing to Nuke's root directory (there are the headers of NDK + library we will link to). Something like:
```
export NDKDIR=/opt/Nuke6.3v2/
```

  * Make sure you have Houdini environment set up. In particular you will need $HT variable pointing to HDK toolkit and $HFS. To do that, as usual, go to Houdini installation folder and run _houdini\_setup_ sctipt you find there.
```
cd /opt/houdini12.1.185
source houdini_setup
cd $OLDPWD
```

  * Build as usual with provided Makefile (by typing _make_ in source directory). _make install_ will place plugins into a _~/.nuke_ directory.
  * You can optionally export DEBUG=1 env. variable to enable verbose output of the plugins.


## Install ##

  * Copy _ratReader.so_, _picReader.tcl_ and _ratReaderDeep.so_ anywhere your Nuke is looking for plugins (that is to $NUKE\_PATH).
  * Append to or export $LD\_LIBRARY\_PATH with Houdini's dso libraries:
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HFS/dsolib
```

  * It seems like Nuke is coming with a versions of standard c++/gcc libraries which shadow system files required by Houdini. You can fix it by renaming those libraries in Nuke's directory:
```
mv libgcc_s.so.1 libgcc_s.so.1.disabled
mv libstdc++.so.6.0.8  libstdc++.so.6.0.8.disabled
mv libstdc++.so.6 libstdc++.so.6.disabled
```

I'm not aware of any side effects of these steps, which doesn't mean there aren't any. So, beware of what you're doing.


  * Make sure Houdini won't load its image's dso before Nukes does it, otherwise Nuke will crash at some point.
```
export HOUDINI_DISABLE_IMAGE_DSO=1
```

## Status ##
  * Tested on various builds of Houdini 12 and 12.1 (+12.0.6) and Nuke (6.3v2, 6.3v7, 7.0v4) on 64bit Centos6.2 and Fedora12.
  * Metadata support in deep reader.
  * No writers.
  * In an addition to DeepReader plugin, standard Reader node will open deep images as a flattened 2d rasters.
  * There is no use of data windowing. At every engine run Nuke loads entire scanline width.
  * Plugins require Houdini license to exist in a system, although they won't consume any. Houdini Apprentice will do.

## Disclaimer ##
This software is provided "as is" with no warranties of any kind. You're free to use and/or modify it on your own risk.
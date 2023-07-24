# build cimgui with glfw3

## git

``` shell
git clone --recursive https://github.com/jinzhongmin/libgoimgui
```

## build
``` shell
mkdir build && cd build
cmake  -DCMAKE_INSTALL_PREFIX="../install"  ../libgoimgui
make install # or ninja install
```
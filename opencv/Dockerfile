FROM leeyanzhe/arm-cross:base AS builder

ENV OPENCV_VERSION 4.1.0

WORKDIR /usr/src

RUN wget -qO opencv-$OPENCV_VERSION.tar.gz https://github.com/opencv/opencv/archive/$OPENCV_VERSION.tar.gz \
    && wget -qO opencv_contrib-$OPENCV_VERSION.tar.gz https://github.com/opencv/opencv_contrib/archive/$OPENCV_VERSION.tar.gz \
    && tar -zxf opencv-$OPENCV_VERSION.tar.gz \
    && tar -zxf opencv_contrib-$OPENCV_VERSION.tar.gz \
    && rm *.gz

WORKDIR /usr/src/opencv-$OPENCV_VERSION/build

RUN cmake -DCMAKE_INSTALL_PREFIX=/opt/opencv \
      -DCMAKE_SYSTEM_NAME=Linux \
      -DCMAKE_SYSTEM_PROCESSOR=arm \
      -DCMAKE_C_COMPILER=arm-linux-musleabihf-gcc \
      -DCMAKE_CXX_COMPILER=arm-linux-musleabihf-g++ \
      -DCMAKE_LINKER=/usr/bin/arm-musl-linuxeabihf-ld \
      -DCMAKE_AR=/usr/bin/arm-linux-musleabihf-ar \
      -DCMAKE_EXE_LINKER_FLAGS="-static" \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_TOOLCHAIN_FILE=../platforms/linux/arm.toolchain.cmake \
      -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-$OPENCV_VERSION/modules \
      -DWITH_OPENCL=OFF \
      -DBUILD_TIFF=ON \
      -DBUILD_JPEG=ON \
      -DBUILD_opencv_world=ON \
      -DBUILD_opencv_xfeatures2d=ON \
      -DBUILD_SHARED_LIBS=OFF \
      -DWITH_ADE=OFF \
      -DWITH_PROTOBUF=ON \
      -DWITH_GTK=OFF \
      -DWITH_FFMPEG=OFF \
      -DWITH_D1394=OFF \
      -DOPENCV_ENABLE_NONFREE=ON \
      -DENABLE_VFPV3=ON \
      -DENABLE_NEON=ON \
      -L \
      ..

RUN make -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l) \
    && make install \
    && rm -rf *

FROM leeyanzhe/arm-cross:base

COPY --from=builder /opt/opencv /opt/opencv

WORKDIR /root

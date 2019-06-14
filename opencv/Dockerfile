FROM leeyanzhe/arm-cross:base AS ffmpeg-builder

ENV FFMPEG_VERSION 4.1.3

WORKDIR /usr/src

RUN git clone https://code.videolan.org/videolan/x264.git

RUN wget -q https://ffmpeg.org/releases/ffmpeg-$FFMPEG_VERSION.tar.xz \
    && tar -Jxf ffmpeg-$FFMPEG_VERSION.tar.xz

WORKDIR /usr/src/x264

RUN ./configure \
      --cross-prefix=aarch64-linux-musl- \
      --prefix=/opt/cross-host-root \
      --extra-ldflags="-static" \
      --host=arm-linux \
      --enable-static \
      --disable-asm \
      --enable-pic

RUN make -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l) \
    && make install

WORKDIR /usr/src/ffmpeg-$FFMPEG_VERSION

RUN ./configure \
      --enable-cross-compile \
      --cross-prefix=aarch64-linux-musl- \
      --target-os=linux \
      --arch=aarch64 \
      --enable-nonfree \
      --prefix=/opt/cross-host-root \
      --ar=aarch64-linux-musl-ar \
      --disable-librtmp \
      --extra-cflags="-I/opt/cross-host-root/include" \
      --extra-ldflags="-L/opt/cross-host-root/lib -static" \
      --enable-pic \
      --enable-libx264 \
      --enable-gpl \
      --sysroot=/opt/cross-host-root

RUN make -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l) \
    && make install

FROM leeyanzhe/arm-cross:base AS openblas-builder

WORKDIR /usr/src

RUN git clone https://github.com/xianyi/OpenBLAS.git

WORKDIR /usr/src/OpenBLAS

RUN make CC=aarch64-linux-musl-gcc \
         FC=aarch64-linux-musl-gfortran \
         HOSTCC=gcc \
         TARGET=CORTEXA53 \
         BINARY=64 \
         -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l)

RUN make PREFIX=/opt/cross-host-root install

FROM leeyanzhe/arm-cross:base AS opencv-builder

ENV OPENCV_VERSION 4.1.0

WORKDIR /usr/src

RUN wget -qO opencv-$OPENCV_VERSION.tar.gz https://github.com/opencv/opencv/archive/$OPENCV_VERSION.tar.gz \
    && wget -qO opencv_contrib-$OPENCV_VERSION.tar.gz https://github.com/opencv/opencv_contrib/archive/$OPENCV_VERSION.tar.gz \
    && tar -zxf opencv-$OPENCV_VERSION.tar.gz \
    && tar -zxf opencv_contrib-$OPENCV_VERSION.tar.gz \
    && rm *.gz

WORKDIR /usr/src/opencv-$OPENCV_VERSION/build

COPY --from=ffmpeg-builder /opt/cross-host-root /opt/cross-host-root

COPY --from=openblas-builder /opt/cross-host-root /opt/cross-host-root

RUN cmake -DCMAKE_INSTALL_PREFIX=/opt/cross-host-root \
      -DCMAKE_SYSTEM_NAME=Linux \
      -DCMAKE_SYSTEM_PROCESSOR=arm \
      -DARM_LINUX_SYSROOT=/opt/cross-host-root \
      -DCMAKE_FIND_ROOT_PATH=/opt/cross-host-root \
      -DCMAKE_C_COMPILER=aarch64-linux-musl-gcc \
      -DCMAKE_CXX_COMPILER=aarch64-linux-musl-g++ \
      -DCMAKE_LINKER=/usr/bin/aarch64-linux-musl-ld \
      -DCMAKE_AR=/usr/bin/aarch64-linux-musl-ar \
      -DCMAKE_EXE_LINKER_FLAGS="-static" \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_TOOLCHAIN_FILE=../platforms/linux/aarch64-gnu.toolchain.cmake \
      -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-$OPENCV_VERSION/modules \
      -DWITH_OPENCL=OFF \
      -DWITH_TBB=ON \
      -DBUILD_TBB=ON \
      -DBUILD_TIFF=ON \
      -DBUILD_JPEG=ON \
      -DBUILD_opencv_world=ON \
      -DBUILD_opencv_xfeatures2d=ON \
      -DBUILD_SHARED_LIBS=OFF \
      -DWITH_ADE=OFF \
      -DWITH_PROTOBUF=ON \
      -DWITH_GTK=OFF \
      -DWITH_FFMPEG=ON \
      -DWITH_D1394=OFF \
      -DOPENCV_ENABLE_NONFREE=ON \
      -L \
      ..

RUN make -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l) \
    && make install \
    && rm -rf *

FROM leeyanzhe/arm-cross:base

COPY --from=opencv-builder /opt/cross-host-root /opt/cross-host-root

WORKDIR /root

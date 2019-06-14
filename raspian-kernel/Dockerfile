FROM leeyanzhe/arm-cross:base

RUN apt -y update \
    && apt -y install libncursesw5-dev \
                      bison \
                      flex \
                      bc \
                      libssl-dev \
    && apt -y clean

WORKDIR /usr/src

ENV KERNEL kernel8

RUN git clone --depth=1 https://github.com/raspberrypi/linux

WORKDIR /usr/src/linux

RUN make ARCH=arm64 \
         CROSS_COMPILE=aarch64-linux-musl- \
         bcmrpi3_defconfig \
    && make ARCH=arm64 \
            CROSS_COMPILE=aarch64-linux-musl- \
            -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l) \
            Image modules dtbs \
    && make ARCH=arm64 \
       CROSS_COMPILE=aarch64-linux-musl- \
       INSTALL_MOD_PATH=../linux-built \
       modules_install

RUN mkdir -p ../linux-built/boot/overlays \
    && cp arch/arm64/boot/dts/broadcom/*.dtb ../linux-built/boot/ \
    && cp arch/arm64/boot/dts/overlays/*.dtb* ../linux-built/boot/overlays/ \
    && cp arch/arm64/boot/dts/overlays/README ../linux-built/boot/overlays/ \
    && cp arch/arm64/boot/Image ../linux-built/boot/$KERNEL.img \
    && cd .. \
    && tar -zcf linux-built.tar.gz linux-built
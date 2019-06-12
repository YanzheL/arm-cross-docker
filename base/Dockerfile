FROM ubuntu

RUN apt -y update \
    && apt -y install build-essential \
                      cmake \
                      gdb-multiarch \
                      openssh-server \
                      rsync \
                      vim \
                      git \
                      python \
                      python3 \
                      wget \
                      curl \
                      pkg-config \
    && apt -y clean \
    && mkdir -m 700 /run/sshd

WORKDIR /usr/src

COPY musl-config.sh ./musl-config.sh

RUN git clone https://github.com/GregorR/musl-cross.git \
    && cd musl-cross \
    && mv ../musl-config.sh ./config.sh \
    && ./build.sh \
    && cd .. \
    && rm -rf /usr/src/* \
    && ln -s /opt/cross/arm-linux-musleabihf/bin/* /usr/bin/

WORKDIR /root

ENV PKG_CONFIG_PATH /opt/cross-host-root/lib/pkgconfig

CMD /usr/sbin/sshd -D $SSHD_OPTS
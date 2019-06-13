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
                      libncursesw5-dev \
    && apt -y clean \
    && mkdir -m 700 /run/sshd

WORKDIR /usr/src

COPY config.mak ./config.mak

RUN git clone https://github.com/richfelker/musl-cross-make.git \
    && cd musl-cross-make \
    && mv ../config.mak ./config.mak \
    && make -j$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l) \
    && make all \
    && make install \
    && cd .. \
    && rm -rf /usr/src/* \
    && ln -s /opt/cross/bin/* /usr/bin/

WORKDIR /root

ENV PKG_CONFIG_PATH /opt/cross-host-root/lib/pkgconfig

CMD /usr/sbin/sshd -D $SSHD_OPTS
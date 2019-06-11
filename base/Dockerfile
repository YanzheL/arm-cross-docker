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
    && apt -y clean \
    && mkdir -m 700 /run/sshd

WORKDIR /usr/src

RUN git clone https://github.com/GregorR/musl-cross.git

COPY musl-config.sh ./musl-cross/config.sh

RUN cd musl-cross \
    && ./build.sh \
    && cd .. \
    && rm -rf /usr/src/* \
    && ln -s /opt/cross/arm-linux-musleabihf/bin/* /usr/bin/

WORKDIR /root

CMD /usr/sbin/sshd -D $SSHD_OPTS
FROM open5gbase:v1 as builder

# Get open5gs code and install
RUN git clone --recursive https://github.com/open5gs/open5gs && cd open5gs && \
    git checkout main && meson build --prefix=`pwd`/install && \
    ninja -C build && cd build && ninja install && \
    mkdir -p /open5gs/install/include

# Build final image
FROM ubuntu:focal

ENV DEBIAN_FRONTEND=noninteractive
ENV LD_LIBRARY_PATH=/open5gs/install/lib/x86_64-linux-gnu

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        curl \
        ca-certificates \
        libssl-dev \
        libyaml-dev \
        libmicrohttpd-dev \
        libmongoc-dev \
        libsctp-dev \
        libcurl4-gnutls-dev \
        libtins-dev \
        libidn11-dev \
        libtalloc-dev \
        netbase \
        ifupdown \
        net-tools \
        iputils-ping \
        python3-setuptools \
        python3-wheel \
        python3-pip \
        iptables && \
    apt-get autoremove -y && apt-get autoclean

RUN pip3 install click
RUN  apt-get remove -y curl && apt-get autoremove -y && apt-get autoclean
RUN update-ca-certificates

COPY --from=builder /open5gs/install/bin /open5gs/install/bin
COPY --from=builder /open5gs/install/etc /open5gs/install/etc
COPY --from=builder /open5gs/install/include /open5gs/install/include
COPY --from=builder /open5gs/install/lib /open5gs/install/lib

# Set the working directory to open5gs
WORKDIR open5gs

COPY open5gs_init.sh /
CMD /open5gs_init.sh
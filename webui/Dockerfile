
FROM open5gbase:v1 as builder

# Get open5gs code and install
RUN git clone --recursive https://github.com/open5gs/open5gs && cd open5gs && \
    git checkout main 

# Building WebUI of open5gs
RUN cd open5gs/webui && npm clean-install && npm run build

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

RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && apt-get install -y nodejs && \
    apt-get remove -y curl && apt-get autoremove -y && apt-get autoclean


COPY --from=builder /open5gs/webui /open5gs/webui

# Set the working directory to open5gs
WORKDIR open5gs

COPY open5gs_init.sh /
CMD /open5gs_init.sh
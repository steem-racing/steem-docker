
# cd ~/dkr && docker build -t steem .
# docker run -p 0.0.0.0:2001:2001 -v $PWD/data:/steem -d -t steem

FROM ubuntu:18.04
RUN apt update && \
    apt install -y \
    autoconf \
    automake \
    cmake \
    gcc-5 \
    g++-5 \
    libbz2-dev \
    libsnappy-dev \
    libssl1.0-dev \
    libtool \
    make \
    wget \
    pkg-config \
    python3 \
    python3-jinja2 && \
    apt clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
    
RUN apt remove -y gcc g++ && \
	ln -s /usr/bin/gcc-5 /usr/bin/gcc && \
	ln -s /usr/bin/gcc-5 /usr/bin/cc && \
	ln -s /usr/bin/g++-5 /usr/bin/g++ && \
	ln -s /usr/bin/g++-5 /usr/bin/cxx
	
RUN cd ~ && wget http://downloads.sourceforge.net/project/boost/boost/1.60.0/boost_1_60_0.tar.gz \
  && tar xfz boost_1_60_0.tar.gz \
  && rm boost_1_60_0.tar.gz \
  && cd boost_1_60_0 \
  && ./bootstrap.sh --prefix=/usr/local --with-libraries=program_options \
  && ./b2 install \
  && cd ~ \
  && rm -rf boost_1_60_0

# P2P (seed) port
EXPOSE 2001
# RPC ports
EXPOSE 5000
EXPOSE 8090

ARG steemd_version=v0.19.10

RUN cd ~ && \
	git clone https://github.com/steemit/steem.git && \
	cd steem && \
	git checkout ${steemd_version} && \
	git submodule update --init --recursive && \
        cd ~/steem && \
	cmake -DCMAKE_BUILD_TYPE=Release . \
            -DLOW_MEMORY_NODE=ON \
            -DCLEAR_VOTES=ON \
            -DSKIP_BY_TX_ID=ON && \
	make -j$(nproc) && make install && rm -rf ~/steem
VOLUME /steem
WORKDIR /steem

RUN echo "Please configure me! You need to mount a data directory onto /steem of this container to it to function correctly. (if you're using Steem-in-a-box most of this is handled automatically)"
CMD ["sh", "-c", "/usr/local/bin/steemd"]

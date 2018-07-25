
# cd ~/dkr && docker build -t steem .
# docker run -p 0.0.0.0:2001:2001 -v $PWD/data:/steem -d -t steem

FROM ubuntu:18.04

RUN apt-get update && \
	apt-get install -y gcc-5 g++-5 cmake make libbz2-dev libdb++-dev libdb-dev && \
	apt-get install -y libssl1.0-dev libreadline-dev autoconf libtool git gdb liblz4-tool wget jq virtualenv libgflags-dev libsnappy-dev zlib1g-dev libbz2-dev liblz4-dev libzstd-dev && \
	apt-get install -y autotools-dev build-essential libbz2-dev libicu-dev python-dev wget doxygen python3 python3-dev python3-pip libboost-all-dev curl && \
        apt-get install -y pkg-config python3 python3-jinja2 && \
    	apt-get clean && \
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

# Start with CUDA base image
FROM kaixhin/cuda
MAINTAINER Soonwoong Lee <soonwoong.lee@sk.com>

# Install git, bc and dependencies
RUN apt-get update && apt-get install -y \
  git \
  bc \
  libatlas-base-dev \
  libatlas-dev \
  libboost-all-dev \
  libopencv-dev \
  libprotobuf-dev \
  libgoogle-glog-dev \
  libgflags-dev \
  protobuf-compiler \
  libhdf5-dev \
  libleveldb-dev \
  liblmdb-dev \
  libsnappy-dev

# Clone Caffe repo and move into it
RUN cd /root && git clone https://github.com/BVLC/caffe.git && cd caffe && \
# Copy Makefile
  cp Makefile.config.example Makefile.config && \
# Make
  make -j"$(nproc)" all

# Install easy_install & pip
RUN apt-get install -y \
  python-setuptools \
  python-pip

RUN apt-get install -y \
  gfortran \
  python-numpy \
  python-scipy

# Make pycaffe
RUN cd /root/caffe/python \
  for req in $(cat requirements.txt); do pip install $req; done \
  cd .. \
  make pycaffe \
  make distribute

# Set ~/caffe as working directory
WORKDIR /root/caffe

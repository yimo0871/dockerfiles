FROM ubuntu:16.04
MAINTAINER yimo "42022883@qq.com"

RUN \
  apt-get --assume-yes update && apt-get --assume-yes upgrade && \
  apt-get --assume-yes install build-essential gcc g++ automake git-core git autoconf make patch \
  libmysql++-dev php5.6 nginx libtool libssl-dev grep binutils zlibc libc6 libbz2-dev cmake subversion \
  libboost-all-dev screen libace-dev

RUN \
  wget -O ACE-6.5.0.tar.gz "http://download.dre.vanderbilt.edu/previous_versions/ACE-6.5.0.tar.gz" \
  tar -zxvf ACE-6.5.0.tar.gz && \
  export ACE_ROOT=/root/ACE_wrappers \
  export LD_LIBRARY_PATH=$ACE_ROOT/lib:$LD_LIBRARY_PATH && \
  vim $ACE_ROOT/ace/config.h \
  #include "ace/config-linux.h" \
  vim $ACE_ROOT/include/makeinclude/platform_macros.GNU \
  #include "$(ACE_ROOT)/include/makeinclude/platform_linux.GNU"  \
  INSTALL_PREFIX = /usr/local && \
  cd "/root/ACE_wrappers " && \
  make && make install 
    
RUN \
  cd "~" && \
  git clone https://github.com/lh-server/core.git /sources --recursive -b master && \
  git clone https://github.com/brotalnia/database.git /database --recursive -b master
  
RUN \
  cd "/root/core" && \
  vim CMakeLists.txt && \
  :s/TBB" 0/TBB" 1/ && \
  mkdir build && \
  cd build && \
  cmake -DCMAKE_INSTALL_PREFIX=/opt/lh ../ && \
  make && make install 

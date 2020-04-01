FROM mysql:5.7
MAINTAINER yimo "42022883@qq.com"

RUN \
  apt-get --assume-yes update && apt-get --assume-yes upgrade && \
  apt-get --assume-yes install build-essential gcc g++ automake git-core git autoconf make patch \
  libmysql++-dev libtool libssl-dev grep binutils zlibc libc6 libbz2-dev cmake subversion \
  libboost-all-dev wget vim tumx mysql-client-5.6 screen libace-dev 

RUN yum install wget \
    && wget -O ACE-6.5.0.tar.gz "http://download.dre.vanderbilt.edu/previous_versions/ACE-6.5.0.tar.gz" \
    && tar -zxvf ACE-6.5.0.tar.gz \
    && export ACE_ROOT=/root/ACE_wrappers \
    && export LD_LIBRARY_PATH=$ACE_ROOT/lib:$LD_LIBRARY_PATH \
    && vim $ACE_ROOT/ace/config.h \
    && #include "ace/config-linux.h" \
    && vim $ACE_ROOT/include/makeinclude/platform_macros.GNU \
    && #include "$(ACE_ROOT)/include/makeinclude/platform_linux.GNU"  \
    && INSTALL_PREFIX = /usr/local \
    && cd "/root/ACE_wrappers " \
    && make && make install 
    
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
  
RUN \
  mysql -u root -p && \
  CREATE DATABASE IF NOT EXISTS realmd DEFAULT CHARSET utf8 COLLATE utf8_general_ci; && \
  CREATE DATABASE IF NOT EXISTS characters DEFAULT CHARSET utf8 COLLATE utf8_general_ci; && \
  CREATE DATABASE IF NOT EXISTS mangos DEFAULT CHARSET utf8 COLLATE utf8_general_ci; && \
  CREATE DATABASE IF NOT EXISTS logs DEFAULT CHARSET utf8 COLLATE utf8_general_ci; && \
  create user mangos identified by 'mangos'; && \
  grant all on realmd.* to mangos@'localhost' identified by 'mangos' with grant option; && \
  grant all on characters.* to mangos@'localhost' identified by 'mangos' with grant option; && \
  grant all on mangos.* to mangos@'localhost' identified by 'mangos' with grant option; && \
  grant all on logs.* to mangos@'localhost' identified by 'mangos' with grant option; && \
  cd "/root/database" && \
  tar world_full_05_october_2019.7z && \
  cd "/root/core/sql" && \
  mysql -u mangos -p realmd < logon.sql && \
  mysql -u mangos -p logs < logs.sql && \
  mysql -u mangos -p characters < characters.sql && \
  mysql -u mangos -p mangos < /root/database/world_full_05_october_2019.sql && \
  cd "/root/core/sql/migrations" && \
  chmod +x merge.sh && \
  ./merge.sh && \
  mysql -u mangos -p realmd < logon_db_updates.sql && \
  mysql -u mangos -p logs < logs_db_updates.sql && \
  mysql -u mangos -p mangos < world_db_updates.sql

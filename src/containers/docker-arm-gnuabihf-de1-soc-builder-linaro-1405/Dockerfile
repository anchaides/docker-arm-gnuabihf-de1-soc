# tag as docker-arm-gnuabihf-de1-soc-builder-linaro-1405
from ubuntu:12.04 as initial-setup
COPY --from=anchaides/base /root/.bashrc /root/
COPY build.sh /
RUN  sed -i -e "s/archive.ubuntu.com/old-releases.ubuntu.com/g" /etc/apt/sources.list
RUN  apt-get update 
RUN  apt-get -y upgrade 
RUN  apt-get -y install git

FROM initial-setup as dependencies32bit
#install 32bit dependencies 
RUN apt-get -y install wget vim
RUN apt-get -y install ia32-libs
RUN apt-get -y install gcc make
RUN apt-get -y install gcc-multilib

FROM dependencies32bit as tool-chain
# this is needed to build 32bit binaries on 64bit operating systems
#LD_PRELOAD=inode64.so is necessary 

#obtained from https://thesofproject.github.io/latest/developer_guides/tech/compile_wsl.html  
RUN wget https://raw.githubusercontent.com/jajanusz/sof-goodies/master/wsl_32bit_support/inode64.c --no-check-certificate -O /inode64.c
RUN chmod 740 /build.sh
RUN /build.sh
RUN cp /b64/inode64.so /lib/x86_64-linux-gnu/inode64.so
RUN cp /b32/inode64.so /lib/
RUN wget http://releases.linaro.org/archive/14.05/components/toolchain/binaries/gcc-linaro-arm-linux-gnueabihf-4.9-2014.05_linux.tar.xz -O - | tar xvfJ -
RUN git clone https://github.com/altera-opensource/intel-socfpga-hwlib.git soc-hwlib

from tool-chain as final-setup 
ENV LD_PRELOAD=inode64.so
ENV LINARO_PATH=/gcc-linaro-arm-linux-gnueabihf-4.9-2014.05_linux
ENV ARM_COMPILER_PATH=${LINARO_PATH}/bin
ENV ARM_LIBC_PATH=${LINARO_PATH}/arm-linux-gnueabihf/libc/
ENV ARM_INC_PATH=${LINARO_PATH}/arm-linux-gnueabihf/include/
ENV HWLIBS_ROOT=/soc-hwlib/armv7a/hwlib/


WORKDIR /source
CMD [/bin/bash -c "$@"]   
LABEL maintainer="Antonio <vxpwg6n8@anonaddy.me>"

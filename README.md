# anchaides/arm-gnuabihf-de1-soc

## Summary 

Docker container to cross compile ARM Cortex-A9 applications for Altera's (Intel) DE1-SOC development board in 32 bits in WLS. This container integrates the fix originally found [here](https://thesofproject.github.io/latest/developer_guides/tech/compile_wsl.html): for the inode compatibility isssues seen in WSL subsystems running on 64 bit systems. Uses a linaro release from May 2015 to avoid glibc compatibility issues. This container fetches intel-socfpga-hwlib from altera-opensource. 

## Usage 

Build the binary from your source directory as follows: 

    `docker  run -v "$PWD:/source" --rm -it --privileged anchaides/arm-gnuabihf-de1-soc make`

make command expects your source directory to already contain a Makefile in it. These environment variables will be passed: 

```
ENV LD_PRELOAD=inode64.so
ENV LINARO_PATH=/gcc-linaro-arm-linux-gnueabihf-4.9-2014.05_linux
ENV ARM_COMPILER_PATH=${LINARO_PATH}/bin
ENV ARM_LIBC_PATH=${LINARO_PATH}/arm-linux-gnueabihf/libc/
ENV ARM_INC_PATH=${LINARO_PATH}/arm-linux-gnueabihf/include/
ENV HWLIBS_ROOT=/soc-hwlib/armv7a/hwlib/

```

Makefile example:


```
TARGET = my_app

SOCEDS_DEST_ROOT    ?= /embedded/
ARM_COMPILER_PATH   ?= $(SOCEDS_DEST_ROOT)/host_tools/linaro/gcc/bin
ARM_LIBC_PATH       ?= $(SOCEDS_DEST_ROOT)/host_tools/linaro/gcc/arm-linux-gnueabihf/libc/
ARM_INC_PATH        ?= $(SOCEDS_DEST_ROOT)/host_tools/linaro/gcc/arm-linux-gnueabihf/include
ALT_DEVICE_FAMILY   ?= soc_cv_av
SOCEDS_ROOT         ?= $(SOCEDS_DEST_ROOT)
HWLIBS_ROOT         ?= $(SOCEDS_ROOT)/ip/altera/hps/altera_hps/hwlib
CROSS_COMPILE = arm-linux-gnueabihf-
CFLAGS = -g -Wall  -D$(ALT_DEVICE_FAMILY) -I$(HWLIBS_ROOT)/include/$(ALT_DEVICE_FAMILY)   -I$(HWLIBS_ROOT)/include/
LDFLAGS =  -g  -Wall
CC =$(ARM_COMPILER_PATH)/$(CROSS_COMPILE)gcc

ARCH= arm

build: $(TARGET)
$(TARGET): main.o
    $(CC) $(LDFLAGS)  $^ -o $@
%.o : %.c
    $(CC) -I${ARM_INC_PATH}  $(CFLAGS) -c $< -o $@

.PHONY: clean
clean:
    rm -f $(TARGET) *.a *.o *~
```

## Resources 
* [intel SOCFPGA hardware library](https://github.com/altera-opensource/intel-socfpga-hwlib)
* [U-BOOT-socfpga](https://github.com/altera-opensource/u-boot-socfpga)
* [Linaro.org]( http://releases.linaro.org)
* [DE1 SOC Resources](http://cd-de1-soc.terasic.com)

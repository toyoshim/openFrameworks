# openFrameworks universal makefile
#
# make help : shows this message
# make Debug:  makes the application with debug symbols
# make Release: makes the app with optimizations
# make: the same as make Release
# make cleanDebug: cleans the Debug target
# make cleanRelease: cleans the Release target
# make clean: cleans everything
#
# edit the following  vars to customize the makefile

OS_NAME := $(shell uname -s)
ARCH := linux

# Android
ifeq ($(findstring Android,$(MAKECMDGOALS)),Android)
	include paths.make
	ARCH = android
	ifeq ($(OS_NAME),Darwin)
		HOST_PLATFORM = darwin-x86
	else
		HOST_PLATFORM = linux-x86
	endif
	COMPILER_OPTIMIZATION = -Os
	NDK_PLATFORM = android-8
endif

# Google Native Client (NaCl)
ifeq ($(findstring NaCl,$(MAKECMDGOALS)),NaCl)
	ifndef NACL_SDK_ROOT
		$(error NACL_SDK_ROOT not set)
	endif
	ifeq ($(OS_NAME),Darwin)
		OS_SUBDIR = mac
	else ifeq ($(OS_NAME), Linux)
		OS_SUBDIR = linux
	else ifneq (, $(filter CYGWIN%,$(OS_NAME)))
		OS_SUBDIR = win
	else
		$(error No support for the Operating System: $(OS_NAME))
	endif
	NACL_TOOLCHAIN_ROOT = $(NACL_SDK_ROOT)/toolchain/$(OS_SUBDIR)_x86_newlib
	ifeq ($(findstring 32,$(MAKECMDGOALS)),32)
		ARCH = nacl
	else
		ARCH = nacl64
	endif
	COMPILER_OPTIMIZATION = -finline-functions -funroll-all-loops
	POCOFLAGS = -DPOCO_NACL -DPOCO_NACL_NEWLIB -DPOCO_NO_SYS_SELECT_H -DPOCO_NO_SIGNAL_HANDLER -DPOCO_NO_SHAREDMEMORY -DPOCO_NO_FPENVIRONMENT -DPOCO_THREAD_PRIORITY_MIN=0 -DPOCO_THREAD_PRIORITY_MAX=31
	SYSFLAGS = -D_XOPEN_SOURCE=500 -D_REENTRANT -D_THREAD_SAFE -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE
endif

# Linux
ifeq ($(ARCH),linux)
	COMPILER_OPTIMIZATION = -march=native -mtune=native -finline-functions -funroll-all-loops  -Os
endif

USER_CFLAGS = 





# you shouldn't modify anything below this line




SHELL =  /bin/sh

ifeq ($(ARCH),android)
	LIBSPATH = android
	#NDK_ROOT = $(shell cat ndk_path.make)
	TOOLCHAIN=arm-linux-androideabi-4.4.3
	TOOLCHAIN_PATH=$(NDK_ROOT)/toolchains/$(TOOLCHAIN)/prebuilt/$(HOST_PLATFORM)/bin/
	ANDROID_PREFIX=arm-linux-androideabi-
	CC=$(NDK_ROOT)/toolchains/$(TOOLCHAIN)/prebuilt/$(HOST_PLATFORM)/bin/$(ANDROID_PREFIX)gcc
	CXX=$(NDK_ROOT)/toolchains/$(TOOLCHAIN)/prebuilt/$(HOST_PLATFORM)/bin/$(ANDROID_PREFIX)g++
	AR=$(NDK_ROOT)/toolchains/$(TOOLCHAIN)/prebuilt/$(HOST_PLATFORM)/bin/$(ANDROID_PREFIX)ar
	SYSROOT=$(NDK_ROOT)/platforms/$(NDK_PLATFORM)/arch-arm/
	CFLAGS += -nostdlib --sysroot=$(SYSROOT) -fno-short-enums 
	CFLAGS += -I"$(NDK_ROOT)/platforms/$(NDK_PLATFORM)/arch-arm/usr/include" -I"$(NDK_ROOT)/sources/cxx-stl/gnu-libstdc++/include/" -I"$(NDK_ROOT)/sources/cxx-stl/gnu-libstdc++/libs/armeabi/include"
	CFLAGS += -DANDROID
else ifeq ($(ARCH),nacl)
	CXX = $(NACL_TOOLCHAIN_ROOT)/bin/i686-nacl-g++
	CFLAGS += -m32 $(POCOFLAGS) $(SYSFLAGS) -Dsigjmp_buf=jmp_buf -Dsiglongjmp=longjmp
	AR = $(NACL_TOOLCHAIN_ROOT)/bin/i686-nacl-ar
	LIBSPATH=nacl
else ifeq ($(ARCH),nacl64)
	CXX = $(NACL_TOOLCHAIN_ROOT)/bin/x86_64-nacl-g++
	CFLAGS += -m64 $(POCOFLAGS) $(SYSFLAGS) -Dsigjmp_buf=jmp_buf -Dsiglongjmp=longjmp
	AR = $(NACL_TOOLCHAIN_ROOT)/bin/x86_64-nacl-ar
	LIBSPATH=nacl64
else
	CXX =  g++
	ARCH = $(shell uname -m)
	ifeq ($(ARCH),x86_64)
		LIBSPATH=linux64
	else
		LIBSPATH=linux
	endif
endif

SOURCE_DIRS = $(shell find ../../../openFrameworks -maxdepth 1 -mindepth 1 -type d )
ifeq ($(ARCH),android)
	SOURCES = $(shell find $(SOURCE_DIRS) -name "*.cpp" | grep -v "ofAppGlutWindow.cpp" | grep -v ofGstVideoPlayer.cpp | grep -v ofQuickTimeGrabber.cpp | grep -v ofVideoPlayer.cpp | grep -v ofGstVideoGrabber.cpp | grep -v ofSoundPlayer.cpp | grep -v ofOpenALSoundPlayer.cpp)
else ifeq ($(findstring nacl,$(ARCH)),nacl)
	SOURCES = $(shell find $(SOURCE_DIRS) -name "*.cpp" | grep -v ofURLFileLoader.cpp | grep -v ofUtils.cpp | grep -v ofDirectShowGrabber.cpp | grep -v ofGstUtils.cpp | grep -v ofGstVideoGrabber.cpp | grep -v ofGstVideoPlayer.cpp | grep -v ofQuickTimeGrabber.cpp | grep -v ofQuickTimePlayer.cpp | grep -v ofVideoGrabber.cpp | grep -v ofVideoPlayer.cpp | grep -v ofBitmapFont.cpp | grep -v ofCairoRenderer.cpp | grep -v ofGraphics.cpp | grep -v ofImage.cpp | grep -v ofTrueTypeFont.cpp | grep -v ofArduino.cpp | grep -v ofSerial.cpp | grep -v ofAppGlutWindow.cpp | grep -v ofAppRunner.cpp | grep -v ofCamera.cpp | grep -v ofNode.cpp | grep -v ofFbo.cpp | grep -v ofGLRenderer.cpp | grep -v ofLight.cpp | grep -v ofMaterial.cpp | grep -v ofShader.cpp | grep -v ofTexture.cpp | grep -v ofVbo.cpp | grep -v ofVboMesh.cpp)
else
	SOURCES = $(shell find $(SOURCE_DIRS) -name "*.cpp") 
endif
OBJFILES = $(patsubst ../../../%.cpp,%.o,$(SOURCES))
CORE_INCLUDES = $(shell find ../../../openFrameworks/ -type d)
CORE_INCLUDE_FLAGS = $(addprefix -I,$(CORE_INCLUDES))
INCLUDES = $(shell find ../../../*/include -type d | grep -v glu | grep -v quicktime | grep -v poco)
INCLUDES_FLAGS = $(addprefix -I,$(INCLUDES))
INCLUDES_FLAGS += -I../../../poco/include
ifeq ($(ARCH),android)
	INCLUDES_FLAGS += -I../../../glu/include_android
 	INCLUDES_FLAGS += -I../../../../addons/ofxAndroid/src
else
 	INCLUDES_FLAGS += -I../../../glu/include
endif

CFLAGS += -Wall -fexceptions
CFLAGS += $(INCLUDES_FLAGS)
CFLAGS += $(CORE_INCLUDE_FLAGS)


ifeq ($(ARCH),linux)
	CFLAGS += $(shell pkg-config  gstreamer-0.10 gstreamer-video-0.10 gstreamer-base-0.10 libudev --cflags)
	#check if gtk exists and add it
	GTK = $(shell pkg-config gtk+-2.0 --exists; echo $$?)
	ifeq ($(GTK),0)
		CFLAGS += $(shell pkg-config gtk+-2.0 --cflags) -DOF_USING_GTK
	endif
	#check if mpg123 exists and add it
	MPG123 = $(shell pkg-config libmpg123 --exists; echo $$?)
	ifeq ($(MPG123),0)
		CFLAGS += -DOF_USING_MPG123
	endif
endif

ifeq ($(findstring Debug,$(MAKECMDGOALS)),Debug)
	TARGET_CFLAGS = -g3
	TARGET_NAME = $(MAKECMDGOALS)
	TARGET = ../../lib/$(LIBSPATH)/libopenFrameworksDebug.a
endif

ifeq ($(findstring Release,$(MAKECMDGOALS)),Release)
	TARGET_CFLAGS = $(COMPILER_OPTIMIZATION)
	TARGET_NAME = $(MAKECMDGOALS)
	TARGET = ../../lib/$(LIBSPATH)/libopenFrameworks.a
endif

ifeq ($(findstring Release_arm7,$(MAKECMDGOALS)),Release_arm7)
	TARGET_CFLAGS += -march=armv7-a -mfloat-abi=softfp -mfpu=neon
	TARGET = ../../lib/$(LIBSPATH)/libopenFrameworks_arm7.a
endif

ifeq ($(MAKECMDGOALS),)
	TARGET_CFLAGS = $(COMPILER_OPTIMIZATION)
	TARGET_NAME = Release
	TARGET = ../../lib/$(LIBSPATH)/libopenFrameworks.a
endif

ifeq ($(MAKECMDGOALS),clean)
	TARGET = ../../lib/$(LIBSPATH)/libopenFrameworks.a ../../lib/$(LIBSPATH)/libopenFrameworksDebug.a
endif

ifeq ($(MAKECMDGOALS),cleanAndroid)
	TARGET = ../../lib/$(LIBSPATH)/libopenFrameworks.a ../../lib/$(LIBSPATH)/libopenFrameworksDebug.a ../../lib/$(LIBSPATH)/libopenFrameworks_arm7.a
endif

ifeq ($(MAKECMDGOALS),cleanDebug)
	TARGET_NAME = Debug
endif

ifeq ($(MAKECMDGOALS),cleanRelease)
	TARGET_NAME = Release
endif

CLEANTARGET = $(addprefix clean,$(TARGET_NAME))
OBJ_OUTPUT = obj/$(TARGET_NAME)/
DEPFILES = $(addprefix $(OBJ_OUTPUT),$(patsubst ../../../%.cpp,%.d,$(SOURCES)))
OBJS = $(addprefix $(OBJ_OUTPUT), $(OBJFILES))

.PHONY: all Debug Release after clean cleanDebug cleanRelease help DebugAndroid ReleaseAndroid Release_arm7Android cleanAndroid ReleaseNaCl32 ReleaseNaCl64

Release: $(TARGET) 

Debug: $(TARGET) 

DebugAndroid: $(TARGET)

ReleaseAndroid: $(TARGET)

Release_arm7Android: $(TARGET)

ReleaseNaCl32: $(TARGET)

ReleaseNaCl64: $(TARGET)

all: 
	$(MAKE) Debug
	$(MAKE) Release
	
android:
	$(MAKE) DebugAndroid
	$(MAKE) ReleaseAndroid
	$(MAKE) Release_arm7Android

#This rule does the compilation
$(OBJ_OUTPUT)%.o: ../../../%.cpp 
	@echo "compiling object for " $<
	mkdir -p $(@D)
	$(CXX) $(TARGET_CFLAGS) $(CFLAGS) $(USER_CFLAGS) -MMD -MP -MF$(OBJ_OUTPUT)$*.d -MT$(OBJ_OUTPUT)$*.o -o $@ -c $<

$(TARGET) : $(OBJS) 
	echo "creating " $(TARGET)
	$(AR) -r "$@" $(OBJS)

-include $(DEPFILES)

#.PHONY: clean cleanDebug cleanRelease
clean:
	rm -Rf obj
	rm -f -v $(TARGET)
	
$(CLEANTARGET):
	rm -Rf -v $(OBJ_OUTPUT)
	rm -f -v $(TARGET)
	
cleanAndroid:
	rm -Rf obj
	rm -f -v $(TARGET)

#.PHONY: help
help:
 
	@echo 
	@echo openFrameworks compiled library makefile
	@echo
	@echo targets:
	@echo "make Debug:		builds the library with debug symbols"
	@echo "make Release:		builds the library with optimizations"
	@echo "make:			= make Release"
	@echo "make all:		= make Debug + make Release"
	@echo "make cleanDebug:	cleans the Debug target"
	@echo "make cleanRelease:	cleans the Release target"
	@echo "make clean:		cleans everything"
	@echo

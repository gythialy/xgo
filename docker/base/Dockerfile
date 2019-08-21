# Go cross compiler (xgo): Base cross-compilation layer
# Copyright (c) 2014 Péter Szilágyi. All rights reserved.
#
# Released under the MIT license.

FROM ubuntu:18.04

LABEL MAINTAINER Péter Szilágyi <peterke@gmail.com>
LABEL MAINTAINER Goren G <yong.gu@qlink.mobi>

# Mark the image as xgo enabled to support xgo-in-xgo
ENV XGO_IN_XGO 1

# Configure the Go environment, since it's not going to change
ENV PATH   /usr/local/go/bin:$PATH
ENV GOPATH /go

ADD . /

ENV FETCH /fetch.sh
ENV UPDATE_IOS /update_ios.sh
ENV BOOTSTRAP /bootstrap.sh
ENV BOOTSTRAP_PURE /bootstrap_pure.sh
ENV BOOTSTRAP_REPO /bootstrap_repo.sh
ENV BUILD_DEPS /build_deps.sh
ENV BUILD /build.sh
ENV OSX_CROSS /osx-cross.sh

RUN chmod +x $FETCH && chmod +x $UPDATE_IOS && chmod +x $BOOTSTRAP && chmod +x $BOOTSTRAP_REPO && chmod +x $BOOTSTRAP_PURE && \
       chmod +x $BUILD_DEPS && chmod +x $BUILD && chmod +x $OSX_CROSS

# Make sure apt-get is up to date and dependent packages are installed
RUN apt-get update -qq && \
      apt-get install -y -q --no-install-recommends \ 
      apt-utils automake autogen build-essential ca-certificates curl                      \
      gcc-6-arm-linux-gnueabi g++-6-arm-linux-gnueabi libc6-dev-armel-cross                \
      gcc-6-arm-linux-gnueabihf g++-6-arm-linux-gnueabihf libc6-dev-armhf-cross            \
      gcc-6-aarch64-linux-gnu g++-6-aarch64-linux-gnu libc6-dev-arm64-cross                \
      gcc-6-mips-linux-gnu g++-6-mips-linux-gnu libc6-dev-mips-cross                       \
      gcc-6-mipsel-linux-gnu g++-6-mipsel-linux-gnu libc6-dev-mipsel-cross                 \
      gcc-6-mips64-linux-gnuabi64 g++-6-mips64-linux-gnuabi64 libc6-dev-mips64-cross       \
      gcc-6-mips64el-linux-gnuabi64 g++-6-mips64el-linux-gnuabi64 libc6-dev-mips64el-cross \
      gcc-6-multilib gcc-7-multilib g++-6-multilib gcc-mingw-w64 g++-mingw-w64 clang llvm-dev \
      libtool libxml2-dev uuid-dev libssl-dev swig openjdk-8-jdk pkg-config patch          \
      make xz-utils cpio wget zip unzip p7zip git mercurial bzr texinfo help2man cmake   \
      file --no-install-recommends && \
      apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
      ln -s /usr/include/asm-generic /usr/include/asm 

# RUN git clone https://github.com/mackyle/xar.git && \
#     cd /xar/xar && ./autogen.sh && make && make install && cd /

# Configure the container for OSX cross compilation
# ENV OSX_SDK     MacOSX10.10.sdk
# ENV OSX_NDK_X86 /usr/local/osx-ndk-x86

# RUN OSX_SDK_PATH=https://s3.dockerproject.org/darwin/v2/${OSX_SDK}.tar.xz && \
#   $FETCH $OSX_SDK_PATH 631b4144c6bf75bf7a4d480d685a9b5bda10ee8d03dbf0db829391e2ef858789       && \
#   tar -xf `basename $OSX_SDK_PATH` && rm -f `basename $OSX_SDK_PATH`

# RUN tar -cf - $OSX_SDK/ | xz -c - > $OSX_SDK.tar.xz && rm -rf $OSX_SDK && \
#   git clone https://github.com/tpoechtrager/osxcross.git && \
#   git checkout -q a9317c18a3a457ca0a657f08cc4d0d43c6cf8953 && \
#   mv  $OSX_SDK.tar.xz /osxcross/tarballs/ && \
#   UNATTENDED=yes OSX_VERSION_MIN=10.10 /osxcross/build.sh && \
#   LIBTOOL_VERSION=2.4.6 && \
#   OSX_CODENAME=yosemite && \
#   curl -sSL https://homebrew.bintray.com/bottles/libtool-${LIBTOOL_VERSION}.${OSX_CODENAME}.bottle.tar.gz \
# 	| gzip -dc | tar xf - \
# 		-C /osxcross/target/SDK/${OSX_SDK}/usr/ \
# 		--strip-components=2 \
# 		libtool/${LIBTOOL_VERSION}/include/ \
# 		libtool/${LIBTOOL_VERSION}/lib/ \
#   mv /osxcross/target $OSX_NDK_X86 &&   \
#   rm -rf /osxcross

RUN $OSX_CROSS

ENV OSX_NDK_X86 /usr/local/osx-ndk-x86
ENV PATH /usr/local/osx-ndk-x86/bin:$PATH

ENTRYPOINT ["/build.sh"]

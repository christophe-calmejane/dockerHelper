# Base our image from ubuntu jammy
FROM ubuntu:22.04

# Update apt sources (jammy has been moved to old-releases)
# RUN sed -i -r 's/([a-z]{2}.)?archive.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list && sed -i -r 's/security.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list

# Install required packages
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get -qq -y install \
       wget \
       vim \
       sudo \
       git \
       build-essential \
       ninja-build \
       g++ \
       flex \
       python3 \
       ncurses-dev \
       libpcap-dev \
       language-pack-en \
       '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev libfontconfig-dev fontconfig
       RUN rm -rf /var/lib/apt/lists/*

# Install more recent CMake
ARG CMAKE_VERSION=3.25.2

RUN mkdir ~/cmake-install \
       && cd ~/cmake-install \
       && wget https://github.com/Kitware/CMake/releases/download/v$CMAKE_VERSION/cmake-$CMAKE_VERSION.tar.gz \
       && tar -xzvf cmake-$CMAKE_VERSION.tar.gz \
       && cd cmake-$CMAKE_VERSION/ \
       && ./bootstrap -- -DCMAKE_USE_OPENSSL=OFF \
       && make -j$(nproc) \
       && make install

# Install more recent Qt
ARG QT_VERSION=6.4.2

RUN git clone https://code.qt.io/qt/qt5.git qt6 \
       && cd qt6 \
       && git checkout $QT_VERSION \
       && perl init-repository --module-subset=essential \
       && mkdir _build \
       && cd _build \
       && ../configure -opensource -nomake examples -nomake tests -confirm-license -skip webengine -skip 3d -skip location -no-opengl -fontconfig \
       && cmake --build . --parallel \
       && cmake --build . --target install \
       && cd ../.. \
       && rm -rf qt6
# Qt is installed in /usr/local/Qt-$QT_VERSION
# Qt6Config.cmake is installed in /usr/local/Qt-$QT_VERSION/lib/cmake

# Create a ld.so.conf.d file to help find Qt
RUN echo "/usr/local/Qt-$QT_VERSION/lib" > /etc/ld.so.conf.d/qt.conf \
       && ldconfig

# Add an environment variable to help differentiate between the host and the container
ENV IN_DOCKER_CONTAINER=1

# Create a user
ARG USERNAME=builder
ARG PASSWORD=builder

RUN useradd -rm -d /home/$USERNAME -s /bin/bash -g root -G sudo -u 1000 $USERNAME
RUN echo "$PASSWORD\n$PASSWORD" | passwd $USERNAME
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Switch to user
WORKDIR /home/$USERNAME
USER $USERNAME

# Add Qt bin to PATH
RUN echo "export PATH=/usr/local/Qt-$QT_VERSION/bin:\$PATH" >> ~/.bashrc

# Set UTF8 locale
RUN echo "export LANG=en_US.utf8" >> ~/.bashrc

# Create a folder to mount sources
RUN mkdir -p ~/sources

# Create a folder to mount build tree
RUN mkdir -p ~/builds

# Start in sources
WORKDIR /home/$USERNAME/sources

# Default command
CMD [ "bash" ]

# Base our image from ubuntu noble
FROM ubuntu:24.04

# Update apt sources (noble has been moved to old-releases)
# RUN sed -i -r 's/([a-z]{2}.)?archive.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list && sed -i -r 's/security.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list

# Install required packages
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get -qq -y install \
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
       curl \
       zip \
       unzip \
       libssl-dev \
       && rm -rf /var/lib/apt/lists/*

# Install more recent CMake
ARG CMAKE_VERSION=3.29.3

RUN mkdir ~/cmake-install \
       && cd ~/cmake-install \
       && wget https://github.com/Kitware/CMake/releases/download/v$CMAKE_VERSION/cmake-$CMAKE_VERSION.tar.gz \
       && tar -xzvf cmake-"$CMAKE_VERSION".tar.gz \
       && cd cmake-"$CMAKE_VERSION"/ \
       && ./bootstrap -- -DCMAKE_USE_OPENSSL=ON \
       && make -j$(nproc) \
       && make install

# Add an environment variable to help differentiate between the host and the container
ENV IN_DOCKER_CONTAINER=1

# Create a user
ARG USERNAME=builder
ARG PASSWORD=builder

RUN useradd -rm -d /home/"$USERNAME" -s /bin/bash -g root -G sudo -u 1001 "$USERNAME"
RUN echo "$PASSWORD\n$PASSWORD" | passwd "$USERNAME"
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Switch to user
WORKDIR /home/$USERNAME
USER $USERNAME

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

FROM ubuntu:noble-20240605 AS ubuntu_base

WORKDIR /root

RUN apt-get update \
    && DEBIAN_FRONTEND="noninteractive" apt-get install -y --no-install-recommends \
    ca-certificates \
    locales \
    wget \
    ssh \
    libssl-dev \
    git \
    make \
    cmake \
    gcc g++ gfortran lcov gdb\
    clang-format\
    clang-tidy\
    libblas-dev \
    liblapack-dev \
    libsuitesparse-dev \
    libomp-dev \
    doxygen\
    python3-pip python3-dev python3-venv \
    && python3 -m venv /usr/local/share/venv \
    && . /usr/local/share/venv/bin/activate \
    && python3 -m pip install --break-system-packages --no-cache-dir --upgrade\
    pip cmakelang \
    && rm -rf /var/lib/apt/lists/*

FROM debian:testing AS builder

# Install Openblas, git, python3, cmake, make, wget and xz-utils
RUN apt update \
    && apt dist-upgrade -y \
    && apt install -y --no-install-recommends gfortran-13 libopenblas-dev git \
       ca-certificates python3 cmake ninja-build make m4 wget xz-utils zlib1g-dev libaec-dev \
    && apt clean \
    && rm -rf /var/lib/apt/lists /var/cache/apt

# Set default compilers
ENV CC=gcc-13 FC=gfortran-13

# Clone and Build pFUnit
RUN git clone --recursive --depth=1 --single-branch --branch main https://github.com/Goddard-Fortran-Ecosystem/pFUnit  \
    && cd pFUnit \
    && cmake \
      -Wno-dev \
      -DCMAKE_BUILD_TYPE=Debug \
      -DCMAKE_INSTALL_PREFIX=/opt/pfunit \
      -Bbuild \
      -S. \
    && cmake --build build --parallel $(nproc) \
    && cmake --install build \
    && cd .. \
    && rm -rf ./pFUnit

# Download and Build HDF5
RUN wget https://github.com/HDFGroup/hdf5/releases/download/hdf5_1.14.5/hdf5-1.14.5.tar.gz \
    && tar -xf hdf5-1.14.5.tar.gz \
    && rm hdf5-1.14.5.tar.gz \
    && cd hdf5-1.14.5 \
    && cmake \
      -GNinja \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_INSTALL_PREFIX=/usr/local \
      -DHDF5_BUILD_CPP_LIB=OFF \
      -DHDF5_BUILD_FORTRAN=ON \
      -DBUILD_TESTING=OFF \
      -DBUILD_STATIC_LIBS=OFF \
      -Bbuild -S. \
    && cmake --build build \
    && cmake --install build --strip \
    && cd .. \
    && rm -rf ./hdf5-1.14.5

CMD ["/bin/bash"]

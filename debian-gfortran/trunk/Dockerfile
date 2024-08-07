FROM debian:experimental AS builder

# Install Openblas, git, python3, cmake, make, wget and xz-utils
RUN apt update \
    && apt dist-upgrade -y \
    && apt install -t experimental -y --no-install-recommends gfortran-14 \
       libopenblas-dev git libcurl3t64-gnutls ca-certificates python3 cmake make \
       ninja-build m4 wget xz-utils \
    && apt clean \
    && rm -rf /var/lib/apt/lists /var/cache/apt

# Set default compilers
ENV CC=gcc-14 FC=gfortran-14

# Clone and Build pFUnit
RUN git clone --recursive --depth=1 --single-branch --branch main https://github.com/Goddard-Fortran-Ecosystem/pFUnit  \
    && cd pFUnit \
    && mkdir build \
    && cd build \
    && cmake .. -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=/opt/pfunit \
    && cmake --build . --parallel $(nproc) \
    && cmake --install . \
    && cd ../.. \
    && rm -rf ./pFUnit

# Download and Build HDF5
RUN wget https://github.com/HDFGroup/hdf5/archive/hdf5_1.14.4.2/hdf5-1.14.4.2.tar.gz \
    && tar -xf hdf5-1.14.4.2.tar.gz \
    && rm hdf5-1.14.4.2.tar.gz \
    && cd hdf5-hdf5_1.14.4.2 \
    && mkdir build \
    && cd build \
    && cmake .. \
      -GNinja \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_INSTALL_PREFIX=/usr/local/hdf5 \
      -DHDF5_BUILD_CPP_LIB=OFF \
      -DHDF5_BUILD_FORTRAN=ON \
      -DBUILD_TESTING=OFF \
      -DBUILD_STATIC_LIBS=OFF \
    && ninja \
    && ninja install/strip \
    && cd ../.. \
    && rm -rf ./hdf5-hdf5-1.14.4.2

CMD ["/bin/bash"]

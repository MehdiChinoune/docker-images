FROM debian:testing AS builder

# Install Openblas, git, python3, cmake, make, wget and xz-utils
RUN apt update \
    && apt dist-upgrade -y \
    && apt install -y --no-install-recommends gfortran-10 libopenblas-dev git \
       ca-certificates python3 cmake make m4 wget xz-utils \
    && cd /usr/bin \
    && ln -s /usr/bin/gfortran-10 gfortran \
    && ln -s /usr/bin/gcc-10 gcc \
    && apt clean \
    && rm -rf /var/lib/apt/lists /var/cache/apt

# Clone and Build pFUnit
RUN git clone --depth=1 --single-branch --branch main https://github.com/Goddard-Fortran-Ecosystem/pFUnit  \
    && cd pFUnit \
    && mkdir build \
    && cd build \
    && cmake .. -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=/opt/pfunit \
    && make -j$(nproc) \
    && make install \
    && cd ../.. \
    && rm -rf ./pFUnit

# Download and Build HDF5
RUN wget https://github.com/HDFGroup/hdf5/archive/hdf5-1_12_0.tar.gz \
    && tar -xvf hdf5-1_12_0.tar.gz \
    && rm hdf5-1_12_0.tar.gz \
    && cd hdf5-hdf5-1_12_0 \
    && mkdir build \
    && cd build \
    && cmake .. -DCMAKE_BUILD_TYPE=Release -DHDF5_BUILD_CPP_LIB=OFF -DHDF5_BUILD_FORTRAN=ON \
    && make -j$(nproc) \
    && make install/strip \
    && cd ../.. \
    && rm -rf ./hdf5-hdf5-1_12_0

CMD ["/bin/bash"]
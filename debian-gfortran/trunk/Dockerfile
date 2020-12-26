FROM debian:testing AS builder

# Install git, python3, cmake, make, wget and xz-utils
RUN apt update \
    && apt dist-upgrade -y \
    && apt install -y --no-install-recommends git ca-certificates \
       python3 cmake make m4 binutils libisl23 libmpc3 libc6-dev \
       wget xz-utils \
    && apt clean \
    && rm -rf /var/lib/apt/lists /var/cache/apt

# Clone and Build gcc-trunk
RUN git clone --depth=1 --single-branch --branch master git://gcc.gnu.org/git/gcc.git gcc \
    && apt update \
    && apt dist-upgrade -y \
    && apt install -y --no-install-recommends g++-10 libmpc-dev libisl-dev bison flex \
    && export CC=gcc-10 CXX=g++-10 \
    && cd gcc \
    && mkdir build \
    && cd build \
    && ../configure --enable-languages=c,c++,fortran --disable-multilib --disable-bootstrap \
    && make -j"$(nproc)" \
    && make install-strip \
    && cd ../.. \
    && rm -rf ./gcc \
    && echo '/usr/local/lib64' > /etc/ld.so.conf.d/local-lib64.conf \
    && ldconfig -v \
    && apt purge --autoremove -y gcc-10 libmpc-dev libisl-dev bison flex \
    && apt clean \
    && rm -rf /var/lib/apt/lists /var/cache/apt

# Clone and Build OpenBLAS
RUN git clone --depth=1 --single-branch --branch develop https://github.com/xianyi/OpenBLAS \
    && cd OpenBLAS \
    && mkdir build \
    && cd build \
    && cmake .. -DBUILD_SHARED_LIBS=ON -DDYNAMIC_ARCH=TRUE \
    && make -j$(nproc) \
    && make install/strip \
    && ldconfig -v \
    && cd ../.. \
    && rm -rf ./OpenBLAS

# Clone and Build pFUnit
RUN git clone --depth=1 --single-branch --branch main https://github.com/Goddard-Fortran-Ecosystem/pFUnit \
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
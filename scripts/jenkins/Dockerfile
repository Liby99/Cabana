FROM nvidia/cuda:10.1-devel-ubuntu18.04
RUN apt-get update && apt-get install -y \
        bc \
        wget \
        openssh-client \
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install CMake
RUN export CMAKE_VERSION=3.13.4 && \
    export CMAKE_VERSION_SHORT=3.13 && \
    export CMAKE_URL=https://cmake.org/files/v${CMAKE_VERSION_SHORT}/cmake-${CMAKE_VERSION}-Linux-x86_64.sh && \
    export CMAKE_SCRIPT=cmake-${CMAKE_VERSION}-Linux-x86_64.sh && \
    wget --quiet ${CMAKE_URL} --output-document=${CMAKE_SCRIPT} && \
    sh ${CMAKE_SCRIPT} --skip-license --prefix=/usr && \
    rm ${CMAKE_SCRIPT}

# Install CUDA-aware Open MPI
RUN export OPENMPI_VERSION=4.0.0 && \
    export OPENMPI_VERSION_SHORT=4.0 && \
    export OPENMPI_SHA1=fee1d0287abfb150bae16957de342752c9bdd4e8 && \
    export OPENMPI_URL=https://www.open-mpi.org/software/ompi/v${OPENMPI_VERSION_SHORT}/downloads/openmpi-${OPENMPI_VERSION}.tar.bz2 && \
    export OPENMPI_ARCHIVE=openmpi-${OPENMPI_VERSION}.tar.bz2 && \
    export OPENMPI_SOURCE_DIR=openmpi-${OPENMPI_VERSION} && \
    export OPENMPI_INSTALL_PREFIX=/usr && \
    wget --quiet ${OPENMPI_URL} --output-document=${OPENMPI_ARCHIVE} && \
    echo "${OPENMPI_SHA1} ${OPENMPI_ARCHIVE}" | sha1sum -c && \
    mkdir -p ${OPENMPI_SOURCE_DIR} && \
    tar -xf ${OPENMPI_ARCHIVE} -C ${OPENMPI_SOURCE_DIR} --strip-components=1 && \
    cd ${OPENMPI_SOURCE_DIR} && mkdir build && cd build && \
    ../configure --prefix=${OPENMPI_INSTALL_PREFIX} --with-cuda && \
    make -j8 install && \
    rm -rf ${OPENMPI_ARCHIVE} ${OPENMPI_SOURCE_DIR}

# Install Kokkos
ENV KOKKOS_DIR=/opt/kokkos
RUN KOKKOS_VERSION=master && \
    KOKKOS_URL=https://github.com/kokkos/kokkos/archive/${KOKKOS_VERSION}.tar.gz && \
    KOKKOS_ARCHIVE=kokkos-${KOKKOS_VERSION}.tar.gz && \
    SCRATCH_DIR=/scratch && mkdir -p ${SCRATCH_DIR} && cd ${SCRATCH_DIR} && \
    wget --quiet ${KOKKOS_URL} --output-document=${KOKKOS_ARCHIVE} && \
    mkdir -p kokkos && \
    tar -xf ${KOKKOS_ARCHIVE} -C kokkos --strip-components=1 && \
    cd kokkos && \
    mkdir -p build && cd build && \
    cmake \
      -D CMAKE_INSTALL_PREFIX=${KOKKOS_DIR} \
      -D CMAKE_BUILD_TYPE=Debug \
      -D CMAKE_CXX_COMPILER=/scratch/kokkos/bin/nvcc_wrapper \
      -D Kokkos_ENABLE_CUDA=ON \
      -D Kokkos_ENABLE_CUDA_LAMBDA=ON \
      -D Kokkos_ARCH_VOLTA70=ON
    .. && \
    make && make install && \
    rm -rf ${SCRATCH_DIR}

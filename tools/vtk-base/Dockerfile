FROM nvidia/cudagl:10.2-devel-ubuntu18.04

RUN apt-get update && apt-get install -q -y \
    git build-essential python3-dev wget curl unzip \
    libtbb-dev wget curl p7zip-full sudo \
    libxkbcommon-x11-0 libfontconfig1 libdbus-1-3 \
    && rm -rf /var/lib/apt/lists/*

# Modern CMake
WORKDIR /CMake
RUN wget https://github.com/Kitware/CMake/releases/download/v3.16.4/cmake-3.16.4.tar.gz && tar -xvf cmake-3.16.4.tar.gz
WORKDIR /CMake/cmake-3.16.4
RUN bash bootstrap -- -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_USE_OPENSSL=OFF && make -j3 && make install

WORKDIR /Qt
RUN wget https://code.qt.io/cgit/qbs/qbs.git/plain/scripts/install-qt.sh
RUN bash install-qt.sh --version 5.14.1 qtbase qtx11extras qtwebview qtwebsockets qtwebglplugin qtwebengine qtwebchannel qttools qtsvg qtscxml qtscript qtremoteobjects qtquicktimeline qtquickcontrols2 qtquickcontrols qtquick3d qtnetworkauth qtmultimedia qtimageformats qtlocation qtgraphicaleffects qtdeclarative qtdatavis3d qtconnectivity qtcharts qt3d
RUN bash install-qt.sh --version 4.11.1 qtcreator

RUN git clone --branch v8.2.0 --recursive https://gitlab.kitware.com/vtk/vtk.git /VTK
WORKDIR /VKT-build

# VTK specific
RUN apt-get update && apt-get install -q -y \
    libxt-dev \
    && rm -rf /var/lib/apt/lists/*

RUN cmake \
    -DVTK_QT_VERSION:STRING=5 \
    -DQT_QMAKE_EXECUTABLE:PATH=/opt/Qt/5.14.1/gcc_64/bin/qmake \
    -DVTK_Group_Qt:BOOL=ON \
    -DCMAKE_USE_OPENSSL:BOOL=OFF \
    -DBUILD_TESTING:BOOL=OFF \
    -DCMAKE_PREFIX_PATH:PATH=/opt/Qt/5.14.1/gcc_64/lib/cmake  \
    -DBUILD_SHARED_LIBS:BOOL=ON \
    /VTK
RUN make -j3
RUN make install

# Create the home directory for the new app user.
RUN apt-get update && apt-get install -y sudo && rm -rf /var/lib/apt/lists/*
ARG LOCAL_USER_ID
ENV LOCAL_USER_ID=${LOCAL_USER_ID}
RUN useradd --shell /bin/bash -u ${LOCAL_USER_ID} -o -c "" -m developer -G sudo
RUN echo "developer ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers.d/nosudo
USER developer

ENTRYPOINT ["/bin/bash"]

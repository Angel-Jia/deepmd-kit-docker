FROM nvidia/cuda:11.3.0-cudnn8-devel-ubuntu20.04

ENV LANG C.UTF-8
ENV OFED_VERSION=5.3-1.0.0.1

# https://github.com/dptech-corp/Uni-Core/blob/main/docker/cu116/Dockerfile
RUN APT_INSTALL="apt-get install -y --no-install-recommends" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        software-properties-common \
        && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        build-essential \
        apt-utils \
        ca-certificates \
        wget \
        git \
        vim \
        libssl-dev \
        curl \
        unzip \
        unrar \
        cmake \
        net-tools \
        sudo \
        autotools-dev \
        rsync \
        jq \
        openssh-server \
        tmux \
        screen \
        htop \
        pdsh \
        openssh-client \
        lshw \
        dmidecode \
        util-linux \
        automake \
        autoconf \
        libtool \
        net-tools \
        pciutils \
        libpci-dev \
        libaio-dev \
        libcap2 \
        libtinfo5 \
        fakeroot \
        devscripts \
        debhelper \
        nfs-common

# ==================================================================
# InfiniBand & RDMA
# ------------------------------------------------------------------

RUN cd /tmp && \
    wget -q http://content.mellanox.com/ofed/MLNX_OFED-${OFED_VERSION}/MLNX_OFED_LINUX-${OFED_VERSION}-ubuntu20.04-x86_64.tgz && \
    tar xzf MLNX_OFED_LINUX-${OFED_VERSION}-ubuntu20.04-x86_64.tgz && \
    MLNX_OFED_LINUX-${OFED_VERSION}-ubuntu20.04-x86_64/mlnxofedinstall --user-space-only --without-fw-update --force --all && \
    rm -rf /tmp/MLNX_OFED_LINUX-${OFED_VERSION}*


RUN cd /tmp && \
    mkdir -p /usr/local/nccl-rdma-sharp-plugins && \
    DEBIAN_FRONTEND=noninteractive apt install -y zlib1g-dev && \
    git clone --depth=1 https://github.com/Mellanox/nccl-rdma-sharp-plugins.git && \
    cd nccl-rdma-sharp-plugins && \
    ./autogen.sh && \
    ./configure --prefix=/usr/local/nccl-rdma-sharp-plugins --with-cuda=/usr/local/cuda && \
    make && \
    make install


# ==================================================================
# python
# ------------------------------------------------------------------

# Set timezone
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
ENV PATH /usr/local/nvidia/bin:/usr/local/nvidia/lib64:$PATH
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib64:/usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENV PYTHON_VERSION=3.8
RUN wget -O ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh
ENV PATH /opt/conda/bin:$PATH

RUN conda install -y python=3.8  && conda clean -ya

RUN conda install -y scipy scikit-learn pyyaml tensorboard tensorboardX && \
    conda clean -ya

RUN ldconfig

# ==================================================================
# tensorflow
# ------------------------------------------------------------------

RUN conda install -y numpy pyyaml scipy ipython ninja cython typing pandas pip && \
    conda clean -ya
RUN pip install tensorflow-gpu==2.6 && rm -r ~/.cache/pip



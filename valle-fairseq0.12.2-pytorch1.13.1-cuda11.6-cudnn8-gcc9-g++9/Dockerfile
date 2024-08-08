FROM pytorch/pytorch:1.13.1-cuda11.6-cudnn8-devel

LABEL MAINTAINER="nzk020109@gmail.com"

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN set -x \
    && apt-get update \
    && apt-get -y install wget curl pkg-config man git less openssl libssl-dev unzip unar build-essential aria2 tmux cmake vim ninja-build htop \
    && apt-get install -y openssh-server software-properties-common manpages-dev sox libsox-fmt-all libsox-fmt-mp3 libgoogle-perftools-dev libsndfile1-dev ffmpeg \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean
    
RUN set -x \
    && add-apt-repository ppa:ubuntu-toolchain-r/test -y \
    && apt-get update -y \
    && apt install gcc-9 g++-9 -y \
    && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9 --slave /usr/bin/g++ g++ /usr/bin/g++-9 --slave /usr/bin/gcov gcov /usr/bin/gcov-9 \
    && rm -rf /var/lib/apt/lists/*

ENV STAGE_DIR=/tmp
RUN mkdir -p ${STAGE_DIR}

RUN git clone https://github.com/google/sentencepiece.git ${STAGE_DIR}/sentencepiece

RUN cd ${STAGE_DIR}/sentencepiece && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make -j $(nproc) && \
    make install && \
    ldconfig -v

RUN pip install --no-cache-dir packaging editdistance gpustat wandb einops debugpy tqdm soundfile matplotlib scipy sentencepiece pandas \
    && pip install torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu116

WORKDIR /workspace
RUN git clone https://github.com/facebookresearch/fairseq.git \
    && cd fairseq \
    && git checkout tags/v0.12.2 \
    && pip install --editable ./ \
    && pip install --no-cache-dir npy-append-array librosa pyarrow fairscale timm triton

ENV PATH /usr/local/cuda-11.6/bin:$PATH
ENV CUDA_HOME /usr/local/cuda
ENV LD_LIBRARY_PATH "/usr/local/cuda-11.6/lib64:$LD_LIBRARY_PATH"

RUN git clone https://github.com/NVIDIA/apex \
    && cd apex \
    && pip install -v --disable-pip-version-check --no-cache-dir --no-build-isolation --global-option="--cpp_ext" --global-option="--cuda_ext" ./

ENV FORCE_CUDA="1"
ENV TORCH_CUDA_ARCH_LIST="7.0 7.5 8.0"

RUN git clone https://github.com/facebookresearch/xformers \
    && cd /workspace/xformers \
    && git checkout tags/v0.0.20 \
    && git submodule update --init --recursive \
    && pip install --no-cache-dir -e .

ENV SHELL=/bin/bash
CMD [ "/bin/bash" ]
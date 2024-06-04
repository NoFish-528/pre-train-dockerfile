FROM pytorch/pytorch:1.13.1-cuda11.6-cudnn8-devel

LABEL MAINTAINER="nzk020109@gmail.com"

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN set -x \
    && apt-get update \
    && apt-get -y install wget curl man git less openssl libssl-dev unzip unar build-essential aria2 tmux vim ninja-build \
    && apt-get install -y openssh-server software-properties-common manpages-dev sox libsox-fmt-all libsox-fmt-mp3 libsndfile1-dev ffmpeg \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean
    
RUN set -x \
    && add-apt-repository ppa:ubuntu-toolchain-r/test -y \
    && apt-get update -y \
    && apt install gcc-9 g++-9 -y \
    && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9 --slave /usr/bin/g++ g++ /usr/bin/g++-9 --slave /usr/bin/gcov gcov /usr/bin/gcov-9 \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir packaging editdistance gpustat wandb einops debugpy tqdm soundfile matplotlib scipy sentencepiece pandas \
    && pip install torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu116

WORKDIR /workspace
RUN git clone https://github.com/facebookresearch/fairseq.git \
    && cd fairseq \
    && git checkout tags/v0.12.2 \
    && pip install --editable ./ \
    && pip install --no-cache-dir npy-append-array librosa pyarrow

ENV SHELL=/bin/bash
CMD [ "/bin/bash" ]
FROM pytorch/pytorch:1.12.1-cuda11.3-cudnn8-devel

LABEL MAINTAINER="nzk020109@gmail.com"

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN set -x \
    && apt-get update \
    && apt-get -y install wget curl man git less openssl libssl-dev unzip unar build-essential aria2 tmux vim ninja-build\
    && apt-get install -y openssh-server sox libsox-fmt-all libsox-fmt-mp3 libsndfile1-dev ffmpeg \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN pip install --no-cache-dir packaging editdistance gpustat wandb einops debugpy tqdm soundfile matplotlib scipy sentencepiece pandas \
    && pip install torchaudio==0.12.1 --extra-index-url https://download.pytorch.org/whl/cu113

WORKDIR /
RUN git clone https://github.com/facebookresearch/fairseq.git \
    && cd fairseq \
    && git checkout 336c26a5 \
    && pip install --editable ./ \
    && pip install --no-cache-dir npy-append-array librosa \
    && git clone https://github.com/NVIDIA/apex.git /apex \
    && cd /apex \
    && git checkout 9263bc8 \
    && pip install --no-cache-dir -v --disable-pip-version-check --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./

RUN set -x \  
    && apt-get update \  
    && apt-get install -y sudo \  
    && useradd -m -s /bin/bash zkniu \  
    && echo "zkniu:zkniu" | chpasswd \  
    && adduser zkniu sudo \  
    && mkdir -p /etc/sudoers.d \  
    && echo 'zkniu ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/zkniu \  
    && chmod 0440 /etc/sudoers.d/zkniu \  
    && chown -R zkniu:zkniu /usr/local/bin/ \  
    && chown -R zkniu:zkniu /usr/local/cuda-11.3/ \  
    && chown -R zkniu:zkniu /usr/local/cuda/ \  
    && chown -R zkniu:zkniu /usr/local/cuda/include/ \  
    && chown -R zkniu:zkniu /home/zkniu/   

USER zkniu

ENV SHELL=/bin/bash
WORKDIR /home/zkniu/
CMD [ "/bin/bash" ]
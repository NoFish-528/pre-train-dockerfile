# An Intro to set up your Docker environment and debug using VSCode

> Created by zym22 on 2021.12.20
>
> Based on SJTU-XLANCE Servers & Speech Pre-train Docker as examples. 



## Set up VSCode Remote

1. Download extensions in VSCode. 

<img src="Docker&Debug/image-20211220103747587.png" alt="image-20211220103747587" style="zoom: 50%;" />

2. Set up config of multi-jumps. (For you need a jumping machine to get access the GPUs.) 

```bash
# local at ~/.ssh/config
Host gauss
    HostName 202.xxx.xxx.xxx
    Port 5566
    User zym22
Host date
    HostName 192.xxx.xxx.xxx
    # ProxyJump gauss
    ProxyCommand ssh -A gauss -W %h:%p
    User zym22
```

3. Connect to the Server such as `date`. 

<img src="Docker&Debug/image-20211220104838119.png" alt="image-20211220104838119" style="zoom:50%;" />



## Set up Docker environment

> Before take steps in this section, make sure your terminal is like `zym22@date:~$`.

1. Set up Docker environment in `zym22@date:~$`.

```bash
docker pull zym22/pre-train:latest 
docker run -it --runtime=nvidia --name myDocker \ # choose a name
    --mount src=/mnt/xlancefs/home/zym22/data,target=/data/zym22,type=bind \ # access to your src
    # -v /mnt/xlancefs/home/zym22:/home/zym22 \
    -p 8023:22 \ # choose a port
    --ipc=host    # host shares memory with the container
    zym22/pre-train:latest /bin/bash
docker start myDocker
docker exec -it myDocker /bin/bash
```

2. Set up Docker environment in `root@myDocker:~$`.

```bash
passwd root
apt update
apt install -y openssh-server
vim /etc/ssh/sshd_config
###
# edit here
Port 22                     #Enabling Port 22
PermitRootLogin yes         #Allow root user to log in using ssh
RSAAuthentication yes       #Enabling RSA Authentication 
PubkeyAuthentication yes    #Enable public-private key pairing Authentication
# wq
###
service ssh restart
exit
```

3. Verify that port mapping is correct. 

```less
zym22@date:~$ docker port myDocker 22
0.0.0.0:8023
```

4. Edit local config at `~/.ssh/config`. 

```bash
# local at ~/.ssh/config
Host gauss
    HostName 202.xxx.xxx.xxx
    Port 5566
    User zym22
Host date
    HostName 192.xxx.xxx.xxx
    # ProxyJump gauss
    ProxyCommand ssh -A gauss -W %h:%p
    User zym22
Host myDocker
    HostName 192.xxx.xxx.xxx # same as date
    Port 8023
    # ProxyJump gauss
    ProxyCommand ssh -A gauss -W %h:%p
    User root
```

5. Connect to the Docker. 

<img src="Docker&Debug/image-20211220112552697.png" alt="image-20211220112552697" style="zoom:50%;" />



## Debug using VSCode

> Before take steps in this section, make sure your terminal is like `root@myDocker:~$`

1. Create a `launch.json` in `.vscode` folder.

```json
# launch.json
{
    "version": "0.2.0",
    "configurations": [
        {// python -m debugpy --listen 5678 --wait-for-client main.py args
            "name": "Python: attach local",
            "type": "python",
            "request": "attach",
            "console": "integratedTerminal",
            "env": {
                "CUDA_VISIBLE_DEVICES": "1" //edit
            },
            "connect": {
                "host": "localhost",
                "port": 5678 //edit
            }
        }

    ]
}
```

2. Run a python file and block.

```bash
pip install debugpy
python -m debugpy --listen 5678 --wait-for-client main.py args
# An example to debug wav2vec2.0 using fairseq.
python -m debugpy --listen 5678 --wait-for-client ../miniconda3/envs/espnet/bin/fairseq-hydra-train task.data=examples/wav2vec/manifest --config-dir examples/wav2vec/config/pretraining --config-name wav2vec2_test_librispeech
```

3. Start debugging mode.

<img src="Docker&Debug/image-20211220114021177.png" alt="image-20211220114021177" style="zoom:50%;" />

<img src="Docker&Debug/image-20211220114209393.png" alt="image-20211220114209393" style="zoom:50%;" />



## About the Speech Pre-train Docker (Latest tag: 20220218)

> A docker for Speech, compatible with Kaldi, Espnet and Fairseq.

Basic environment：

- ubuntu=16.04 
- cudnn=7 cuda=10.2 
- python=3.8 torch=1.10.0 torchaudio=0.10.0 
- condaenv=espnet 

Basic Directory structure:

```bash
- root
	- miniconda3
	- .bashrc
	- .profile
- data
	- zym22
	- xc095
- espnet
- fairseq -> espnet/tools/fairseq
- kaldi -> espnet/tools/kaldi
```


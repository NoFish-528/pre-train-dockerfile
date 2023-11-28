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
docker pull zkniu/fairseq:torch1.12-cu113-fairseq
docker run -it --gpus all --name fairseq \
    -v /home/v-zhikangniu:/root \
    zkniu/fairseq:torch1.12-cu113-fairseq0.12 /bin/bash
docker start fairseq
docker exec -it fairseq /bin/bash
```

2. Install docker extension in VSCODE


5. Connect to the Docker. 

<img src="Docker&Debug/image-20211220112552697.png" alt="image-20211220112552697" style="zoom:50%;" />



## Debug using VSCode

> Before take steps in this section, make sure your terminal is like `root@myDocker:~$`

1. Create a `launch.json` in `.vscode` folder.

```json
# launch.json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Attach",
            "type": "python",
            "request": "attach",
            "connect": {
              "host": "localhost",
              "port": 5678
            },
            "justMyCode": true,
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


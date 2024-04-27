### Introduction

The lab uses torchRL and open AI gym. The training can be performed on CPU/GPU, however, it is best that the experiments is running on GPU. 
In order to streamline the setup process and focus on the environment construction and training we have AWS ```g5.xlarge``` instances for use and 
provide docker images to manage all the dependencies. (If you are unable to connect to the AWS machine, you can try running the training locally following [this instructions](local_inst.md). However, it is suggested that the local machine has CUDA support, otherwise the training could be very slow.)

### Access the AWS g5.xlarge instance

To access the ```g5.xlarge``` instance, please use the following google form

[https://forms.gle/xVaQWDtDpwbnEBUY8](https://forms.gle/xVaQWDtDpwbnEBUY8)

Once you submit your email, the login method and credentials will be emailed.

### Launch the docker image

Once you log on the AWS instance, please use the following command to pull the docker image 

```
$ docker pull ml2558/autocat-rlmeta
```

Once it is finished, please use the following command to launch the docker image

```
$ docker run -it --gpus all ml2558/autocat-rlmeta /bin/bash 
```

After that you will be prompt to a docker shell.

### Activate the conda environment

inside the docker container, set the conda environment variables and activate the py38 environment

```
# eval "$(/root/miniconda3/bin/conda shell.bash hook)" 
# conda activate py38
```

```
# cd lab/src/rlmeta
# python train_ppo_attack.py table_view=true
```


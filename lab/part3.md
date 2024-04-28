### Introduction

The lab uses rlmeta and open AI gym. The training can be performed on CPU/GPU, however, it is best that the experiments is running on GPU. 
In order to streamline the setup process and focus on the environment construction and training we have AWS ```g5.xlarge``` instances for use and 
provide docker images to manage all the dependencies. (If you are unable to connect to the AWS machine, you can try running the training locally following [this instructions](local_inst.md). However, it is suggested that the local machine has CUDA support, otherwise the training could be very slow.)

### Access the AWS g5.xlarge instance

We provide limited number of AWS instances for experiments.
The credentials are handed out during the tutorial. For those attending ASPLOS '24 in-person, the details to login to AWS instance is in the slip of paper provided to you at the beginning of tutorial. Please do not share or copy this piece of paper in anyway due to security reasons. 

### Launch the docker image

Once you log on the AWS instance, please use the following command to pull the docker image 

```
docker pull ml2558/autocat-rlmeta
```

Once it is finished, please use the following command to launch the docker image

```
docker run -it --gpus all ml2558/autocat-rlmeta /bin/bash 
```

After that you will be prompt to a docker shell.

Before you continue there are two important things to do. First, you must install nano.

```
apt-get install nano -y
```
Second, you must clone the lab repo. 

```
git clone https://github.com/ut-ldma/lab.git
```

### Activate the conda environment

Inside the docker container, set the conda environment variables and activate the py38 environment

```
eval "$(/root/miniconda3/bin/conda shell.bash hook)" 
conda activate py38
```

```
cd lab/src/rlmeta
python train_ppo_attack.py table_view=true
```
Here are the results that you should be expecting to show up on your screen. First the replay buffer will be filled. 

```
...
[20:53:45] Warming up replay buffer: [   1478 / 131072 ]                                         replay_buffer.py:208
[20:53:46] Warming up replay buffer: [   1709 / 131072 ]                                         replay_buffer.py:208
[20:53:47] Warming up replay buffer: [   1937 / 131072 ]                                         replay_buffer.py:208
[20:53:48] Warming up replay buffer: [   2179 / 131072 ]                                         replay_buffer.py:208
[20:53:54] Warming up replay buffer: [   3596 / 131072 ]                                         replay_buffer.py:208
...
```
Then training will begin with results for every epoch. 

```

...




...
```


Be sure to observe the return (episode_return for training (T), episode_return for evaluation (E)) for each epoch. This is the reward for each epoch for training and evaluation, and while it starts off low, it should be getting higher after a couple of epochs, converging close to 1. 


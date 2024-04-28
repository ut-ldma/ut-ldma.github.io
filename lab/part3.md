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
Training... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:01:19
[2024-04-28 10:21:50,319][root][INFO] -

     info                  key          mean          std           min           max    count
---------  -------------------  ------------  -----------  ------------  ------------  -------
T Epoch 1               return   -0.62614770   0.10059808   -0.89800441   -0.25853318     3000
T Epoch 1         policy_ratio    0.97290586   0.01395912    0.92607641    1.01706052     3000
T Epoch 1          policy_loss   -0.08119216   0.01225191   -0.11347346    0.00035499     3000
T Epoch 1           value_loss    0.79066899   0.10023246    0.51054454    1.04701352     3000
T Epoch 1              entropy    1.63982680   0.14539894    1.23214149    1.89808714     3000
T Epoch 1                 loss    0.28134580   0.05658503    0.13921326    0.42976552     3000
T Epoch 1            grad_norm    1.41852424   0.40762858    0.68202126    5.91281080     3000
T Epoch 1  sample_data_time/ms    0.47210614   1.44579243    0.03368100   23.21799700     3000
T Epoch 1  batch_learn_time/ms   25.11491106   3.03774654   18.80394600   50.34879800     3000
T Epoch 1       episode_length    6.35657075   3.41048177    1.00000000   25.00000000     3371
T Epoch 1       episode_return   -0.51070009   0.89089254   -1.24000000    0.99000000     3371
T Epoch 1       episode_time/s    0.09798736   0.05553257    0.00475310    0.41407304     3371
T Epoch 1     steps_per_second   71.18828027  32.87222660   31.76741979  247.36684181     3371
T Epoch 1         correct_rate    0.27143281   0.44469882    0.00000000    1.00000000     3371
T Epoch 1                 time  181.57425414   0.00000000  181.57425414  181.57425414        1



     info                  key          mean          std           min           max    count
---------  -------------------  ------------  -----------  ------------  ------------  -------
T Epoch 1               return   -0.62614770   0.10059808   -0.89800441   -0.25853318     3000
T Epoch 1         policy_ratio    0.97290586   0.01395912    0.92607641    1.01706052     3000
T Epoch 1          policy_loss   -0.08119216   0.01225191   -0.11347346    0.00035499     3000
T Epoch 1           value_loss    0.79066899   0.10023246    0.51054454    1.04701352     3000
T Epoch 1              entropy    1.63982680   0.14539894    1.23214149    1.89808714     3000
T Epoch 1                 loss    0.28134580   0.05658503    0.13921326    0.42976552     3000
T Epoch 1            grad_norm    1.41852424   0.40762858    0.68202126    5.91281080     3000
T Epoch 1  sample_data_time/ms    0.47210614   1.44579243    0.03368100   23.21799700     3000
T Epoch 1  batch_learn_time/ms   25.11491106   3.03774654   18.80394600   50.34879800     3000
T Epoch 1       episode_length    6.35657075   3.41048177    1.00000000   25.00000000     3371
T Epoch 1       episode_return   -0.51070009   0.89089254   -1.24000000    0.99000000     3371
T Epoch 1       episode_time/s    0.09798736   0.05553257    0.00475310    0.41407304     3371
T Epoch 1     steps_per_second   71.18828027  32.87222660   31.76741979  247.36684181     3371
T Epoch 1         correct_rate    0.27143281   0.44469882    0.00000000    1.00000000     3371
T Epoch 1                 time  181.57425414   0.00000000  181.57425414  181.57425414        1

[2024-04-28 10:21:55,329][root][INFO] -

     info               key          mean          std           min           max    count
---------  ----------------  ------------  -----------  ------------  ------------  -------
E Epoch 1    episode_length    4.84000000   0.36660606    4.00000000    5.00000000      100
E Epoch 1    episode_return   -0.09840000   0.99990272   -1.04000000    0.97000000      100
E Epoch 1    episode_time/s    0.03665065   0.00680238    0.02171022    0.05658663      100
E Epoch 1  steps_per_second  135.75962736  22.41160053   88.36009495  194.76951909      100
E Epoch 1      correct_rate    0.47000000   0.49909919    0.00000000    1.00000000      100
E Epoch 1              time  186.58510643   0.00000000  186.58510643  186.58510643        1



     info               key          mean          std           min           max    count
---------  ----------------  ------------  -----------  ------------  ------------  -------
E Epoch 1    episode_length    4.84000000   0.36660606    4.00000000    5.00000000      100
E Epoch 1    episode_return   -0.09840000   0.99990272   -1.04000000    0.97000000      100
E Epoch 1    episode_time/s    0.03665065   0.00680238    0.02171022    0.05658663      100
E Epoch 1  steps_per_second  135.75962736  22.41160053   88.36009495  194.76951909      100
E Epoch 1      correct_rate    0.47000000   0.49909919    0.00000000    1.00000000      100
E Epoch 1              time  186.58510643   0.00000000  186.58510643  186.58510643        1



...
```


Be sure to observe the return (episode_return for training (T), episode_return for evaluation (E)) for each epoch. This is the reward for each epoch for training and evaluation, and while it starts off low, it should be getting higher after a couple of epochs, converging close to 1. 

### NOTE: Please hit CTRL^C when episode_return for training and testing converge close enough to 1. 

Please be sure to hit CTRL^C to interrupt the training process, otherwise it will run indefinetely. Throughout the training process there are logs and checkpoints which we will show you how to access in part4.md. 

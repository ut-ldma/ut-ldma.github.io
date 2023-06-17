# Running the Training on Local Computer/Laptop

### Apple-M series CPU

For Apple m series chip use the following to launch and run 

```
$ docker pull ml2558/autocat-torchrl:apple-m1
$ docker run -it ml2558/autocat-torchrl:apple-m1 /bin/bash
```
Inside the docker containeer, set the conda environment variables and activate py38 environment.

```
# eval "$(/root/miniconda3/bin/conda shell.bash hook)" 
# conda activate py38
```

To install the latest version of TorchRL and tensordict, execute these commands:

```
(py38) $ pip install git+https://github.com/pytorch-labs/tensordict
(py38) $ pip install git+https://github.com/pytorch/rl
```
Checkout ```m1``` branch of ```https://github.com/rl4cas/lab```.

```
(py38) $ git clone https://github.com/rl4cas/lab -b m1
```

Launch the training
```
(py38) $ cd lab/src/torchrl
(py38) $ python train_ppo_attack.py
```


### Linux-based CPU

For X86 machine without CPU, use the following to launch and run 

```
$ docker pull ml2558/autocat-torchrl:new
$ docker run -it ml2558/autocat-torchrl:new /bin/bash
```

Inside the docker containeer, set the conda environment variables and activate py38 environment.

```
# eval "$(/root/miniconda3/bin/conda shell.bash hook)" 
# conda activate py38
```

To install the latest version of TorchRL and tensordict, execute these commands:

```
(py38) $ pip install git+https://github.com/pytorch-labs/tensordict
(py38) $ pip install git+https://github.com/pytorch/rl
```
Checkout ```cpu``` branch of ```https://github.com/rl4cas/lab```.

```
(py38) $ git clone https://github.com/rl4cas/lab -b cpu
```

Launch the training
```
(py38) $ cd lab/src/torchrl
(py38) $ python train_ppo_attack.py
```


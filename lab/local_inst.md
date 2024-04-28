# Running the Training on Local Computer/Laptop

### Apple-M series CPU

For Apple m series chip use the following to launch and run (note the autocat-torchrl:apple-m1 image supports rlmeta)

```
docker pull ml2558/autocat-torchrl:apple-m1
docker run -it ml2558/autocat-torchrl:apple-m1 /bin/bash
```
Inside the docker containeer, set the conda environment variables and activate py39 environment.

```
eval "$(/root/miniconda3/bin/conda shell.bash hook)" 
conda activate py39
```

Launch the training
```
cd AutoCAT/src/rlmeta
python train_ppo_attack.py
```


### x86 CPU with Linux OS without GPU

For X86 machine without GPU, use the following to launch and run 

```
docker pull ml2558/autocat-rlmeta
docker run -it ml2558/autocat-rlmeta /bin/bash
```

Inside the docker containeer, set the conda environment variables and activate py38 environment.

```
eval "$(/root/miniconda3/bin/conda shell.bash hook)" 
conda activate py38
```

Launch the training

```
cd AutoCAT/src/rlmeta
python train_ppo_attack.py
```

### x86 CPU with Nvidia GPU

Please follow the same setting as AWS g5.xlarge.


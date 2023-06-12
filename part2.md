### Training the gym environment using TorchRL

Now that we have shown how an gym environment is constructed, we need to train an RL agent to interact with the environment. We use (TorchRL)[https://pytorch.org/rl/] for that purpose. The related torchRL script are located in ```src/torchrl/```

### Train the RL agent using TorchRL

```src/torchrl/train_ppo_attack.py``` is the script for training. The default configuration for training is specified in ```src/torchrl/config/ppo_attack.yml```. The environment configuration is specified in ```src/torchrl/config/env_config/``` and the model configuration of the policy net is specified in 
```src/torchrl/config/model_config/```.

Please use the following to launch the training

```
(py38) $ python ${REPO_ROOT}/src/torchrl/train_ppo_attack.py
```

select (2) when prompted to plot all the training progress in wandb. 

On your browser, open a new tab and open ```https://wandb.ai``` and navigate to the ```rl4cache``` project, where you can see the current training and testing return and other statistics.

### Sampling 






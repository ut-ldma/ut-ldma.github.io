### Notice
It is highly suggested to look over Parts 1, 2, and 3 before Part 4's Coding Challenge. 

### Flush + Reload Cache Timing Attacks

The gym environment described in Part 1 does not include the flush instruction in the environment, as a result, it will
not be able to find flush+reload attacks commonly used. Even though the cache simulator has the interface for flush instruction,
the gym environment did not expose the flush to the ```action_space()```, in this excercise, we aim at extending the gym environment to incorporate the flush action into the gym environment. We directly modify the [```src/cache_guessing_game_env_impl.py```](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py).

We use a flag [```flush_inst```](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py#L100) to indicate whether we want to include flush instruction for the attacker, by default it is not enabled.

### Quick Refresher on Flush + Reload Cache Timing Attacks

## Flush+Reload Attack Summary

The Flush+Reload technique is a side-channel attack that exploits shared cache memory to infer activities of other processes on the same system. This technique involves two primary steps within the cache simulator:

### Steps of the Flush+Reload Attack

1. **Flush**:
   - **Action**: The attacker forcibly removes (flushes) a target line of memory from the cache.
   - **Method**: This is achieved using the `clflush` method in the cache simulator, where a specific address is cleared out of the cache memory.

2. **Reload**:
   - **Action**: The attacker then accesses the same memory address.
   - **Observation**:
     - **Fast Access**: If the data is accessed quickly, it suggests that the data was reloaded into the cache by the victim's process, indicating that the victim accessed the same data after it was flushed. This results in a cache hit.
     - **Slow Access**: If the data access is slow, it indicates a cache miss, suggesting the data was not used by the victim after the flush.



<div align="center">
  <img src="fig/flushandreload_almostperfect.png" width="1500" alt="Flush and Reload Illustration">
</div>


### Modify the ```action_space``` to Incorporate Flush Action

First, the original [```action_space```](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py#L172) does not include the encodings for flush action, we need to extend the size of the action_space to include necessary encoding for flushing different attacker addresses, this is defined and done for you [here](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py#L177).

### Modify the Action Parser ```parse_action()``` 

Second, after we increase the action space size, we still need to decode/parse the action so that we know which encoding corresponds to flushing which address. This is defined and done for you [here](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py#L441). Once [```is_flush```](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py#L446) is set 1, it means the action correspond to a flush instruction which flushes the [address](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py#L445).

### Perform the Flush Instruction in ```step()```

Finally, even if we know what the action means, we still need to actually invoke proper [cache simulator's API](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py) in the [```step()```](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L204) of the cache environment. This is an coding exercise, please fill in the proper API [here](https://github.com/ut-ldma/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L295).

### Train the Modified Env to Explore Flush+Reload Attack

Once the flush instruction is included, please use the following command to launch the training.

Go to rlmeta directory.
```
cd /lab/src/rlmeta
```

Please use the following to launch the training (```hpca_ae_exp_4_3``` is a configuration that require flush instruction.)

```
python /lab/src/rlmeta/train_ppo_attack.py env_config=hpca_ae_exp_4_3
```

Once the return is close to 1, press CTRL^C to interrupt the process and please use the following to sample the attack.

```
nano /lab/src/rlmeta/config/sample_attack.yaml
Add checkpoint by going to /lab/src/rlmeta/outputs/<day>/<UTC-timestamp>/<most-recent-agent>.pth, and getting path and adding path to checkpoint
```
### Note: One important thing to note is that if your training does not get a good reward, we have a checkpoint for you to use! The path for that checkpoint is ```src/rlmeta/outputs/ppo_agent-99.pth```. On that path is also ``` src/rlmeta/outputs/train_ppo_attack.log``` if you want to observe an average training for this agent. 

Then run, 

```
python /lab/src/rlmeta/sample_attack.py
```
Which it will generate the attack sequence.

### Going Further

If you want to explore more in this area, feel free to use ```hpca_ae_exp_4_1``` for Prime Probe Attack Configuration, referring to [```Part 2```](https://github.com/ut-ldma/ut-ldma.github.io/blob/main/lab/part2.md) for specific instructions. and continue with  ```hpca_ae_exp_4_3``` for Flush Reload Attack Configuration for better results! 


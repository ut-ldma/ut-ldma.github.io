### Flush + Reload Cache Timing Attacks

The gym environment described in Part 1 does not include the flush instruction in the environment, as a result, it will
not be able to find flush+reload attacks commonly used. Even though the cache simulator has the interface for flush instruction,
the gym environment did not expose the flush to the ```action_space()```, in this excercise, we aim at extending the gym environment to
incorporate 

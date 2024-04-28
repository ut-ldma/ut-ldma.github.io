## Part 1: The Gym Enviornment 

We explain how the gym environment is constructed for a reinforcement learning approach to cache timing attacks. 

### Gym environment 

```gym``` is a standard interface developed by Open AI, it is defined as a python class with three methods ```step()```, ```__init__()```, and ```reset()```. It has become a standard for RL research and many mainstream RL frameworks support gym environment. For more information please refer to [https://www.gymlibrary.dev](https://www.gymlibrary.dev).

Below is a brief summary of the gym API:

* ```__init()__```
    - ```observation_space```: the observation space is defined here, the observation space is ...
  
      ###Cache Hit/Miss Information: Indicating whether each action resulted in a cache hit or miss.
      ###Latency Measurements: The time taken for each cache operation, which can provide insights into whether data was fetched from the cache or main memory.
      ###State of Cache Lines: Information about which data or tags are currently stored in specific cache lines, potentially represented in a structured format like a vector or matrix.
     
    - ```action_space```: the action space is defined here, the action space is ...

      ###Read: Accessing a specific cache line to read data.
      ###Write: Writing data to a specific cache line.
      ###Flush (clflush): Flushing a specific cache line, which is commonly used in cache attack scenarios to manipulate or infer the state of the cache.
      ###Guess: An action where the agent attempts to guess secret data based on observed cache behavior (more relevant in an adversarial scenario like a cache timing attack). 

* ```step(action) --> state, reward, done, info```
    - ```action```: How to perform the action is defined here
    - ```state```: What the agent observed is defined here
    - ```reward```: Reward is defined here

* ```reset()```


### Cache Simulator Interface

The main focus of the environment used is a cache simulator, which forms the backbone of the cache timing attacks. The cache simulator (in [```src/cache.py```](https://github.com/rl4cas/lab/blob/main/src/cache.py) ) implements the basic functions that are representative of a real-world cache. These include

```init()```, ```read()```, ```write()```, ```clflush()``` and ```parse_address()```, these functions are defined accordingly.

[```__init()__```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L11): instantiate and initialize the cache simulator class. 

## `__init__` Method Summary

This `__init__` method initializes an instance of a cache simulator class, setting up the cache with the necessary configurations and structures for operation. Here are the detailed components and functionalities:

### Configuration Parameters
- **General Settings**: Configures cache parameters such as name, word size, block size, number of blocks, associativity, hit time, and write time.
- **Write Policy**: Determines if the cache uses a write-back policy.
- **Logging**: Sets up logging capabilities and verbosity for detailed output.

### Replacement Policy Setup
- **Default Policy**: Defaults to an LRU (Least Recently Used) policy.
- **Policy Configuration**: Logs messages about the replacement policy setup based on the cache's name and the provided policy argument.

### Cache Structure Initialization
- **Number of Sets**: Calculates the total number of sets in the cache based on the number of blocks and their associativity.
- **Data Structures**: Initializes the `data` dictionary and `set` dictionary to hold the cache data and metadata.

### Memory Address Calculation
- **Address Components**: Calculates the sizes for the block offset and cache index, essential for decoding binary memory addresses into cache-friendly components (block offset, index, tag).

### Cache Data Initialization
- **Data Setup**: If a next-level cache or memory layer is specified, initializes each set in the cache with empty blocks and configures the replacement policy for each set.

### Logging and Debugging
- **Verbose Output**: Contains provisions for verbose output through the logger, enabling detailed runtime information about cache operations and configuration.

This method is critical as it not only configures the cache parameters but also establishes the foundational data structures and policies necessary for its operation.


[```read()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L96): perform a reading operation through the cache interface

## `read` Method Summary

The `read` method simulates reading from a cache line within a cache hierarchy, handling both cache hits and misses, and managing cache evictions when necessary.

### Overview
- **Parameters**:
  - `address`: The memory address to read from.
  - `current_step`: The current step or time in the simulation, used for tracking when actions occur, essential for policies like LRU that depend on the order of operations.

- **Return Values**:
  - `r`: A `Response` object indicating the result of the read operation, including whether it was a hit and the time taken.
  - `evict_addr`: The address of any evicted block, if applicable.

### Process Details
1. **Check Level**:
   - Directly returns a hit if the cache level is main memory, as main memory is assumed to always hit.

2. **Address Parsing**:
   - Decomposes the input `address` into `block_offset`, `index`, and `tag` components for internal cache indexing.

3. **Cache Lookup**:
   - Checks if the tag is present in the cache set. If found, it's a hit:
     - Updates the access time for the cache block.
     - Adjusts the position or status of the block within the replacement policy.

4. **Handling Misses**:
   - If the tag is not found (a miss), it performs a read from the next lower memory level.
   - Adds the read time of the next level and current level's write time to the response.

5. **Cache Update on Miss**:
   - Checks for free space in the set to add the new block.
   - If no space is available, identifies a victim block using the replacement policy, potentially evicts it, and then adds the new block.
   - Manages dirty blocks by writing back to the next level if using a write-back policy.

6. **Eviction Details**:
   - Constructs the eviction address by combining the victim tag and block offset.
   - Updates the replacement policy to reflect the eviction and new block addition.

### Usage
This method is crucial for simulating realistic cache operations, including reads that might result in cascading reads from lower memory levels depending on the cache hierarchy and policies in place.



[```write()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L172): perform a write operation through the cache interface

### Overview
The write method is not needed in our lab, and thus not implemented.

[```clflush()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L69): flush a specific address from the cache hierachy

## `clflush` Method Summary

The `clflush` method simulates the flushing of a cache line from the cache hierarchy. This operation is common in scenarios where explicit control over data in the cache is needed, often for security or consistency reasons.

### Overview
- **Parameters**:
  - `address`: The memory address of the cache line to flush.
  - `current_step`: The current step or time in the simulation, used for tracking when the flush occurs.

- **Return Values**:
  - `r`: A `Response` object indicating that the flush operation was successful, along with the time taken to perform the flush.

### Process Details
1. **Address Parsing**:
   - Decomposes the input `address` into `block_offset`, `index`, and `tag` components for locating the specific cache line within the cache.

2. **Cache Line Identification**:
   - Identifies if the tag associated with the address is present in the cache. If so, it proceeds to flush the cache line.

3. **Cache Line Flushing**:
   - If the cache line is found (tag exists in the cache set), it removes the tag and resets the corresponding block:
     - Sets the tag to `INVALID_TAG`.
     - Resets the block data, effectively clearing it from the cache.

4. **Policy Update**:
   - Updates the replacement policy to mark the cache line as invalidated, ensuring that it no longer influences cache behavior.

5. **Recursive Flushing**:
   - If the cache is part of a hierarchy with multiple levels and the next level is not main memory, it recursively flushes the same address from the next level of the cache. This ensures that all instances of the data across the cache hierarchy are flushed.

### Usage
This method is essential for maintaining cache coherence and security in simulations that involve complex cache hierarchies. It allows the simulation to mimic the behavior of actual hardware operations where certain data must be explicitly cleared from the cache.



[```_parse_address()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L175): Parse the address into block, tag, offset

## `_parse_address` Method Summary

The `_parse_address` method is a utility function used to decompose a memory address into its constituent parts based on the cache's configuration. This decomposition is essential for locating the appropriate cache line and set within the cache.

### Overview
- **Parameter**:
  - `address`: The memory address in hexadecimal format that needs to be parsed.

- **Return Values**:
  - `block_offset`: The offset within a block where the data is stored.
  - `index`: The index of the set within the cache where the data might reside.
  - `tag`: The tag used to verify whether the correct data is in the specified cache line.

### Process Details
1. **Address Conversion**:
   - Converts the hexadecimal address into a binary string to facilitate bit-level manipulation.

2. **Component Extraction**:
   - **Block Offset**: Extracts the block offset from the least significant bits of the binary address, determined by `block_offset_size`.
   - **Index**: Extracts the set index from the next set of bits above the block offset, determined by `index_size`.
   - **Tag**: The remaining bits form the tag, which is used to uniquely identify a block of data within a set.

3. **Edge Cases**:
   - Handles special cases where the block offset or index sizes are zero, which could occur in configurations with only one block or one set.

### Usage
This method is critical for the cache's operation as it allows the simulator to determine where in the cache a particular piece of data should be read from or written to. Properly parsing addresses ensures that the cache simulator can accurately mimic the behavior of a real cache memory system.



### Learn to play the guessing game

The cache guessing game is played as the following. First the attacker makes a sequence of memory access  (read/write/flush) and observe the access latency, after acquiring enough information (decided by the player/agent itself), it tries to guess the secret. Thus, the information the attacker gets from the game is the access latency, while the actions include access some cache addresses, guess the secret, or wait for the victim to access.

Here is a brief summary of the cache guessing game:

* Agent: Attacker

* Environment: Cache
    - architecture simulator 
    - cache in the processor

* Actions
    - attacker makes an access (read)
    - attacker waits for victim access
    - attacker guesses the secret

* Observation
    - latency of attacker accesses

* Reward
    - guess correct: positive reward
    - guess wrong: negative reward
    - each step: small negative reward

* Maximizing long-term reward
    - more correct guesses
    - fewer wrong guesses
    - fewer number of steps


### Cache Guessing Game as Gym Environment

The cache guessing game, described above is defined as the follows using the gym API, the code is located in [here](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py).

* Gym Environment
    - Define actions
        - [```action_space```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L172): defined in [```__init()__```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L65)
        - ```action```: defined in [```step()```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L204)
    - Define state
        - [```observation_space```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L185): defined in [```__init()__```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L65)
        - [```observation/state```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L327): defined in [```step()```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L204)
    - Define rewards
        - ```reward``` for different scenario defined in [```step()```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L204)

We discuss the several parts in detail.

#### Define Actions

As described, there are three types of actions, listed below (assume there are N different addresses and there are M possible secrets).
* Action
    - Read an address: there are N actions
    - Wait and allow victim access the secret: one action
    - Guess the secret: there are M actions

There are ```N (different possible addresses to guess) + M (different possible secrets to guess) + 1 (wait and allow victim)``` actions 

Use one hot encoding, represented in [here](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L172)

```
self.action_space = spaces.Discrete(len(self.attacker_address_space) + 1+len(self.victim_address_space))
```

Given an action encoded in [```spaces.Discrete```](https://www.gymlibrary.dev/api/spaces/#discrete), which is a standard gym space definition.

* how to parse the action is defined [here](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#LL426)

```
def parse_action(self, action):
...
return [ address, is_guess, is_victim, is_flush, victim_addr ] 
```

* how to interact with the cache simulator l1 to update the simulator state:
    - Guess action : no update to l1, but check the [correctness of guess](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L260)
    - Read action : [```l1.read(adress)```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L283)
    - Wait and allow victim access action : [```l1.read(victim_address)```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L249)


#### Define State

* The state/observation is defined as an instance of [```spaces.Box```](https://www.gymlibrary.dev/api/spaces/#box), which is instantiated [here](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L185) in [```__init()__```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L65)

* The state include is represented as a X-by-4 matrix, where X is the length of the history window defined [window_size](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L147) 
    - Each row is a 4-element tuple
        - ```[latency, victim_accessed, action, steps_count]```
        - ```latency``` : 0 hit , 1 miss , 2 NA
        - ```victim_accessed``` : 0 not accessed , 1 accessed
        - ```action``` : the action used 
        - ```steps_count``` : the current steps

* We keep a window_size row as history this history provide more information for the RL agent to decide what actions should be taken.



#### Define Reward

* After each step(), the reward is assigned based on state and action
    - guess action, guess is correct: [a positive reward is assigned](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L273)
    - guess action, guess is wrong: [a negative reward is assigned](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L280)
    - Not a guess action
        - Length within the limit: [a small negative reward](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L292)
        - Length exceeding the limit: [a large negative reward](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L240)

#### Putting together

Putting together the defined actions, states and reward, we summarize the skeleton of the gym environment that implements the cache guessing game here.

* Gym API:
    - ```init()```
        - Instantiate the cache simulator l1
        - ```observation_space```: spaces.
        - ```action_space```: space.Discrete() 

    - ```step(action) --> state, reward, done, info```
        - Parse the action into ```[ address, is_guess, is_victim, is_flush, victim_addr ]```
        - Depending on the action, invoke ```l1.read()``` correspondingly

    - ```reset()```
        - Reset the cache simulator 
 
 The entire code of the gym environment for cache guessing game is in [```src/cache_guessing_game_env_impl.py```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py).
 












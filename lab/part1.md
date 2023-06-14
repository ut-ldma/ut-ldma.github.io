## Part 1: The Gym Enviornment 

We explain how the gym environment is constructed for reinforcement learning.

### Gym environment 

```gym``` is an standard interface developed by open AI, it is defined as a python class with three methods ```step()```, ```__init__()```, and ```reset()```. It has become a standard for RL research and many mainstream RL frameworks support gym environment. For more information please refer to [https://www.gymlibrary.dev](https://www.gymlibrary.dev).

Below is a brief summary of the gym API:

* ```__init()__```
    - ```observation_space```:
    - ```action_space```: the action space is defined here

* ```step(action) --> state, reward, done, info```
    - ```action```: How to perform the action is defined here
    - ```state```: What the agent is observed is defined here
    - ```reward```: Reward is defined here

* ```reset()```


### Cache Simulator Interface

The main focus of the environment used is a cache simulator, which forms the backbone of the cache timing attaks. The cache simulator (in [```src/cache.py```](https://github.com/rl4cas/lab/blob/main/src/cache.py) ) implements the basic functions that are representative of a real-world cache. These include

```init()```, ```read()```, ```write()```, ```clflush()``` and ```parse_address()```, these functions are defined accordingly.

[```__init()__```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L11): instantiate and initialize the cache simulator class

[```read()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L96): perform a reading operation through the cache interface

[```write()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L172): perform a write operation through the cache interface

[```clflush()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L69): flush a specific address from the cache hierachy

[```_parse_address()```](https://github.com/rl4cas/lab/blob/main/src/cache.py?plain=1#L175): Parse the address into block, tag, offset

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

The cache guessing game, described above is defined as the follows using the gym API

* Gym Environment
    - Define actions
        - [```action_space```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L172): defined in [```__init()__```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L65)
        - ```action```: defined in [```step()```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L204)
    - Define state
        - [```observation_space```](https://github.com/rl4cas/lab/blob/main/src/cache_guessing_game_env_impl.py?plain=1#L185): defined in ```__init()__```
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

There are ```N + M + 1``` actions 

Use one hot encoding, represented in 

```
self.action_space = spaces.Discrete(len(self.attacker_address_space) + 1+len(self.victim_address_space))
```

Given an action encoded in ```spaces.Discrete```
* how to parse the action 

```
def parse_action(self, action):
...
return [ address, is_guess, is_victim, is_flush, victim_addr ] 
```

* how to interact with the cache simulator l1 to update the simulator state:
    - Guess action : no update to l1
    - Read action : ```l1.read(adress)```
    - Wait and allow victim access action : ```l1.read(victim_address)```


#### Define State

* The state include is represented as a X times Y matrix
    - Each row is a 4-element tuple
        - ```[latency, victim_accessed, action, steps_count]```
        - ```latency``` : 0 hit , 1 miss , 2 NA
        - ```victim_accessed``` : 0 not accessed , 1 accessed
        - ```action``` : the action used 
        - ```steps_count``` : the current steps

* We keep Y row of this as history this history provide more information for the RL agent to decide what actions should be taken

* This state_space is defined in ```init()```

#### Define Reward

* After each step(), the reward is assigned based on state and action
    - guess action, guess is correct: a positive reward is assigned
    - guess action, guess is wrong: a negative reward is assigned
    - Not a guess action
        - Length within the limit: a small negative reward
        - Length exceeding the limit: a large negative reward

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
 
 The entire code of the gym environment for cache guessing game is in ```src/cache_guessing_game_env_impl.py```.
 












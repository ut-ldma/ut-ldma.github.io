## Part 1: The Gym Enviornment

We explain how the gym environment is constructed for reinforcement learning.

### Gym environment 

```gym``` is an standard interface developed by open AI, it is defined as a python class with three methods ```step()```, ```__init__()```, and ```reset()```. It has become a standard for RL research and many mainstream RL frameworks support gym environment. For more information please refer to (https://www.gymlibrary.dev)[https://www.gymlibrary.dev].

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

The main focus of the environment used is a cache simulator, which forms the backbone of the cache timing attaks. The cache simulator implements the basic functions that are representative of a real-world cache. These include

```init()```, ```read()```, ```write()```, ```flush()``` and ```parse_address()```, these functions are defined accordingly.

```init()```: instantiate and initialize the cache simulator class

```read()```: perform a reading operation through the cache interface

```write()```: perform a write operation through the cache interface

```flush()```: flush a specific address from the cache hierachy

```parse_address()```: Parse the address into block, tag, offset

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
        - ```action_space```: defined in ```init()```
        - ```action```: defined in ```step()```
    - Define state
        - ```Observation_space```: defined in ```init()```
        - ```Observation/state```: defined in ```step()```
    - Define rewards
        - ```reward``` for different scenario defined in ```step()```

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
    - Guess action  no update to l1
    - Read action  l1.read(adress)
    - Wait and allow victim access action  l1.read(victim_address)










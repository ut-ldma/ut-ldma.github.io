## Part 1: The Gym Enviornment

We explain how the gym environment is constructed for reinforcement learning.

### Gym environment 

```gym``` is an standard interface developed by open AI, it is defined as a python class with three methods ```step()```, ```__init__()```, and ```reset()```. It has become a standard for RL research and many mainstream RL frameworks support gym environment. For more information please refer to (https://www.gymlibrary.dev)[https://www.gymlibrary.dev].

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






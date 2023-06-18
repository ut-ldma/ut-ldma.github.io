# RL4CAS Tutorial Lab

This is the index page of the lab session for the [RL4CAS tutorial](https://rl4cas.github.io), focusing on bring in the reinforcement learning to computer architecture and system research. 
The repo of the lab is listed [here](https://github.com/rl4cas/lab).

We are using AWS ```g5.xlarge``` instance with with Deep Learning AMI GPU PyTorch 2.0.0 (Ubuntu 20.04) 20230401 image (ami-0a4caa099fc23090f) for running the experiments. You can create an instance on AWS. Alternatively, a local Linux machine with GPU should work as well, if you are using Apple M-series chips or Linux machine without GPU, please follow the set up [here](https://github.com/rl4cas/rl4cas.github.io/blob/main/lab/local_inst.md).

The lab is targeting using reinforcement learning to find cache-timing attacks in microprocessors. Instead of relying on human expert, we formulate it as a RL game. For more background information, please see [AutoCAT](https://drive.google.com/file/d/1FinwdpKCS9oY-3PX38oZ2j1gCjqglX2d/view?usp=share_link).

The lab is divided into four parts listed below. The first two parts we describe how to write an RL environment and how to launch the training of RL. In part three we setup the training code to actually launch a training. And In part four we have a small coding exercise to include new action in the environment. Please click on the link for detailed description of each part.

[Part 1: RL environment for Cache Attacks](lab/part1.md)

[Part 2: Training the RL agent](lab/part2.md)

[Part 3: Lab Setup](lab/part3.md)

[Part 4: Lab Exercise](lab/part4.md)

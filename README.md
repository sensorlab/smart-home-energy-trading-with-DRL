# SMART HOME ENERGY TRADING WITH DRL
This python library storest code for a conference paper about smart home energy management using Deep Reinforcement Learning. It is possible for the user to train their own DRL agent, as well as load pre-trained models and test the agent's performance.

## Installation
To begin using the code, just clone the repository:
```git clone https://github.com/sensorlab/smart-home-energy-trading-with-DRL.git```

## Dependencies
The project requires the following libraries to be installed:
```
pip install tqdm
pip install torch torchvision
pip install matplotlib
pip install numpy
pip install pandas
```

## Usage
To use the functionalities of this repository, we must first import the module into our own ```.py``` or ```.ipynb``` file:
```
import HEMS
```
### Training an agent
The below code trains a new RL agent:
```
manager = HEMS.HEMS()
manager.train()
```
The ```HEMS``` class has a few parameters:
- ```load```(default = False): tells the object to initialize from a pre-trained network
- ```path``` (default = None): path to directory from where to load if ```load = True```
- ```battery``` (default = 20): maximum battery capacity in kWh
- ```max_en``` (default = 1.5): maximum energy in kWh that can be charged or discharged from the battery in a single step (in our case in a 15 min window)
- ```eff``` (default = 0.9): input / output battery efficiency
- ```price_coefs``` (default = [2,1]): price coefficients. They specifiy by how much the market price from the dataset should be multiplied to get buying and selling price of energy. This is an artificial way to introduce Distribution System Operator charges.
- ```n_days``` (default = 2): number of past days that will be stored in the environment state
- ```data_path``` (default = 'data/rtp.csv'): specifies the dataset with which pricing model to use
#### Optional parameters for ```manager.train()``` function:
- ```a``` (default = 3): weight parameter for reward function
- ```b``` (default = 3): weight parameter for reward function
- ```n_episodes``` (default = 200): number of training episodes
- ```epsilon_reduce``` (default = 0.98): factor by which the exploration rate (epsilon) is reduced after each episode
- ```n_days``` (default = 2): number of past days that will be stored in the environment state
- ```n_steps``` (dafault = 7 * 24 * 4): number of steps to take each episode (the default is equal to one week)

### Saving
The following code saves the manager to a directory called ```'my_net'```:
```
manager.save('saved_nets/my_net')
```

### Loading from a saved directory
To load a pre-trained model, we must create a new manager with the ```load``` parameter set to ```True```:
```
manager = HEMS.HEMS(load=True, path='saved_nets/my_net')
```

### Testing
```
manager.test()
```
Due to the unstable nature of the DQN algorithm, meaningful trainig results can sometimes only be achieved by significantly increasing the number of episodes or training the agent over and over (>10 times) until results are satisfactory. This is very computationally intensive and is also the reason why pre-trained managers are important for demonstrating the performance of the algorithm.

## What happens under the hood
This is a Deep Reinforcement Learning library for Smart Home Energy Management. In RL, we usually need to define a RL **ENVIRONMENT** on which a RL **AGENT** is trained. That is why the ```HEMS``` class initialises an ```Env``` class from ```env.py``` (the environment) and a ```DQN``` class from ```dqn.py``` (the agent - in this case the agent is defined as an algorithm called DQN). If ```load == False``` the environment and agent are initilaised inside the ```train()``` function. If ```load == True``` the environment and agent are loaded from the saved folder.

## Using ```env.py``` independently
```env.py``` defines the RL environment for this particular problem. It follows the prinicple of an environment definition in the RL library 'gymnasium'.
### Env
To initialise the environment, we call the class ```Env```. It has the following paramenters:
- ```df``` pandas datadrame from which the environment will be sourced
- ```full_battery_capacity``` (default = 20) 
- ```max_energy``` (default = 1.5)
- ```eff``` (default = 0.9)
- ```price_coefs``` (default = [2,1])
- ```n_days``` (default = 2) number of days from which the environment will store the data
- ```n_steps``` (default = 1000) length of episode
- ```test``` (default = False) if set to ```False```, it will initialise the environment at a random index in ```df```, considering ```n_steps``` as the length of an episode. If set to ```True```, it will initialise the environment at index ```low``` and expect to end the episode at ```high```
- ```low``` (default = 0) index in ```df``` at which the episode will begin if ```test == True```
- ```high``` (default = 30000) index in ```df``` at which the episode will end if ```test == True```

### Functions
- ```reset(seed)``` resets the environment with seeded randomness if ```test == False```, otherwise it begins at ```low```
- ```next_observation()``` returns the most recent step
- ```next_observation_normalized()``` returns normalised values of the most recent step (used for feeding into neural net)
- ```step(action)``` takes a step with action ```action``` and returns the next step, reward from the transition and whether the episode is to be terminated. Additionally it returns data about how much energy was exchanged between entities in the system
  

## FOLDER STRUCTURE
```
project
│   README.md
|   dqn.py
|   env.py
|   HEMS.py
│
└───data
│   │   rtp.csv
│   │   tou.csv
│   |   tou2.csv
│
└───saved_nets
    │...  
```

## Citation
If you are using our datasets or scripts in your research, citation of the following paper would be greatly appreciated.

[Matic Pokorn, Mihael Mohorčič, Andrej Čampa, and Jernej Hribar (2023). Smart Home Energy Cost Minimisation Using Energy Trading with Deep Reinforcement Learning](https://dl.acm.org/doi/10.1145/3600100.3625684)

```
@inproceedings{10.1145/3600100.3625684,
author = {Pokorn, Matic and Mohor\v{c}i\v{c}, Mihael and \v{C}ampa, Andrej and Hribar, Jernej},
title = {Smart Home Energy Cost Minimisation Using Energy Trading with Deep Reinforcement Learning},
year = {2023},
isbn = {9798400702303},
publisher = {Association for Computing Machinery},
address = {New York, NY, USA},
url = {https://doi.org/10.1145/3600100.3625684},
doi = {10.1145/3600100.3625684},
abstract = {Dynamic pricing for electrical energy provision is recently being adopted in several countries. While it is primarily intended to help Distribution System Operators (DSOs) improve load balancing, it also provides an opportunity to smart homes equipped with power generation units such as photovoltaics (PV) and energy storage systems to take advantage of pricing changes and thus reduce overall costs. However, the latter case remains largely unexplored in the literature, as most research focuses on community-based optimisation or managing energy consumption by controlling smart home appliances. To address this gap, we propose a Deep Reinforcement Learning (DRL)-based approach to single house energy trading that can reduce energy costs in a smart home. We demonstrate in a simulated environment with two energy pricing schemes that our solution can minimise the total cost of energy provision. Moreover, we show that the learned energy trading approach leads to up to 47.66\% lower costs for end users compared to conventional rule-based approaches.},
booktitle = {Proceedings of the 10th ACM International Conference on Systems for Energy-Efficient Buildings, Cities, and Transportation},
pages = {361–365},
numpages = {5},
keywords = {Deep Reinforcement Learning, Energy Trading, HEMS},
location = {Istanbul, Turkey},
series = {BuildSys '23}
}
```

## Links
SensorLab: https://sensorlab.ijs.si/

## Licensing
Copyright 2023 Matic Pokorn
The code in this project is licensed under MIT license.

## Acknowledgement
The research leading to these results was supported in part by the HORIZON-MSCA-IF project TimeSmart No. 101063721, H2020 project BD4OPEM (grant
agreement ID: 872525), and by the Slovenian Research Agency under the grant P2-0016.

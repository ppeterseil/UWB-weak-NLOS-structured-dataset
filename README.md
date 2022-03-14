# UWB weak-NLOS strucutred dataset
This repo provides a structured dataset of UWB measurements in static weak NLOS scenarios acquired with custom Qorvo DW1000 based UWB-transceivers and contains over 1.2M samples.

## Acquisition
The setup consists of a tag, three anchors, a base node and a host PC connected to the base node via USB. The tag is centred on a electrically actuated rotary table to get different relative angles between sending and receiving antenna for each setup. Care is taken to align the rotation axis with the symmetry axis of the antenna. A Python script running on the host-PC sends commands via TCP-IP socket connection to a Raspberry-Pi single-board computer generating the actuation commands for the rotary table. The connection between PC and Raspberry-Pi is established using a mobile Wi-Fi router. Rotary table and Raspberry-Pi are mounted on a wooden board to reduce bothersome cable management to a single power-line. Additionally, a smartphone camera is used to film each data capturing setup for documentation purposes. The camera is triggered over Wi-Fi using adb-commands. The first frame of each clip can be found in the `cam` subfolders.

![acquisition of data](measurement-setup.png)

## Datasets
In total, 1.2 million data points were collected and structured in 36 data sets. The data sets differ in weak NLOS environment  (15 in corridor, 12 in lab, 5 outdoors, 4 in anechoic chamber), in ground truth distance  (1, 3, 5 and 8m), and in obstacle on the LOS path (none, non-conductive wooden wall, conductive flipchart, human). Per data set, 20 relative angular orientations differing by 18° were adjusted and for each 200 DS-TWR cycles between 3 node pairs were performed. For each DS-TWR packet, 10 features and the CIR were measured. We decided to split all sets into 24 training and 12 test sets.

| Environment \| #Train/#Test | anechoic | corridor | lab | outdoor | sum  |
|----------------------------|----------|----------|-----|---------|------|
| LOS                        | 2/2      | 2/2      | 2/2 | 2/1     | 8/7  |
| human                      | 0/0      | 3/1      | 0/1 | 2/0     | 5/2  |
| wood                       | 0/0      | 3/1      | 2/0 | 0/0     | 5/1  |
| flipchart                  | 0/0      | 2/1      | 2/0 | 0/0     | 4/1  |
| monitor                    | 0/0      | 0/0      | 2/1 | 0/0     | 2/1  |
| sum                        | 2/2      | 10/5     | 8/4 | 4/1     | 24/12 |


### Environments
* Lab: A congested room with chairs, tables and things mounted to the walls, which is often the case in office-, lab- and residential environments.
* Corridor: A narrow corridor with flat uncongested walls present in many public buildings.
* Outdoor: Free space propagation except for reflections caused by nearby buildings and the ground.
* Anechoic chamber: Multipath free environment for reference.

### Obstacles
* Non conducting objects: Wooden blocks were used to simulate obstruction caused by non conductive objects like wooden furniture.
* Conductive objects: A metal whiteboard should simulate blockage caused bymetal objects.
* Humans: When UWB is used in wearable devices the line of sight is usually obstructed in one direction.

### Indices
In our Pandas dataframe structure the following indices were used. More details as well as references can be found in the related paper.
| Index    | Description                                                                 |
|----------|-----------------------------------------------------------------------------|
| anchor   | Anchor identifier (can be 21, 22, 23)                                       |
| sample   | Sequence number                                                             |
| step     | Packet type of DS-TWR message exchance (0-Packet A, 1-Packet B, 2-Packet C) |
| path     | LOS/NLOS                                                                    |
| tagAngle | Angle between tag and anchor antennas                                       |
| location | Environment                                                                 |
| obstacle | Obstacle obstructing LOS path                                               |

### Features
The following features were collected for each packet of the DS-TWR packet exchange. More details as well as references can be found in the related paper.
| Feature                                              | Description                          |
|------------------------------------------------------|--------------------------------------|
| firstPathPower                                       | First path power                     |
| receivePower                                         | Received signal power level          |
| saturationFeat                                       | Accumulator saturation               |
| maxAmpl                                              | Max CIR amplitude                    |
| meanExcessDelay                                      | Mean excess delay                    |
| delaySpread                                          | Delay spread                         |
| kurtosis                                             | Kurtosis                             |
| prNLOS                                               | Probability NLOS                     |
| pUEP                                                 | Probability of undetected early path |
| TS_TTX1, TS_ARX1, TS_ATX1, TS_TRX1, TS_TTX2, TS_ARX2 | DS-TWR timestamps                    |
| CIR_1..99                                            | CIR samples                          |


## How to use our datasets
All data sets are stored as Pandas dataframes (`python==3.9.6` with `pandas==1.3.2`) we serialized with pickle. To load a specific dataset you can use the following commands.
```python3
import pandas as pd
import pickle
with read('test/antennaRoom___A3__08_06_2021__12_03.pkl', 'rb') as f:
  dataset = pickle.load(f)

```
To load all datasets within a particular folder, the following method can be defined.
```python3
import pandas as pd
import pickle
import os

def loadDataset(path):
    files = [f for f in os.listdir(path) if os.path.isfile(os.path.join(path, f)) and f.endswith(".pkl")]
    lsets = []
    for file in files:
        with open(os.path.join(path, file), 'rb') as f:
            measurement = pickle.load(f)
        lsets.append(measurement)
    dataset = pd.concat(lsets)
    return dataset

```


## Check also our paper
Link to be added.

## Authors
Philipp Peterseil<br>
David Märzinger<br>
Bernhard Etzlinger<br>


Institute for Communications Engineering and RF-Systems<br>
https://www.jku.at/institut-fuer-nachrichtentechnik-und-hochfrequenzsysteme/

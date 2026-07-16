# gputemp

A small CLI for Ubuntu Server 22.04+ that lists NVIDIA GPUs with temperatures.

Supports temp, junc temp, memory temp, fan speed, power usage/power limit.

## Usage

```
./gputemp                         # one-shot table output
./gputemp --json                  # one-shot JSON output
./gputemp --watch                 # refresh every 2s until Ctrl-C
./gputemp --watch --interval 5    # custom refresh interval
sudo ./gputemp                    # required to get junction temps on Blackwell GPUs
sudo ./gputemp --watch --json     # e.g. pipe into a log/monitoring sidecar
```

Example table output:

```
IDX  NAME                         PCI ID       TEMP   JUNC    MEM   FAN          POWER  NOTE
--------------------------------------------------------------------------------------------
0    NVIDIA GeForce RTX 5060      01:00.0       65C    73C    N/A   50%      104W/145W
1    NVIDIA GeForce RTX 5070 Ti   0a:00.0       65C    76C    N/A  100%      212W/320W
2    NVIDIA GeForce RTX 5050      0c:00.0       59C    64C    N/A   50%       84W/130W
3    NVIDIA GeForce RTX 5060 Ti   0d:00.0       65C    75C    N/A   82%      114W/180W
4    NVIDIA GeForce RTX 5070      0e:00.0       65C    74C    N/A   51%      150W/250W
5    NVIDIA GeForce RTX 5080      0f:00.0       68C    78C    N/A   91%      252W/360W
```

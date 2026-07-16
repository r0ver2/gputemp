# gputemp

A small CLI for Ubuntu Server 22.04+ that lists NVIDIA GPUs with temperatures.

Supports temp, junc temp, memory temp, fan speed, power usage/power limit.

## Usage

```
./gputemp                         # one-shot table output
./gputemp --json                  # one-shot JSON output
./gputemp --watch                 # refresh every 2s until Ctrl-C
./gputemp --watch --interval 5    # custom refresh interval
sudo ./gputemp                    # required to get junction temps
sudo ./gputemp --watch --json     # e.g. pipe into a log/monitoring sidecar
sudo ./gputemp --target 65c              # active fan control, target 65C (implies --watch)
sudo ./gputemp --target 65c --min 30     # ...with a 30% floor when at/below target
```

### Active fan control (`--target`, `--min`)

`--target TEMP` (e.g. `65` or `65c`) enables closed-loop fan control per
GPU, aiming to hold core temperature near that value:

- **At or below target**: fan is held at whatever speed it already had
  when control started (its "baseline") — or at `--min`, if you gave one.
  It is never forced lower than baseline without `--min` explicitly
  saying so.
- **Above target**: ramps linearly from that baseline up to 100% by
  `target + 10°C`, then stays at 100% beyond that.
- **Safety ceiling**: forces 100% regardless of the curve above 95°C
  core temp, or if the temperature couldn't be read that cycle at all.
- **On exit** (`Ctrl-C` or `SIGTERM`/`kill`): restores each GPU's
  automatic fan control policy. **This does not happen on `SIGKILL`
  (`kill -9`) or a crash** — if the process dies that way, fans stay at
  whatever they were last set to until you either restart `gputemp` or
  reboot/reload the driver.

`--target` requires root and implies `--watch` (continuous control needs
an ongoing loop, not a one-shot read). NVIDIA's own NVML documentation
carries an explicit warning worth repeating here: *manually setting fan
speed disengages the automatic curve, and setting it too low can damage
the GPU* — this tool tries to fail toward more cooling, not less
(unread temperature or the 95°C ceiling both force 100%), but it's still
your responsibility to sanity-check the behavior on your specific
hardware before relying on it unattended (e.g. a 24/7 mining rig).

Example table output:

```
ID  NAME                         PCI ID       TEMP   JUNC    MEM   FAN          POWER  NOTE
-------------------------------------------------------------------------------------------
0   NVIDIA GeForce RTX 5060      01:00.0       64C    72C    66C   56%      104W/145W
1   NVIDIA GeForce RTX 5070 Ti   0a:00.0       65C    75C    62C  100%      212W/320W
2   NVIDIA GeForce RTX 5050      0c:00.0       59C    65C    58C   50%       84W/130W
3   NVIDIA GeForce RTX 5060 Ti   0d:00.0       64C    75C    62C   91%      113W/180W
4   NVIDIA GeForce RTX 5070      0e:00.0       64C    72C    62C   56%      150W/250W
5   NVIDIA GeForce RTX 5080      0f:00.0       68C    79C    66C   92%      252W/360W

```

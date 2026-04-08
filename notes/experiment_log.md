# Experiment Log

This file records experiment progress, observations, and next steps for the history-length ablation study in bipedal reinforcement learning.

## Experiment Goal

Study how different observation history lengths affect:

- training stability
- final policy performance
- robustness and recovery behavior

## Experiment Settings

### 1. Baseline
Original observation history setting.

### 2. Short History
Reduced observation history length.

### 3. Minimal History
Only a very short history or current-state-based observation.

## Baseline

### Status
Baseline simulation successfully launched with the provided checkpoint.

### Training Setup
Used the official `play.sh` entry for policy playback in simulation.

### Results
Cassie model loaded successfully and started moving in MuJoCo simulation.

### Observations
The simulation window launched correctly. TensorFlow produced GPU-related warnings, but the baseline could still run on CPU.
## Short History

### Status
Not started yet.

### Training Setup
To be updated.

### Results
To be updated.

### Observations
To be updated.

## Minimal History

### Status
Not started yet.

### Training Setup
To be updated.

### Results
To be updated.

### Observations
To be updated.

## General Observations

- MuJoCo 2.1.0 was installed successfully.
- The `cassie_rl_walking` repository was installed successfully in the `cassie-rl` environment.
- TensorFlow 1.15 and baselines were configured successfully after fixing the protobuf compatibility issue.
- The official baseline simulation was launched successfully with `./play.sh`.

## Problems Encountered

To be updated.

## Next Steps

- prepare baseline configuration
- prepare short-history configuration
- prepare minimal-history configuration
- run first round of training
- compare qualitative behavior and training curves

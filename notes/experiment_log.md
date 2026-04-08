# Experiment Log

This file records experiment progress, observations, and next steps for the history-length ablation study in bipedal reinforcement learning.

The current goal is not only to reproduce locomotion behavior, but to understand how history-based observation design affects policy performance, robustness, and control quality in a bipedal RL framework.

## Experiment Goal

Study how different observation history lengths affect:

- training stability
- final policy performance
- robustness and recovery behavior
- qualitative motion patterns

More specifically, this project aims to investigate whether longer observation / I/O history mainly helps with:

- partial observability
- state estimation under dynamic motion
- adaptation to dynamics variation
- robustness during perturbation and recovery

## Experiment Settings

### 1. Baseline
Original observation history setting from the official framework.

### 2. Short History
Reduced observation history length while preserving part of the temporal input.

### 3. Minimal History
Very short history or near current-state-based observation.

## Baseline

### Status
Baseline simulation playback was launched successfully with the provided checkpoint.

### Training Setup
Used the official `play.sh` entry for policy playback in MuJoCo simulation.

### Results
- Cassie model loaded successfully.
- The simulation pipeline ran successfully.
- The policy playback produced valid locomotion behavior in MuJoCo.

### Observations
- The simulation window launched correctly.
- TensorFlow produced GPU-related warnings, but the baseline could still run on CPU.
- This confirms that the environment setup and official inference pipeline are functioning correctly.
- At this stage, the baseline has been verified at the playback level, while full training reproduction is still in progress.

## Short History

### Status
Not started yet.

### Planned Setup
Modify the original history-related configuration to reduce temporal input length while preserving part of the history information.

### Expected Comparison Focus
- whether training becomes less stable
- whether locomotion behavior becomes less smooth
- whether recovery behavior degrades
- whether the policy becomes more sensitive to partial observability

### Results
To be updated.

### Observations
To be updated.

## Minimal History

### Status
Not started yet.

### Planned Setup
Construct a very short-history or near current-state-based observation setting as a more aggressive ablation of the temporal input.

### Expected Comparison Focus
- whether the policy can still learn meaningful locomotion behavior
- whether robustness drops significantly
- whether failure cases become more frequent
- whether the behavior becomes noticeably less adaptive

### Results
To be updated.

### Observations
To be updated.

## General Observations

- MuJoCo 2.1.0 was installed successfully.
- The `cassie_rl_walking` repository was installed successfully in the `cassie-rl` environment.
- TensorFlow 1.15 and `baselines` were configured successfully after fixing the protobuf compatibility issue.
- The official baseline simulation was launched successfully with `./play.sh`.
- Initial paper reading and code reading have already been carried out to understand:
  - the environment structure
  - the policy input/output pipeline
  - the LPF + PD execution pipeline
  - the role of history in the observation design

## Problems Encountered

### 1. Full training reproduction is still in progress
The official baseline playback has already been verified successfully in simulation, but the full training reproduction and ablation results are still under development.

### 2. History-related ablation needs careful implementation
Changing observation/history length may affect more than one part of the pipeline. The ablation therefore needs to be implemented carefully to keep the comparison fair and interpretable.

### 3. Result interpretation requires controlled comparison
The main challenge is no longer environment setup, but designing a clean comparison that can clearly show how history length affects training stability, locomotion quality, and robustness.

## Next Steps

- clean up and document the baseline configuration
- identify the exact history-related modules to modify
- prepare short-history configuration
- prepare minimal-history configuration
- verify the training / evaluation pipeline
- run the first round of experiments
- compare qualitative behavior and training curves
- analyze whether longer history mainly helps:
  - adaptation to dynamics variation
  - state estimation under partial observability
  - robustness to perturbation
  - stability of locomotion control

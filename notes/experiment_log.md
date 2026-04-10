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
Baseline training has been launched successfully and has produced the first usable training result.

### Training Setup
The current baseline run uses the official training pipeline with:

- `CassieEnv(config=config_train_baseline)`
- `MLPCNNPolicy` as the policy architecture
- PPO training through `ppo_sgd_cnn.learn(...)`

The current baseline configuration keeps:

- `history_len_vf = 4`
- `history_len_pol = 60`

At the current stage, this baseline is used as the reference setting for the planned history-length ablation.

### Current Understanding of the Setup
From the current code reading, this baseline corresponds to the official dual-history policy setup in the codebase:

- short history is included through `previous_obs` and `previous_acs`
- long history is stored in `long_history`
- policy input is split into:
  - `obs_pol_base` for the MLP branch
  - `obs_pol_hist` for the CNN branch
- value estimation uses a separate `obs_vf` input for the critic

### First Training Result
The current baseline run has reached:

- Iteration: `1510`
- TimestepsSoFar: `6.18e+06`
- EpRewMean: `37`
- EpLenMean: `60`
- loss/kl: `0.0618`
- meanclipfracs: `0.514`
- vf_loss: `0.00119`
- ev_tdlam_before: `0.95`

### Preliminary Observations
- The training pipeline is working correctly and the model is being updated normally.
- The critic appears to be learning reasonably well at this stage, based on the low value loss and high explained variance.
- The policy, however, does not yet appear to be mature, since the mean episode length is still short relative to the environment limit.
- This result is better viewed as a first baseline checkpoint rather than a final converged policy.

### Notes
This baseline run is mainly used to establish a controlled reference for later comparison with:

- Short History
- Minimal History

For the first round of ablation, PPO hyperparameters, reward design, and policy structure will remain unchanged, while only the history-related setting will be modified.

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

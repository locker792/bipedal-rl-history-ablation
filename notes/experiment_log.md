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

For the first round of ablation, PPO hyperparameters, reward design, and policy structure remain unchanged, while only the history-related setting is modified.

## Short History

### Status
Short-history training has been launched successfully and has produced the first comparable result.

### Planned Setup
Reduce the long-history length while keeping the short-history setting unchanged:

- `history_len_vf = 4`
- `history_len_pol = 30`

All other main training settings remain unchanged in this first-round ablation.

### Expected Comparison Focus
- whether shorter long-history input improves optimization stability
- whether the policy can maintain enough temporal information for the current task
- whether the reduced history length changes episode reward and episode length
- whether the long-history setting in the baseline contains redundant temporal information

### Results
The current short-history run has reached:

- Iteration: `1509`
- TimestepsSoFar: `6.18e+06`
- EpRewMean: `40.3`
- EpLenMean: `63.1`
- loss/kl: `0.0447`
- meanclipfracs: `0.469`
- vf_loss: `0.00166`
- ev_tdlam_before: `0.949`

### Observations
- At the current training stage, the short-history setting performs better than the baseline in both mean episode reward and mean episode length.
- PPO updates also appear slightly more stable than the baseline, based on the lower KL and lower clip fraction.
- The critic still appears to learn reasonably well, although the value loss is slightly higher than in the baseline run.
- A possible interpretation is that the original 60-step long-history input may contain some redundant temporal information for the current task setup.
- At the current stage, this should still be treated as a preliminary observation rather than a final conclusion.

## Minimal History

### Status
Minimal-history training has been launched successfully and has produced the third result in the current ablation study.

### Planned Setup
Further reduce the long-history length while still keeping the current CNN structure valid:

- `history_len_vf = 4`
- `history_len_pol = 15`

This setting is used as a more aggressive reduction of temporal input while keeping the rest of the training pipeline unchanged.

### Expected Comparison Focus
- whether a much shorter long-history input still preserves enough temporal context
- whether performance drops significantly when temporal input is reduced further
- whether the policy becomes less stable or less robust
- whether a shorter history can simplify optimization in the current task setup

### Results
The current minimal-history run has reached:

- Iteration: `1509`
- TimestepsSoFar: `6.18e+06`
- EpRewMean: `43.3`
- EpLenMean: `65.5`
- loss/kl: `0.0564`
- meanclipfracs: `0.510`
- vf_loss: `0.00169`
- ev_tdlam_before: `0.949`

### Observations
- At the current training stage, the minimal-history setting achieves the best mean episode reward and the longest mean episode length among the three tested settings.
- Compared with the baseline, reducing long-history length from 60 to 15 does not hurt performance in the current setup. Instead, performance improves.
- Compared with the short-history setting, the minimal-history run achieves stronger task performance, although its optimization statistics appear slightly less stable than the 30-step setting.
- A possible interpretation is that, for the current training setup, the original long-history input may be longer than necessary, and shorter history may make learning easier.
- This result is still preliminary and should be interpreted with caution, since only one random seed has been tested and the policies may not have fully converged yet.

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
- The first round of history-length ablation has now produced three comparable runs: Baseline, Short History, and Minimal History.
- Under the current training setup, both reduced-history settings outperform the baseline in mean episode reward and mean episode length.
- The 30-step setting appears slightly more stable during PPO optimization, while the 15-step setting currently achieves the strongest task performance.

## Problems Encountered

### 1. Result interpretation is still limited by the current setup
Although the first round of ablation has already produced a clear trend, the current conclusion is still limited to:

- one random seed
- the current training budget
- the current baseline training setup

### 2. History-related ablation still needs careful interpretation
Changing history length affects the policy input structure directly. Even though the current comparison is controlled, the interpretation should still distinguish between:

- optimization difficulty
- actual temporal-information requirements
- possible task-specific redundancy in long-history input

### 3. Full paper-level reproduction is still not complete
The current results are meaningful as codebase-level baseline and ablation results, but they should not yet be treated as a full reproduction of the final paper results.

## Next Steps

- organize the current baseline / short-history / minimal-history results more clearly
- compare the three settings more systematically under the same training budget
- analyze why shorter long-history input performs better in the current setup
- decide whether to repeat the comparison with additional random seeds
- prepare figures and tables for clearer presentation of the ablation results
- continue improving the paper-to-code understanding of the history-related design

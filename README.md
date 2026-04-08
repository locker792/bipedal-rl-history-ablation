# bipedal-rl-history-ablation

A research-oriented reproduction project on history-based policy design for bipedal reinforcement learning control.

This repository is inspired by the following works:

- **Robust and Versatile Bipedal Jumping Control through Reinforcement Learning**
- **Reinforcement Learning for Versatile, Dynamic, and Robust Bipedal Locomotion Control**

Rather than only reproducing locomotion behaviors such as walking or jumping, this project uses bipedal locomotion as a representative high-dimensional control problem to study a broader question:

> How does history-based observation design contribute to adaptive, robust, and transferable RL control under partial observability?

The current focus is to understand the role of observation / I/O history in the official framework and to build a small ablation study around history length.

---

## Project Overview

This project investigates how observation history length influences the performance, stability, and robustness of a bipedal reinforcement learning policy.

The motivation comes from recent research on bipedal locomotion control, where history-based observations are important for handling:

- partial observability
- dynamic motion
- impact events
- state estimation uncertainty
- recovery behaviors
- robustness under changing dynamics

My goal is not only to reproduce a locomotion demo, but to better understand the design logic of a more general RL control framework for dynamic bipedal robots.

---

## Research Focus

This repository currently focuses on the following questions:

- Why does the policy use both short-term and long-term history?
- What is the role of history in adaptive and robust locomotion control?
- How does reducing history length affect:
  - training stability
  - final policy quality
  - robustness and recovery behavior
  - qualitative motion patterns

---

## Current Progress

So far, the following progress has been completed:

- MuJoCo 2.1.0 was installed successfully.
- The official Cassie RL repository was set up successfully in the local environment.
- TensorFlow 1.15 and `baselines` were configured successfully after resolving compatibility issues.
- The official baseline policy playback was launched successfully in simulation using `./play.sh`.
- Initial paper reading and code reading have been completed for:
  - environment structure
  - policy input/output pipeline
  - LPF + PD execution pipeline
  - history-related observation design
- The first ablation axis has been defined:
  - **Baseline**
  - **Short History**
  - **Minimal History**

At the current stage, this repository should be viewed as an early-stage research reproduction project rather than a finished benchmark reproduction.

---

## Motivation

I created this project as a small research-oriented study to better understand RL-based bipedal locomotion control, especially the role of history information in adaptive and robust control.

More specifically, I am interested in using this project to study how a history-based policy design may support a more general RL control framework, instead of treating locomotion only as an isolated behavior reproduction task.

---

## Experimental Design

The first planned ablation is on the observation / history length used in policy input.

### 1. Baseline
Original observation/history setting from the official framework.

### 2. Short History
Reduced observation/history length while preserving part of the temporal input.

### 3. Minimal History
Very short history or near current-state-based observation.

The objective is not only to compare final performance, but also to analyze what changes qualitatively when the history is reduced.

---

## Planned Evaluation Metrics

The following metrics and observations are planned for comparison:

- episode reward
- success rate
- episode length
- training stability
- qualitative behavior comparison
- stability and failure cases
- action smoothness
- robustness under disturbance or dynamics variation

Where possible, results will include both:

- **quantitative comparison**
- **qualitative video-based analysis**

---

## Paper-to-Code Mapping

The goal of this section is to connect the main ideas in the paper to the implementation.

### 1. History-based policy design
Paper idea:
- short-term history for recent feedback
- longer history for richer temporal information

Code-related components:
- observation/action history buffers
- policy input construction
- history-length-related environment settings

### 2. Policy architecture
Paper idea:
- history-aware policy structure for locomotion control

Code-related components:
- policy definition
- actor/critic input spaces
- history-dependent observation pipeline

### 3. Action execution pipeline
Paper idea:
- policy output -> filtered target -> low-level controller -> simulation

Code-related components:
- normalized action output
- action-to-target mapping
- low-pass filtering
- PD target update in the Cassie environment

### 4. Reference motion and commands
Paper idea:
- locomotion skill is parameterized by reference motion and command inputs

Code-related components:
- reference generator
- environment configuration for playback / task setup

### 5. Robustness-related ingredients
Paper idea:
- observation design, randomization, and perturbation handling are all important for robust control

Code-related components:
- environment randomization
- perturbation generator
- noisy observation setting

---

## Repository Structure

```text
configs/    # experiment configurations
scripts/    # training and evaluation scripts
results/    # curves, tables, visual results, and videos
notes/      # experiment logs, reading notes, and observations
```
## Current Status

This repository is under active development.

At the current stage, the goal is to build a small but complete research-style project including:

- official baseline reproduction
- paper-to-code understanding
- history-length ablation setup
- result visualization
- analysis of observations

The baseline playback has already been launched successfully in MuJoCo simulation, which confirms that the environment and the official inference pipeline are working.

However, the full training and ablation results are still in progress.

---

## General Observations

Current observations from the reproduction process:

- The official simulation environment can run successfully on CPU.
- The project already suggests a clear research direction beyond pure locomotion reproduction: **history-based policy design for adaptive and robust RL control**.
- The most important near-term task is not to over-expand the scope, but to make the first ablation clean and interpretable.

---

## Next Steps

Short-term next steps:

1. clean up and document the baseline configuration
2. prepare short-history configuration
3. prepare minimal-history configuration
4. verify the training / evaluation pipeline
5. compare qualitative behavior and training curves
6. analyze whether longer history mainly helps:
   - adaptation to dynamics variation
   - state estimation under partial observability
   - robustness to perturbation
   - stability of locomotion control

---

## Notes

This repository is intended as a research learning and reproduction project.

The purpose is to progressively develop:

- a deeper understanding of RL-based locomotion control
- a clearer paper-to-code mapping
- a more systematic view of history-based policy design in robotics

---

## References

1. Zhongyu Li et al., **Robust and Versatile Bipedal Jumping Control through Reinforcement Learning**
2. Zhongyu Li et al., **Reinforcement Learning for Versatile, Dynamic, and Robust Bipedal Locomotion Control**

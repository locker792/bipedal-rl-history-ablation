# bipedal-rl-history-ablation

A small ablation study on how observation history length affects bipedal reinforcement learning performance and stability.

## Project Overview

This project investigates how the length of observation history influences the performance, stability, and robustness of a bipedal reinforcement learning policy.

The motivation comes from recent research on bipedal locomotion and jumping control, where history-based observations are often important for handling dynamic motion, impact events, and recovery behaviors.

## Research Question

How does observation history length affect bipedal RL control performance?

More specifically, this project compares different history settings and analyzes how they influence:

- training stability
- final policy performance
- robustness and recovery behavior

## Experimental Design

Three settings will be compared:

1. **Baseline**: original observation history setting
2. **Short History**: reduced observation history length
3. **Minimal History**: only a very short history or current-state-based observation

## Planned Evaluation Metrics

- episode reward
- success rate
- episode length
- qualitative behavior comparison
- stability / failure cases

## Repository Structure

```text
configs/     # experiment configurations
scripts/     # training and evaluation scripts
results/     # curves, tables, and visual results
notes/       # experiment notes and observations
```


## Current Status

This repository is under active development.

At the current stage, the goal is to build a small but complete research-style project including:

- experiment setup
- ablation comparison
- result visualization
- analysis of observations

## Motivation

I created this project as a small research-oriented study to better understand bipedal reinforcement learning control, especially the role of history information in dynamic locomotion tasks.

## Future Updates

Upcoming updates will include:

- experiment configurations
- training curves
- result tables
- analysis of performance differences
- possible videos or qualitative comparisons

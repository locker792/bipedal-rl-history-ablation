# Paper-to-Code Mapping

This note connects the main ideas in the paper to the implementation in the official codebase.

The purpose of this file is not to provide a complete line-by-line explanation, but to build a clear mapping between the paper's control framework and the actual software structure used in the project.

---

## 1. Overall Goal

### Paper
The paper does not only aim to reproduce specific locomotion behaviors such as walking, running, or jumping. It proposes a more general RL-based control framework for dynamic and robust bipedal locomotion.

### Code
The codebase reflects this idea by organizing the system around:

- a reusable locomotion environment
- a history-aware policy structure
- a reference-motion-driven task setup
- an action execution pipeline with filtering and low-level control
- robustness-related components such as randomization and perturbation

### Current Understanding
The current project treats locomotion not only as a behavior reproduction problem, but also as a testbed for understanding a more general history-based RL control framework.

---

## 2. History-Based Observation Design

### Paper
A central idea in the paper is that observation / I/O history is important for adaptive and robust control under partial observability.

The dual-history design is intended to capture both:

- recent information for immediate control
- longer temporal information for richer system context

### Code
This idea appears in the code through history-related observation construction, including:

- recent observation/action buffers
- longer temporal history used by the policy
- policy inputs that are separated according to different roles in the architecture

### Current Understanding
This is the main focus of the current ablation project.

The history-length ablation is intended to test whether reducing temporal information mainly affects:

- training stability
- locomotion quality
- recovery behavior
- robustness under partial observability

---

## 3. Policy Architecture

### Paper
The paper describes a history-aware policy structure rather than a purely current-state-based policy.

The architecture is designed to process temporal information in a structured way.

### Code
This design is reflected in the policy implementation through:

- dedicated policy definition
- actor/critic-related observation spaces
- separate handling of different parts of the observation pipeline
- policy components that explicitly depend on history-related inputs

### Current Understanding
The policy is not treated as a black box in this project.

A key part of the reproduction effort is to understand how the observation structure is connected to the final control behavior.

---

## 4. Action Execution Pipeline

### Paper
The paper’s control pipeline is not simply:

policy output -> simulator

Instead, the policy output is part of a larger control pipeline involving:

- action output from the policy
- target-related processing
- filtering
- low-level control
- simulated physical execution

### Code
This idea is reflected in the code by the presence of modules related to:

- normalized action output
- action-to-target mapping
- low-pass filtering
- low-level PD-related control updates inside the environment
- MuJoCo simulation stepping

### Current Understanding
Understanding this pipeline is important because the final locomotion behavior depends not only on the neural policy, but also on how the action is executed through the control stack.

---

## 5. Reference Motion and Task Parameterization

### Paper
The paper uses reference motions and task-related inputs to organize locomotion skills and guide the policy.

This means the learned controller is not operating without structure; it is conditioned on task-related information.

### Code
This design is reflected in the code through components such as:

- reference-motion-related modules
- task / command configuration
- environment-side setup for playback and control input generation

### Current Understanding
The reference-related modules are important for understanding how the policy is guided during locomotion and how different settings may be compared fairly.

---

## 6. Robustness-Related Components

### Paper
Robust control is an important theme in the paper.

The framework emphasizes more than nominal locomotion performance. It also considers issues such as:

- partial observability
- disturbance handling
- dynamics variation
- adaptive behavior

### Code
This is reflected in the code through components such as:

- environment randomization
- perturbation generation
- noisy observation settings
- history-related design choices

### Current Understanding
For the current project, robustness is not treated as a separate topic from history design.

One of the main research questions is whether longer observation / I/O history improves robustness by providing better temporal context.

---

## 7. Why This Mapping Matters

This mapping is important for at least three reasons:

### 1. It helps prevent shallow reproduction
Running the official code is not enough. A research-oriented reproduction should connect implementation details back to the design logic of the paper.

### 2. It supports fair ablation design
If history length is changed without understanding how the observation pipeline is constructed, the resulting comparison may become misleading or unfair.

### 3. It improves interpretability
A clear paper-to-code mapping makes it easier to explain:

- what was changed
- why it was changed
- and how the observed behavior should be interpreted

---

## 8. Current Focus of This Mapping

At the current stage, the most important mapping targets are:

- history-related observation design
- policy input structure
- action execution pipeline
- reference-related components
- robustness-related modules

This file will be updated as code understanding becomes more detailed and the ablation implementation progresses.

---

## 9. Notes

This file is intentionally written at the conceptual level.

Its current role is to serve as a bridge between:

- paper reading
- code reading
- experiment design

A more detailed mapping can be added later, including specific files, functions, and variables, once the implementation details for the history-length ablation are finalized.

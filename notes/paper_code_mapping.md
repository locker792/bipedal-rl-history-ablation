# Code Notes

This note records my current paper-to-code understanding for the official Cassie RL codebase.

Its purpose is practical rather than presentational:
to identify which parts of the implementation correspond to the main design choices in the paper, and to use that understanding to support a clean history-length ablation later.

This file is a working research note, not a complete code walkthrough.
Some mappings below are already clear from code reading, while others are still being verified.

---

## Scope

The main question of this project is not only whether the official baseline can run, but how the key design ideas in the paper are actually realized in code.

For the current stage, the most relevant paper components are:

- history-based observation design
- policy input structure
- action execution pipeline
- reference motion / task-related input
- robustness-related environment components

These are the parts most likely to matter for a fair history-length ablation.

---

## Current Mappings

### 2.1 Environment backbone -> `CassieEnv`

**Paper-side idea:**  
The locomotion controller is implemented inside a larger control framework rather than as an isolated policy network.

**Code-side mapping:**  
`rlenv/cassie_env.py`

**Current understanding:**  
`CassieEnv` appears to be the main environment-side implementation of the framework. From current code reading, it is responsible for several components that are central to the paper:

- simulation setup
- history-related buffers
- reference-related modules
- action filtering
- low-level PD-related execution interface
- reward-related setup
- randomization / perturbation-related setup

This means that understanding `CassieEnv` is necessary before modifying any history-related setting.

---

### 2.2 History-related observation design -> recent buffers and long-history buffer

**Paper-side idea:**  
The policy uses temporal information rather than a purely current-state-based observation.

**Code-side mapping:**  
In `CassieEnv`, current relevant variables include:

- `previous_obs`
- `previous_acs`
- `long_history`

**Current understanding:**  
These variables appear to implement different forms of temporal context used by the policy.
At the current stage, this is the most important mapping for the planned ablation.

What still needs verification:

- exactly how these buffers are converted into policy inputs
- whether all history-related logic is controlled only here or also elsewhere in the policy/input pipeline

---

### 2.3 Policy structure -> `MLPCNNPolicy`

**Paper-side idea:**  
The paper uses a history-aware policy structure rather than a purely memoryless current-state policy.

**Code-side mapping:**  
`ppo/policies.py`  
Policy class used by playback: `MLPCNNPolicy`

**Current understanding:**  
From the current code reading and the playback entry, `MLPCNNPolicy` is the main policy implementation corresponding to the paper’s history-aware design.

The name itself suggests that policy computation is split across at least:

- an MLP-related pathway
- a CNN-related pathway

This is consistent with the paper’s use of structured temporal input processing.

What still needs verification:

- which exact observation components go into each branch
- how actor and critic inputs differ internally

---

### 2.4 Playback / inference entry -> `play.py`

**Paper-side idea:**  
A trained policy is executed inside the environment using the full control stack.

**Code-side mapping:**  
`scripts/play.py`

**Current understanding:**  
`play.py` is the official simulation playback entry.
It does not define the framework itself, but it is useful because it reveals the runtime path:

- create `CassieEnv`
- create `MLPCNNPolicy`
- load checkpoint
- call `reset()`
- call `pi.act(...)`
- step the environment
- render the result

This file is helpful for identifying which parts of the codebase are actually active during policy execution.

---

### 2.5 Action execution pipeline -> action mapping, filtering, PD interface

**Paper-side idea:**  
The final locomotion behavior is not determined by the neural policy alone.
The action passes through a larger execution pipeline before affecting the robot.

**Code-side mapping:**  
Current relevant components in `CassieEnv` include:

- action-related mapping logic
- `ActionFilterButter`
- PD-related structures such as `pd_in_t`
- target-related fields such as `pTarget`, `pGain`, `dGain`

**Current understanding:**  
From current code reading, the policy output is not applied directly to the simulator state.
Instead, there is an environment-side execution stack involving:

- action representation
- target conversion / mapping
- low-pass filtering
- PD-related target update
- simulation stepping

This part is important because changes in behavior may come not only from the policy itself, but also from how policy outputs are executed.

What still needs verification:

- the exact action-to-target transformation path during `step()`
- which parts of the control stack are most sensitive to observation-history changes

---

### 2.6 Reference-related components -> `ReferenceGenerator`

**Paper-side idea:**  
The controller is not learning without structure; it is conditioned by reference-related inputs and task-related setup.

**Code-side mapping:**  
`ReferenceGenerator` used inside `CassieEnv`

**Current understanding:**  
`ReferenceGenerator` appears to be the main environment-side component that provides reference-related information used by the control framework.

This is important because the history-length ablation should ideally leave reference-related inputs unchanged, so that the comparison remains focused on temporal observation design rather than on task definition.

---

### 2.7 Robustness-related components -> randomization / perturbation / noise

**Paper-side idea:**  
The paper is concerned not only with nominal locomotion performance, but also with robustness under uncertainty and disturbance.

**Code-side mapping:**  
Current relevant components include:

- `EnvRandomlizer`
- `PerturbationGenerator`
- noisy observation settings in the environment

**Current understanding:**  
These modules appear to implement environment-side uncertainty and disturbance mechanisms.
They are not the immediate focus of the first ablation, but they matter because one of the research questions is whether longer history helps robustness partly by providing better temporal context under uncertainty.

---

## Why It Matters

At the current stage, this mapping is already useful in three ways:

### 3.1 It prevents a shallow reproduction workflow
Running the official baseline is only the starting point.
For a meaningful ablation, I need to know where temporal observation design is actually implemented.

### 3.2 It narrows down where history-related changes should be made
The current code reading suggests that the most relevant starting points are:

- history buffers in `CassieEnv`
- policy input construction
- the policy-side use of observation spaces

### 3.3 It helps define what should remain fixed
To keep the first ablation interpretable, components such as:

- reference-related inputs
- playback / runtime logic
- low-level execution stack

should not be changed unless necessary.

---

## Unresolved Points

The following points still need more code-level verification:

- the exact observation assembly path before policy input
- the exact split between actor input and critic input
- the exact relationship between long-history storage and policy-side CNN input
- the most minimal set of code changes needed to produce a fair short-history / minimal-history comparison

These points are especially important because the ablation should modify temporal information cleanly, without accidentally changing other parts of the pipeline.

---

## Working Conclusion

At the current stage, the most likely core implementation path for the planned history-length ablation is:

1. understand how history is stored in `CassieEnv`
2. identify how stored history is converted into policy input
3. identify how `MLPCNNPolicy` consumes those inputs
4. modify only the history-related part while keeping the rest of the framework fixed as much as possible

This is the main reason for writing this note:
to turn paper understanding into a concrete and controlled implementation plan.

---

## Next Checks

The next code-reading targets are:

- `CassieEnv.reset()`
- `CassieEnv.step()`
- observation construction functions
- `MLPCNNPolicy` internals
- history-related configuration points

Once these are clearer, this note can be updated into a more precise mapping with specific functions and variables.

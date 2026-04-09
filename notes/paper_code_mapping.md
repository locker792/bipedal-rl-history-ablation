# Code Notes
The main purpose of this note is to track where those design choices appear in code and what still needs to be checked before implementing the history-length ablation.

---

## Scope

For the current stage, the main parts I need to track are:

- history-related observation design
- policy input structure
- action execution pipeline
- reference-related modules
- robustness-related environment modules

These are the parts most directly related to the planned history-length ablation.

---

## Current Mappings

### Environment backbone -> `CassieEnv`

**Paper-side idea:**  
The controller is implemented as part of a larger control framework rather than as an isolated policy network.

**Code-side mapping:**  
`rlenv/cassie_env.py`

**Current understanding:**  
`CassieEnv` is the main environment-side implementation of the framework. From the current code reading, it handles several components that are central to the paper:

- simulation setup
- history-related buffers
- reference-related modules
- action filtering
- low-level PD-related execution interface
- reward-related setup
- randomization / perturbation-related setup

For the history-length ablation, `CassieEnv` is one of the main files that needs to be understood first.

---

### History-related observation design -> recent buffers and long-history buffer

**Paper-side idea:**  
The policy uses temporal information rather than relying only on the current state.

**Code-side mapping:**  
Current relevant variables in `CassieEnv` include:

- `previous_obs`
- `previous_acs`
- `long_history`

**Current understanding:**  
These variables implement the main temporal context used by the policy.

This is the core mapping for the planned ablation, since the current project is centered on reducing or modifying the amount of temporal information available to the policy.

What still needs checking:

- the exact path from these buffers to the final policy inputs
- whether all history-related logic is handled here or partly in the policy/input pipeline

---

### Policy structure -> `MLPCNNPolicy`

**Paper-side idea:**  
The paper uses a history-aware policy rather than a purely current-state-based policy.

**Code-side mapping:**  
`ppo/policies.py`  
Policy class used by playback: `MLPCNNPolicy`

**Current understanding:**  
`MLPCNNPolicy` is the main policy implementation corresponding to the paper’s history-aware design.

From the code interface and naming, the policy includes at least:

- an MLP-related branch
- a CNN-related branch

This is consistent with the paper’s structured use of temporal input.

What still needs checking:

- which observation components are routed to each branch
- how actor and critic inputs are separated internally

---

### Playback entry -> `play.py`

**Paper-side idea:**  
A trained policy is executed inside the full environment and control stack.

**Code-side mapping:**  
`scripts/play.py`

**Current understanding:**  
`play.py` is the official simulation playback entry.

It is useful because it makes the runtime path explicit:

- create `CassieEnv`
- create `MLPCNNPolicy`
- load checkpoint
- call `reset()`
- call `pi.act(...)`
- step the environment
- render the result

This file helps identify which parts of the code are actually active during policy execution.

---

### Action execution pipeline -> action mapping, filtering, PD interface

**Paper-side idea:**  
The final locomotion behavior is not determined by the neural policy alone. The policy output goes through an execution pipeline before affecting the robot.

**Code-side mapping:**  
Current relevant parts in `CassieEnv` include:

- action-related mapping logic
- `ActionFilterButter`
- PD-related structures such as `pd_in_t`
- target-related fields such as `pTarget`, `pGain`, `dGain`

**Current understanding:**  
The policy output is not applied directly to simulator state. Instead, the environment includes a full execution stack involving:

- action representation
- target conversion / mapping
- low-pass filtering
- PD-related target update
- simulation stepping

This matters because changes in behavior may come from the policy itself, from the execution pipeline, or from the interaction between the two.

What still needs checking:

- the exact action-to-target path during `step()`
- which parts of the control stack are most sensitive to history-related changes

---

### Reference-related components -> `ReferenceGenerator`

**Paper-side idea:**  
The controller is not learning without structure. It is conditioned on reference-related inputs and task-related setup.

**Code-side mapping:**  
`ReferenceGenerator` used inside `CassieEnv`

**Current understanding:**  
`ReferenceGenerator` provides the reference-related information used by the control framework.

This is important for the ablation because the reference-related inputs should remain unchanged as much as possible, so that the comparison stays focused on temporal observation design rather than task definition.

---

### Robustness-related modules -> randomization / perturbation / noise

**Paper-side idea:**  
The paper studies not only nominal locomotion performance, but also robustness under uncertainty and disturbance.

**Code-side mapping:**  
Current relevant modules include:

- `EnvRandomlizer`
- `PerturbationGenerator`
- noisy observation settings in the environment

**Current understanding:**  
These modules implement uncertainty and disturbance on the environment side.

They are not the immediate focus of the first ablation, but they still matter because one of the main questions here is whether longer history improves robustness by providing better temporal context under uncertainty.

---

## Unresolved Points

The following parts still need more code-level checking:

- the exact observation assembly path before policy input
- the exact split between actor input and critic input
- the exact relation between long-history storage and the CNN input on the policy side
- the smallest set of code changes needed for a fair short-history / minimal-history comparison

These points matter because the ablation should modify temporal information cleanly, without accidentally changing other parts of the pipeline.

---

## Working Conclusion

At the current stage, the implementation path for the history-length ablation is:

1. identify how history is stored in `CassieEnv`
2. identify how stored history becomes policy input
3. identify how `MLPCNNPolicy` uses those inputs
4. modify only the history-related part while keeping the rest of the framework fixed as much as possible

The main issue now is no longer understanding the overall algorithm, but making sure the code-level modification is clean and the comparison is fair.

---

## Next Checks

The next code-reading targets are:

- `CassieEnv.reset()`
- `CassieEnv.step()`
- observation construction functions
- `MLPCNNPolicy` internals
- history-related configuration points

Once these parts are fully pinned down, this note can be updated with more specific functions and variables.

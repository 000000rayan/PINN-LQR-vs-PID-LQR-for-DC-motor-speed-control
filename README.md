# PINN + LQR vs PID + LQR for DC motor speed control

> **Status: work in progress. No results yet.** This repository documents the plan, the model, and the reading behind an M2 project. Code and results will be added as they exist.

## Research question

On a real DC motor, which controller structure gives the lowest cost (tracking error and energy) under the same task and conditions: a PID with LQR, or a physics-informed neural network (PINN) plan with LQR feedback?

This is a careful comparison, not a claim of a new method. Related work on PINN-based control exists (see References), and a full literature search is part of the plan.

## Planned comparison

All arms use the same motor, the same task, the same cost function, and the same measured quantities.

| Arm | Description |
|---|---|
| PID | tuned against the shared cost |
| LQR | designed from the identified motor model |
| PID + LQR | LQR with PID action added |
| PINN plan | voltage-versus-time plan computed by a Control PINN, played back open-loop |
| PINN + LQR | PINN plan with LQR feedback around it |

Planned test conditions: nominal, parameter mismatch, load disturbance, sensor noise.
Planned metrics: speed error, energy (measured voltage times current), cost, compute time, spread over repeated runs.

## Motor model

Standard brushed DC motor, with back-EMF $e = K\omega$ and motor torque $T_m = K i$:

$$L\frac{di}{dt} = V - R\,i - K\,\omega$$

$$J\frac{d\omega}{dt} = K\,i - b\,\omega - T_L$$

States: current $i$ and speed $\omega$. Input: voltage $V$. Parameters $R, L, K, J, b$ will be identified from measurements on the real motor.

## Roadmap

- [x] Define the research question and the comparison design
- [ ] Derive and verify the motor model on paper
- [ ] Identify the motor parameters (resistance, inductance, motor constant, friction, inertia)
- [ ] LQR and tuned PID on the identified model
- [ ] Control PINN on a problem with a known exact solution, then on the motor
- [ ] Hardware experiments with repeated runs
- [ ] Results, analysis, and report

## Code (current state)

| File | What it does |
|---|---|
| `src/motor_model.py` | Simulates the two-equation motor open-loop, with a voltage step and a load-torque step, and checks the settled values against the hand-derived steady state |
| `src/lqr_baseline.py` | Finite-horizon LQR on the motor model; checks the state, adjoint and control optimality conditions numerically |
| `src/pid_vs_lqr.py` | A PID tuned against the same cost as LQR, compared with LQR on the nominal motor and on motors that differ from the model (simulation only; the PID uses a noise-free derivative) |

**These scripts use placeholder motor parameters, not measurements.** No PINN code has been added yet.

Run from the repository root:

```
pip install -r requirements.txt
python src/motor_model.py
python src/lqr_baseline.py
python src/pid_vs_lqr.py
```

## Planned hardware

JGB37-520 12 V gear motor with Hall encoder, Arduino Uno R3, TB6612FNG driver, INA219 current sensor, DS18B20 temperature sensor, bench power supply.

## References

- M. Raissi, P. Perdikaris, G. E. Karniadakis, *Physics-informed neural networks: a deep learning framework for solving forward and inverse problems involving nonlinear partial differential equations*, J. Comput. Phys. 378 (2019) 686-707.
- J. Barry-Straume, A. Sarshar, A. A. Popov, A. Sandu, *Physics-Informed Neural Networks for PDE-Constrained Optimization and Control*, Commun. Appl. Math. Comput. 8 (2026) 1283-1306. DOI: 10.1007/s42967-025-00499-x
- S. Mowlavi, S. Nabi, *Optimal control of PDEs using physics-informed neural networks*, J. Comput. Phys. 473 (2023) 111731.
- *Comparison of Deep Learning and Deterministic Algorithms for Control Modeling*, Sensors (2022), DOI: 10.3390/s22176362.

## Notes

[Edit this line to say how you used AI assistance, for example for derivations or scripts, and that you checked the results yourself.]

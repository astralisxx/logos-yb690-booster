# LOGOS-YB690 Booster: digital twin of a two-stage reciprocating gas booster

## Introduction

https://github.com/user-attachments/assets/d17efe2b-39cb-4f09-9862-afdccf7bb673

<p align="center"><em>The 3-D machine: one electric motor drives the crankshaft of two finned cylinders, cooled by two fans; the ball check valves are slowed 4x. Motion, valve lift and gas colour all come from the physics solve.</em><br>
Full 2:26 tour: <a href="https://github.com/astralisxx/logos-yb690-booster/releases/latest">download from Releases</a>.</p>

A desktop application that simulates a reciprocating booster compressor one crank angle at a time
and can also run the model in reverse to size a machine for a stated duty. It is for engineers who
want to see how geometry, valve behaviour and operating conditions shape pressure, temperature and
delivered flow before they build or run hardware. The physics core is a port of the author's MATLAB
research models, kept in step with them by golden tests. Everything solves on the user's own
computer and works offline.

---

## What it does

- **3-D machine view.** A cutaway of the booster built from its CAD model. A solved cycle drives
  piston motion, ball lift and gas colour; nothing is keyframed. Pressure and temperature views.
- **Single Stage.** One cylinder with its valves (nitrogen, 40 to 140 bar, 75 rpm): P-V diagram,
  pressure, temperature and mass-flow histories, valve lift and summary metrics.
- **Multi Stage.** The two-stage booster with interstage pipe and four check valves. The interstage
  pressure is not an input: it settles where stage 2 takes exactly what stage 1 delivers.
- **Design (inverse design).** State a duty (flow, power, efficiency, interstage pressure) and the
  app returns a machine that meets it, verified at full fidelity, with parameter sensitivities.
- **Gases:** nitrogen, air, argon, hydrogen, with real-gas properties. English / Turkish interface.

## How it works

- Real-gas equation of state (Peng-Robinson) for pressure, compressibility and heat capacities.
- Spring-mass-damper check valves that open on a cracking pressure, with compressible flow set by
  ball lift.
- Wall heat loss on each cylinder and the interstage pipe.
- Stiff implicit integration (SciPy BDF), streamed to the screen one crank cycle at a time.
- Parity tests against the MATLAB reference models, on both building blocks and integrated results.
- Inverse design as optimisation over the physics model itself, not a machine-learned surrogate.

## Video

**Live geometry.** The Multi Stage 3-D section redraws as the inputs move: the machine geometry and speed.

https://github.com/user-attachments/assets/0c9739f4-213e-4494-bfae-4fe1ab70f7a6

**Run and results.** Ten crank cycles of real-gas physics solved on the user's computer: pressures, P-V loops, temperatures, mass flows and valve lift, then the section replays the solved cycle in pressure and temperature.

https://github.com/user-attachments/assets/aa184507-3f72-4cd1-baa2-39ed6d15b1e9

**Inverse design.** Pick the targets and the free inputs; candidate machines are simulated in parallel and the best one is checked against every target.

https://github.com/user-attachments/assets/4ad92f2b-0d38-49f6-8680-2f4612710713

## Tech stack

| Layer | Technology |
|---|---|
| Physics core | Python, NumPy, SciPy (BDF) |
| Local engine | FastAPI, running only on the user's machine |
| Desktop shell | Tauri (Rust), bundling the Python engine |
| Interface | HTML, CSS and plain JavaScript (no framework); Plotly charts; three.js (WebGL) 3-D views |
| Reference models | MATLAB (`ode15s`), the source of truth for parity tests |
| Testing | pytest (with xdist), Node suites, Playwright browser suites run on WebKit |
| CI | GitHub Actions |

All front-end libraries and fonts ship with the app, so it needs no network connection.

## Engineering quality

- **Testing:** a large Python suite including MATLAB-parity golden tests, plus JavaScript interface
  and browser suites run on WebKit, the engine the desktop app ships with.
- **CI and releases:** every push runs static checks and the full-solver suites; semantic
  versioning with a release gate.
- **Robustness:** inputs validated at the server boundary; bad input returns a coded warning, and a
  liquid-phase guard refuses inlet states where the gas would be liquid.

## Status

Single Stage, Multi Stage, Design and the 3-D views work in a macOS desktop build (Apple silicon).

## Source code and access

The source code is private; this repository holds only the description, images and video. Read
access may be granted on request for academic review: open an issue here.

## Copyright

Developed by Ahmet Berkay Şimşek (METU) for Logos Kimya. Copyright (c) 2025-2026 Logos Kimya. All
rights reserved. No licence is granted to use, copy, modify or distribute the software, its source
code, or the images and video in this repository.

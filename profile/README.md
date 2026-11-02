<div align="center">

<img alt="Industrial Mathematics Group" src="../logos/svg/logo.svg#gh-light-mode-only" width="640">
<img alt="Industrial Mathematics Group" src="../logos/svg/logo-dark.svg#gh-dark-mode-only" width="640">

[![Zenodo community](https://img.shields.io/badge/Zenodo-community-1682D4.svg)](https://zenodo.org/communities/img)
[![Website](https://img.shields.io/badge/web-math.rptu.de-1B2238.svg)](https://math.rptu.de/ags/techno)

</div>

We are the **Industrial Mathematics Group** at RPTU Kaiserslautern-Landau. We develop models, numerical methods and open-source software for problems from science and industry, with a focus on partial differential equations.

## Research

- **Hyperbolic conservation laws:** meshfree and particle methods, high-order schemes, kinetic models
- **Traffic and transport on networks:** macroscopic and kinetic models, coupling conditions
- **PDE-constrained optimisation and control:** optimal control, feedback and stabilisation
- **Simulation and visualisation tooling:** reproducible parameter studies, caching and interactive exploration

## Software

| Package | Description | Docs |
|---|---|---|
| [HyperCloudCore.jl](https://github.com/blhackslash/HyperCloudCore.jl) | Meshfree engine for hyperbolic systems: particle grids, MLS schemes (upwind, MUSCL, WENO), MOOD, IMEX time stepping | [stable](https://blhackslash.github.io/HyperCloudCore.jl/stable/) |
| [HyperCloud.jl](https://github.com/blhackslash/HyperCloud.jl) | Ready-to-use solvers on top of HyperCloudCore: advection, Burgers, Euler, BGK and kinetic relaxation | [stable](https://blhackslash.github.io/HyperCloud.jl/stable/) |
| [PDEStudioCore.jl](https://github.com/blhackslash/PDEStudioCore.jl) | Headless engine for parameter sweeps, hash-based result caching and error statistics | |
| [PDEStudio.jl](https://github.com/blhackslash/PDEStudio.jl) | Interactive Makie front end for exploring and exporting PDE simulations | |

<!-- Add further packages here. -->

## Open science

Our code is open source, and releases are archived with DOIs in our [Zenodo community](https://zenodo.org/communities/img). If you use our software in your research, please cite the corresponding Zenodo record; each repository lists its citation.

Questions, bug reports and contributions are welcome. Please open an issue in the relevant repository; see [CONTRIBUTING](../CONTRIBUTING.md) for details.

## Contact

[Technomathematics Group, RPTU Kaiserslautern](https://math.rptu.de/ags/techno) · Gottlieb-Daimler-Straße, Building 48, 67663 Kaiserslautern, Germany

---

<sub>Logos © Industrial Mathematics Group, licensed under [CC BY 4.0](../logos/LICENSE). Code in our repositories is licensed as stated in each repository.</sub>

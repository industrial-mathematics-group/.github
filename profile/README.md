<div align="center">

<img alt="Industrial Mathematics Group" src="../logos/svg/logo.svg#gh-light-mode-only" width="640">
<img alt="Industrial Mathematics Group" src="../logos/svg/logo-dark.svg#gh-dark-mode-only" width="640">

<!-- Badges: replace <ZENODO-COMMUNITY> with your community id -->
[![Zenodo community](https://img.shields.io/badge/Zenodo-community-1682D4.svg)](https://zenodo.org/communities/img)
[![Website](https://img.shields.io/badge/web-organization-1B2238.svg)](https://industrial-mathematics.org)

</div>

We are the **Industrial Mathematics Group** based in Kaiserslautern (Germany). We develop models, numerical methods and open-source software for problems from science and industry, with a focus on partial differential equations.

## Research

- **Hyperbolic conservation laws:** meshfree and particle methods, high-order schemes, kinetic models
- **Traffic and transport on networks:** macroscopic and kinetic models, coupling conditions
- **PDE-constrained optimisation and control:** optimal control, feedback and stabilisation
- **Simulation and visualisation tooling:** reproducible parameter studies, caching and interactive exploration

## Software

| Package | Description | Docs |
|---|---|---|
| [HyperCloudCore.jl](https://github.com/industrial-mathematics-group/HyperCloudCore.jl) | Meshfree engine for hyperbolic systems: particle grids, MLS schemes (upwind, MUSCL, WENO), MOOD, IMEX time stepping | [stable](https://industrial-mathematics-group.github.io/HyperCloudCore.jl/stable/) |
| [HyperCloud.jl](https://github.com/industrial-mathematics-group/HyperCloud.jl) | Ready-to-use solvers on top of HyperCloudCore: advection, Burgers, Euler, BGK and kinetic relaxation | [stable](https://industrial-mathematics-group.github.io/HyperCloud.jl/stable/) |
| [PDEStudioCore.jl](https://github.com/industrial-mathematics-group/PDEStudioCore.jl) | Headless engine for parameter sweeps, hash-based result caching and error statistics | [stable](https://industrial-mathematics-group.github.io/PDEStudioCore.jl/stable/) |
| [PDEStudio.jl](https://github.com/industrial-mathematics-group/PDEStudio.jl) | Interactive Makie front end for exploring and exporting PDE simulations | [stable](https://industrial-mathematics-group.github.io/PDEStudio.jl/stable/) |

<!-- Add further packages here. -->

## Open science

Our code is open source, and releases are archived with DOIs in our [Zenodo community](https://zenodo.org/communities/img). If you use our software in your research, please cite the corresponding Zenodo record; each repository lists its citation.

Questions, bug reports and contributions are welcome. Please open an issue in the relevant repository; see [CONTRIBUTING](../.github/CONTRIBUTING.md) for details.

**AI tools are welcome** in contributions, as long as their use is declared. Every change to `main` states how it was written (G0 human to G3 AI-autonomous), how thoroughly a human reviewed it (H0 plausibility check to H3 independently validated) and how thoroughly an AI reviewed it (A0 none to A3 extensive review or benchmark by a different model). See our [AI policy](../.github/CONTRIBUTING.md#ai-assisted-contributions).

## Contact

[info\@industrial-mathematics.org](mailto:info\@industrial-mathematics.org)

---

<sub>Logos © Industrial Mathematics Group, licensed under [CC BY 4.0](../logos/LICENSE). Code in our repositories is licensed as stated in each repository.</sub>

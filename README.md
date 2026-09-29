<p align="center">
  <img src="assets/logoCEA.png" height="80" align="left" alt="CEA" />
  <img src="assets/logopolytech.webp" height="80" align="right" alt="Polytech Paris-Saclay" />
  <img src="assets/logoSalome.gif" height="80" alt="SALOME" />
</p>

<br />
<br />

# Apprenticeship at CEA Paris-Saclay — SALOME platform

3-year software engineering apprenticeship (2025–2028) at CEA Paris-Saclay (DES/ISAS/SGLS/LESIM), combined with an engineering degree in Computer Science and Applied Mathematics at Polytech Paris-Saclay.

I contribute to the development and modernization of [SALOME](https://www.salome-platform.org), the open-source numerical simulation platform co-developed by CEA and EDF since 2000.
My work spans the simulation workflow: mesh generation, mesh quality, post-processing, and the performance and robustness of the platform.

---

## About SALOME

SALOME is an open-source platform for numerical simulation pre- and post-processing, used in research and industry (energy, aerospace, automotive, naval). It covers CAD geometry, meshing, coupling with external solvers, and visualization of results.

| Module | Role | My work |
|---|---|---|
| **SMESH** | Meshing | MeshBooleanPlugin, PolyMeshPlugin, mesh validity |
| **PARAVIS** | Post-processing (ParaView-based) | MEDReader fixes |
| Solver coupling | CFD preprocessing | Boundary layer insertion with code_saturne |

---

## Highlights

- **2 meshing plugins** modernized in the SMESH module (Python, C++, PyQt)
- **Pull requests merged upstream** into the official SALOME repositories
- Cross-platform (Linux / Windows) process management, Python API design, automated tests
- Dedicated C++ converter (`geogram2med`) built with Geogram and MEDCoupling

| Contribution | Link |
|---|---|
| MeshBooleanPlugin — Add Cancel Button | [#22](https://github.com/SalomePlatform/meshbooleanplugin/pull/22) |
| MeshBooleanPlugin — Complete dump study on boolean operations | [#26](https://github.com/SalomePlatform/meshbooleanplugin/pull/26) |
| PolyMeshPlugin — cross-platform refactoring & Geogram pipeline | Internal CEA/EDF repository |

> Contributions to SalomePlatform were made from my work account [@Nicolas-Saikaly](https://github.com/Nicolas-Saikaly).

---

## Timeline

| Year | Period | Focus | Status |
|------|--------|-------|--------|
| [Year 1](CEA2025-2026.md) | Sep 2025 – Jun 2026 | Meshing plugins: MeshBooleanPlugin & PolyMeshPlugin | Completed |
| Year 2 | Jul 2026 – Jun 2027 | Performance, PARAVIS/MEDReader, mesh validity | In progress |
| Year 3 | Jul 2027 – Aug 2028 | — | Upcoming |

---

## Documents

| Document | Link |
|---|---|
| Year 1 — detailed contributions | [CEA2025-2026.md](CEA2025-2026.md) |
| Year 1 — apprenticeship report (FR) | [PDF](year1/report/RapportCEA_2025-2026.pdf) |
| Year 1 — defense slides (EN) | [PDF](year1/slides/SAIKALY_soutenance.pdf) |

---

## Technical stack

| | |
|---|---|
| Languages | Python, C++, CMake |
| Libraries & frameworks | PyQt, MEDCoupling, Geogram, VTK / ParaView, OpenFOAM |
| Testing & quality | unittest, Pylint |
| Tools | Git, GitHub, SAT, Linux (Ubuntu), Windows |

---

## Confidentiality

This repository contains only public-facing information.
No proprietary code or sensitive CEA data is included.

---

## Contact

Nicolas SAIKALY
[LinkedIn](https://www.linkedin.com/in/nicolas-saikaly-8a787935a) · nicolas.saikaly@cea.fr — nicolas.saikaly@universite-paris-saclay.fr

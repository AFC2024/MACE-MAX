# MACE-MAX: A Fine-Tuned Machine Learning Interatomic Potential for High-Entropy M₂AlC MAX Phases


**MACE-MAX** is a domain-specific machine learning interatomic potential obtained by fine-tuning the universal foundation model [MACE-MP-0](https://github.com/ACEsuit/mace-mp) on a compact hierarchical training set of M₂AlC MAX phase compositions. It enables high-throughput thermodynamic screening of high-entropy M₂AlC compositions across a nine-element {Ti, V, Cr, Zr, Nb, Mo, Hf, Ta, W} design space.

---

## Contents

```
MACE-MAX-v1.0/
├── README.md
├── mace_max.model                          # Trained MACE-MAX interatomic potential
└── Training_dataset_DFT_AIMD_N1_N2_N3.xyz  # DFT and AIMD reference data: 9 unary,36 binary,9 ternary equimolar M₂AlC compositions

```

---

## Model Description

| Property | Value |
|---|---|
| Base model | MACE-MP-0 |
| Element space | Ti, V, Cr, Zr, Nb, Mo, Hf, Ta, W |
| Target system | Equimolar M₂AlC MAX phases (N = 1–6 M-site elements) |
| Training compositions | N=1 (9 unary), N=2 (36 binary), N=3 (9 ternary) M₂AlC |
| Formation energy MAE (N=4–6 extrapolation) | 5.3 meV/atom |
| Computational speedup vs. DFT | ~300× |

---

## Requirements

```bash
pip install mace-torch==0.3.4
```

---

## Usage

### Command-line: `mace_eval`

```bash
mace_eval \
    --model mace_max.model \
    --configs your_structures.xyz \
    --output results.xyz \
    --device cpu
```

For GPU:

```bash
mace_eval \
    --model mace_max.model \
    --configs your_structures.xyz \
    --output results.xyz \
    --device cuda
```

### Python API

```python
from mace.calculators import MACECalculator
from ase.io import read

calc = MACECalculator(
    model_paths='mace_max.model',
    device='cpu',
    default_dtype='float64'
)

atoms = read('your_structure.xyz')
atoms.calc = calc

energy = atoms.get_potential_energy()   # eV
forces = atoms.get_forces()             # eV/Å
stress = atoms.get_stress()             # eV/Å³
```

---

## Training Dataset Format

All files use the standard **extended XYZ (extxyz)** format.

```
<N_atoms>
energy=<E_DFT> virial="..." pbc="T T T" Lattice="..."  Properties=species:S:1:pos:R:3:forces:R:3
<element>  <x>  <y>  <z>  <fx>  <fy>  <fz>
...
```

| Field | Unit | Description |
|---|---|---|
| `energy` | eV | DFT total energy |
| `virial` | eV | Virial stress tensor (9 components) |
| `forces` | eV/Å | Atomic forces |

**DFT settings:** VASP, PBE functional, PAW pseudopotentials, ENCUT = 520 eV, ISPIN = 2. AIMD snapshots from NVT trajectories at 600 K (500 steps × 1 fs).

---

## License

CC BY 4.0 — Free to use with attribution.

---

*Citation information will be added upon publication.*

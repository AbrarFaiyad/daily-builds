# Build log

Build date: 2026-11-24. The kit was built and tested on a Linux x86-64 CPU machine with Python 3.12. Environments and clones lived outside the kit folder.

## What was tried

1. **Package check.** `symmetrix` is not on PyPI, so the package was taken from the GitHub repository at tag v0.3.1, commit `664edba`. The post cites a 0.2.0 change, and the repository's latest release was tested.
2. **CPU PyTorch.** `torch==2.15.0+cpu` was installed from the PyTorch CPU index, so no CUDA libraries were pulled in.
3. **Pinned dependencies.** `mace-torch==0.3.17`, `ase==3.29.0`, `numpy==2.5.5`, `matplotlib==3.11.2`, `papermill==2.7.0` and `ipykernel==7.4.0` were installed. `requirements.txt` was built from the full `pip freeze` of that environment.
4. **Symmetrix build.** First, a CPU-only local build with Kokkos turned off (`SYMMETRIX_KOKKOS=OFF`) compiled in a few minutes. The probe comparison below ran against that build.
5. **Model probe.** A 2-layer random-weight `ScaleShiftMACE` (16 channels, l_max 1, Li and Cl) was built and saved. `extract_mace_data` produced JSON, and the Symmetrix CPU calculator matched the MACE reference (|dE| about 1.6e-11 eV, max |dF| about 4.4e-9 eV/Å) at 64 atoms.
6. **Fresh install from `requirements.txt`.** A clean venv installed the full pinned list, including Symmetrix built from the git commit with pip's default settings (Kokkos build included). This install completed successfully.
7. **Notebook runs.** The notebook was executed with `papermill demo.ipynb demo.ipynb -k python3` from inside the kit folder. The final saved run used the fresh requirements venv.

## What failed and how it was fixed

- **`torch.load` error in e3nn.** Importing `mace` failed with an `UnpicklingError` about `slice` in `e3nn/o3/_wigner.py`. Cause: PyTorch 2.6+ defaults to `weights_only=True`. Fix: set `TORCH_FORCE_NO_WEIGHTS_ONLY_LOAD=1` before importing `mace`. The cell now does this.
- **Timing helper.** The first notebook run failed with `AttributeError: 'Symmetrix' object has no attribute 'reset'`. The ASE wrapper has no `reset()`. Fix: the timing helper calls `calculate()` directly.
- **Inline figures not displayed.** `matplotlib.use("Agg")` suppressed inline display under papermill. Fix: the line was removed, and both figures now display inline.
- **Noisy first timing.** The first full run happened while the background install used all four cores. MACE took 190 ms at 64 atoms there, against 29 ms in the clean run. The saved outputs come from the clean run only.

## Notes and open items

- The Symmetrix build from git is the slowest step of installation. It was not timed precisely.
- The timing numbers are one run on one CPU and are not a benchmark.
- The Kokkos-CUDA path, the LAMMPS `pair_style`, and the 100-fold GPU claim were not tested.
- The MACE message `cuequivariance ... not available` is harmless and was left in the output.
- The Results and Gotchas text was written after the run and edited into the notebook's markdown cells directly, without re-executing. The code cells are byte-identical to the code that produced the saved outputs.

## Review note (pass 3)

Checked against PLAN.md and the task rules: executive summary is 117 words and identical in the notebook and README; the notebook has no error outputs, no `%pip`/`!pip` cells, and kernelspec `python3`; no absolute paths, user or host names in any file; every requirements.txt line is pinned (including `papermill` and `ipykernel`); the notebook is about 100 KB and output/ holds only two PNGs and a CSV; the Results numbers match the saved outputs and timing.csv; no stray caches or folders. I changed nothing in the kit apart from this note. The plan's optional MD run (4h) was not implemented, and the plan's 4h became a temp-file cleanup step. I deleted PLAN.md.

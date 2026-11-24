# Symmetrix learning kit: fast evaluation of equivariant MACE models

## Executive summary

Symmetrix is a C++ evaluator for trained equivariant MACE models. It extracts the learned weights and radial splines from a PyTorch MACE checkpoint into a JSON file, then evaluates energies and forces through an ASE calculator or LAMMPS, on CPU or with a Kokkos-CUDA GPU path. The problem it addresses is the cost of long molecular dynamics runs with equivariant potentials, where force evaluation dominates. This demo builds a small random-weight MACE model for LiCl, extracts it, and checks that Symmetrix reproduces the reference MACE energies and forces. It then times both on CPU at 64, 216 and 512 atoms. The model is random and not physical, and the post's 100-fold GPU claim is not tested here.

## Run it

`pip install -r requirements.txt` then open `demo.ipynb`

The notebook runs on CPU in well under a minute once installed, builds its own random LiCl model, and writes `output/timing.csv`, `output/timing.png` and `output/parity.png`.

## Notice

This kit is AI-generated, educational, and unreviewed. It was built by an AI assistant and has not been checked by a domain expert. Treat its results as a demonstration of the software, not as scientific findings.

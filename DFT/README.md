# Computational Chemistry - DFT

## 📁 Project Structure

```
project_name/
│
├── 01_geometries/        # Initial structures, PDB/XYZ files
├── 02_inputs/            # ORCA input files
├── 03_outputs/           # ORCA output files
├── 04_analysis/          # Post-processing, scripts, plots
│     ├── spectra_plots/
│     └── nci_visualization/
├── 05_docs/              # Notes, README, references
└── 06_scripts/           # Automation scripts for batch runs
```

## ORCA Job Execution Guide Using This Repo

This guide explains how to run ORCA jobs while keeping your `02_inputs` and `03_outputs` directories organized.

---

## 1. Navigate to the repo root

```bash
cd /path/to/project_name
```

* Always start from the **root of your repo** so relative paths work.

## 2. Running a job from inputs to outputs

### Option A: Run from repo root using relative paths

```bash
orca 02_inputs/PP_opt.inp > 03_outputs/PP_opt.out
```

* `02_inputs/PP_opt.inp` → input file path
* `03_outputs/PP_opt.out` → output file path
* This keeps all outputs in the `03_outputs` folder.

### Option B: Change directory to inputs and redirect output

```bash
cd 02_inputs
orca PP_opt.inp > ../03_outputs/PP_opt.out
cd ../
```

* Useful if you want to run several jobs from the input folder

## 3. Running jobs in the background

* To avoid terminal closing issues:

```bash
nohup orca 02_inputs/PP_opt.inp > 03_outputs/PP_opt.out 2>&1 &
```

* `2>&1` redirects error messages to the output file
* `&` runs the process in the background
* `nohup.out` will contain logs if you don’t specify output file

```bash
htop
```

### Monitoring jobs

Exit with `q` key.

* Check CPU and memory usage
* Ensure your cloud resources are sufficient

## 4. Batch submission (optional)

* You can write a small script in `06_scripts/submit_all.sh`:

```bash
#!/bin/bash
for inp in ../02_inputs/*.inp; do
    base=$(basename $inp .inp)
    orca $inp > ../03_outputs/${base}.out &
done
wait
```

* Submit all ORCA jobs in parallel and save outputs in the organized folder.

### 5. Recommended ORCA workflow

1. **Optimize geometry first:** change `SP` → `Opt` in your input.
2. **Run Single-Point energy (SP)** after optimization for accurate energy.
3. **Save optimized geometry** for later interaction energy calculations.

### 6. Tips

* Use `%pal nprocs 24` to utilize multiple cores.
* Keep consistent naming conventions for input/output files.
* Backup `.inp` files before large runs.
* For complexes, start from reasonable initial geometries to reduce SCF iterations.
* Optional: use `TightSCF` and `TightOpt` for more accurate convergence.

### 7. Analysis

* Compute interaction energies using:

```text
ΔE_int = E_complex - (E_PP + E_APP)
```

* Visualize optimized geometries in Avogadro or VMD.
* Use Python scripts in `06_scripts/` to automate energy extraction and plotting.

## 📌 References / Helpful Links

* ORCA Manual: [https://orcaforum.cec.mpg.de/](https://orcaforum.cec.mpg.de/)
* Avogadro Visualization: [https://avogadro.cc/](https://avogadro.cc/)
* NCI Plot Tutorial: [https://nciplot.sourceforge.net/](https://nciplot.sourceforge.net/)

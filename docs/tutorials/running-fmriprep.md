# Running fMRIprep on racc2

This guide explains how to run [fMRIprep](https://fmriprep.org) on the racc2 HPC cluster using the `CINN_fmriprep` Python package, which handles SLURM job submission and Singularity container management.

---

## Installation

**1. Load the Anaconda module:**

```bash
module load anaconda
```

**2. Clone the repository:**

```bash
git clone https://github.com/CINN-comp-lab/CINN_fmriprep.git
```

**3. Create and activate the conda environment:**

```bash
cd CINN_fmriprep
conda env create -f environment.yml
conda activate fmriprep
```

**4. Install the package:**

```bash
pip install -e .
```

Then open `notebooks/test.ipynb` using the `fmriprep` kernel.

---

## Usage

### Single subject

```python
from CINN_fmriprep.fmriprep import FmriPrepHandler

handler = FmriPrepHandler(
    bids_path='/storage/data/bids_dataset',
    out_path='/storage/output/fmriprep',
    work_path='/storage/work',
    slurmout_path='/home/<user>/logs/',
    subject='sub-001'
)
handler.make_slurm(output_spaces=['fsnative', 'fsaverage'])
handler.submit_slurm()
```

### Multiple subjects (parallel)

```python
from CINN_fmriprep.fmriprep import MultipleFmriPrepHandler

handler = MultipleFmriPrepHandler(
    bids_path='/storage/data/bids_dataset',
    out_path='/storage/output/fmriprep',
    work_path='/storage/work',
    slurmout_path='/home/<user>/logs/',
    subjects=['sub-001', 'sub-002', 'sub-003']
)
handler.make_fmriprep_handlers()
handler.make_slurms()
handler.submit_slurms()
```

Monitor progress via the `.out` and `.err` log files, or use the **SLURM Dashboard** extension in VSCode.

---

## Performance

A typical job (one anatomical scan + ~200 TR functional volume) takes approximately **45 minutes**. Multiple subjects run as independent parallel SLURM jobs, so 10 subjects can complete in roughly the same wall-clock time as one.

---

## Limitations

!!! warning
    All input paths must reside under `/storage`. Using `/scratch` for intermediate outputs is not currently supported.

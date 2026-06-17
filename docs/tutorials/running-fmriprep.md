# Running fMRIprep on racc2

[fMRIPrep](https://fmriprep.org/en/stable/) is a robust and flexible tool for fMRI preprocessing which draws on FSL, AFNI, and FreeSurfer to create a best-in-class pipeline. You can read the original validation paper [here](https://www.nature.com/articles/s41592-018-0235-4).

Using fMRIPrep on racc2 can save days of processing time, both due to the available compute resources and the SLURM job scheduler, which allows multiple fMRIprep jobs to run in parallel.

This guide covers running fMRIprep via the [`CINN_fmriprep`](https://github.com/CINN-comp-lab/CINN_fmriprep) Python package, which ties together the [SLURM Workload Manager](https://slurm.schedmd.com/documentation.html) and a [Singularity](https://docs.sylabs.io/guides/3.5/user-guide/introduction.html) container prepared by the [Neurodesk team](https://www.neurodesk.org/developers/architecture/transparent_singularity/).

!!! tip "Connecting to racc2"
    If you haven't connected to racc2 before, see the [SSH and VSCode setup guide](connecting-to-racc2.md) first.

---

## 1. Install the package { #installing-the-package }

In a terminal on racc2, navigate to a folder where you have write permissions and load Anaconda:

```bash
module load anaconda
```

Download the package:

```bash
git clone https://github.com/CINN-comp-lab/CINN_fmriprep.git
```

Create the conda environment from the included `environment.yml`:

```bash
cd CINN_fmriprep
conda env create -f environment.yml
```

<!-- VIDEO PLACEHOLDER: part1.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Installing the conda environment"
  frameborder="0"
  allowfullscreen>
</iframe>

This may take a while. Once complete, activate the environment and install the package:

```bash
conda activate fmriprep
pip install -e .
```

You can now navigate to the `notebooks/` subfolder and open `test.ipynb` using the `fmriprep` kernel. This works with VSCode, JupyterLab, or any Jupyter-compatible interface.

<!-- VIDEO PLACEHOLDER: part2.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Opening the notebook with the fmriprep kernel"
  frameborder="0"
  allowfullscreen>
</iframe>

---

## 2. Example usage

The [`test.ipynb`](https://github.com/CINN-comp-lab/CINN_fmriprep/blob/main/notebooks/test.ipynb) notebook contains annotated examples. The paths in the notebook point to personal storage and will need to be changed to match your own data locations.

### Activate fmriprep paths

This cell adds the fmriprep executable to your `PATH` environment variable:

```python
from CINN_fmriprep.fmriprep import activate_fmriprep

activate_fmriprep()
```

You can verify this worked by inspecting your `.bashrc`:

```bash
nano ~/.bashrc
```

<!-- VIDEO PLACEHOLDER: part3.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Activating fmriprep and checking .bashrc"
  frameborder="0"
  allowfullscreen>
</iframe>

### Submit a job and monitor progress

Run through the subsequent notebook cells, which show how to configure and submit an fMRIprep job. Once submitted, you can monitor progress via the `.out` and `.err` log files. If you are using VSCode, the **SLURM Dashboard** plugin provides a convenient interface for monitoring or cancelling jobs.

<!-- VIDEO PLACEHOLDER: part4.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Submitting a job and monitoring with SLURM Dashboard"
  frameborder="0"
  allowfullscreen>
</iframe>

### Multiple subjects in parallel

The final section of the notebook demonstrates submitting fMRIprep jobs for multiple subjects simultaneously. In principle you could submit 100 jobs for 100 subjects in parallel; they will run as cluster resources become available.

<!-- VIDEO PLACEHOLDER: part5.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Submitting multiple subjects in parallel"
  frameborder="0"
  allowfullscreen>
</iframe>

---

## 3. How long will a job take?

A subject with one 1mm anatomical scan and a functional volume of ~200 TRs at 2mm resolution takes approximately **45 minutes**. This scales with the size and sampling density of your data.

Because subjects run as independent parallel jobs, 10 subjects can complete in roughly the same wall-clock time as one. Further benchmarking information is available [here](https://neurostars.org/t/fmriprep-multi-threading-benchmark-results/17622) and [here](https://fmriprep.org/en/stable/faq.html#how-much-cpu-time-and-ram-should-i-allocate-for-a-typical-fmriprep-run).

---

## 4. Limitations

!!! warning
    Every path supplied to `FmriPrepHandler` must reside under `/storage`. Using `/scratch` for intermediate outputs (e.g. `work_dir`) is not currently supported — the cause of this limitation is not yet known.

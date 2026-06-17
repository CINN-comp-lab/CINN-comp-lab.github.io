# Running MRIQC on racc2

[MRIQC](https://mriqc.readthedocs.io) provides automated, standardised, and reproducible quality metrics for both functional and structural MRI data. By evaluating signal-to-noise ratio, motion artefacts, spatial resolution, and other properties, MRIQC helps ensure data quality before committing to a full preprocessing pipeline. You can read the original validation paper [here](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0184661).

This guide covers running MRIQC on racc2 using the same [`CINN_fmriprep`](https://github.com/CINN-comp-lab/CINN_fmriprep) package used for fMRIprep.

---

## 1. Install the package

If you have not already done so, follow the [installation steps in the fMRIprep guide](running-fmriprep.md#installing-the-package). The same package and conda environment are used for both tools.

---

## 2. Example usage

The [`test_mriqc.ipynb`](https://github.com/CINN-comp-lab/CINN_fmriprep/blob/main/notebooks/test_mriqc.ipynb) notebook contains annotated examples. The paths in the notebook point to personal storage and will need to be changed to match your own data locations.

### Activate MRIQC paths

This cell adds the MRIQC executable to your `PATH` environment variable:

```python
from CINN_fmriprep.fmriprep import activate_mriqc

activate_mriqc()
```

Verify this worked by inspecting your `.bashrc`:

```bash
nano ~/.bashrc
```

<!-- VIDEO PLACEHOLDER: mriqc_1.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Activating MRIQC and checking .bashrc"
  frameborder="0"
  allowfullscreen>
</iframe>

### Submit a job and monitor progress

Run through the subsequent cells to configure and submit an MRIQC job. Monitor progress via the `.out` and `.err` log files, or use the **SLURM Dashboard** plugin in VSCode.

<!-- VIDEO PLACEHOLDER: mriqc_2.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Submitting an MRIQC job and monitoring progress"
  frameborder="0"
  allowfullscreen>
</iframe>

### View HTML reports

Once a job has finished, you can view the HTML quality reports by starting a simple HTTP server in the output directory:

```bash
python3 -m http.server {port_number}
```

Replace `{port_number}` with a 4-digit number of your choice (e.g. `8080`). VSCode will detect the running server and prompt you to open it in your local browser.

<!-- VIDEO PLACEHOLDER: mriqc_3.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Viewing MRIQC HTML reports"
  frameborder="0"
  allowfullscreen>
</iframe>

### Multiple subjects in parallel

The notebook also demonstrates submitting MRIQC jobs for multiple subjects simultaneously. In principle you could submit 100 jobs for 100 subjects at once; they will run as cluster resources become available.

<!-- VIDEO PLACEHOLDER: mriqc_4.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Submitting multiple MRIQC jobs in parallel"
  frameborder="0"
  allowfullscreen>
</iframe>

### Email notifications

You can receive email updates when a job starts, finishes, or fails. Add the following line to the `mriqc_slurm` section of `config.yml` (replacing the email address with your own):

```yaml
mail_user: 'n.hedger@reading.ac.uk'
```

The `mail_type` is set to `ALL` by default, covering all job state changes. See the [SLURM documentation](https://slurm.schedmd.com/sbatch.html#OPT_mail-type) for other options.

!!! warning
    Email notifications from SLURM on racc2 can be unreliable in practice.

<!-- VIDEO PLACEHOLDER: mriqc_5.mp4 — replace VIDEO_ID with your YouTube video ID -->
<iframe
  width="100%"
  height="400"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Configuring email notifications"
  frameborder="0"
  allowfullscreen>
</iframe>

---

## 3. How long will a job take?

A subject with one 1mm anatomical scan and a functional volume of ~200 TRs at 2mm resolution takes approximately **5 minutes**. Processing time scales linearly with data complexity.

Because subjects run as independent parallel jobs, 10 subjects can complete in roughly the same wall-clock time as one. Further benchmarking information is available [in the MRIQC documentation](https://mriqc.readthedocs.io/en/stable/running.html#running-mriqc-on-hpc-clusters).

---

## 4. Limitations

!!! warning
    Every path supplied to `MriqcHandler` must reside under `/storage`. Using `/scratch` for intermediate outputs is not currently supported — the cause of this limitation is not yet known.

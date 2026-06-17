# Running MRIQC on racc2

[MRIQC](https://mriqc.readthedocs.io) provides automated, standardised, and reproducible quality metrics for MRI data. This guide covers running it on racc2 using the `CINN_fmriprep` package.

!!! note "Prerequisites"
    Complete the [installation steps](running-fmriprep.md#installation) from the fMRIprep guide first.

---

## Usage

### Single subject

```python
from CINN_fmriprep.fmriprep import MriqcHandler

handler = MriqcHandler(
    bids_path='/storage/data/bids_dataset',
    out_path='/storage/output/mriqc',
    work_path='/storage/work',
    slurmout_path='/home/<user>/logs/',
    subject='sub-001'
)
handler.make_slurm()
handler.submit_slurm()
```

### Multiple subjects (parallel)

```python
from CINN_fmriprep.fmriprep import MultipleMriqcHandler

handler = MultipleMriqcHandler(
    bids_path='/storage/data/bids_dataset',
    out_path='/storage/output/mriqc',
    work_path='/storage/work',
    slurmout_path='/home/<user>/logs/',
    subjects=['sub-001', 'sub-002', 'sub-003']
)
handler.make_mriqc_handlers()
handler.make_slurms()
handler.submit_slurms()
```

Up to 100 jobs can be queued simultaneously; they will run as cluster resources become available.

### Viewing results

Start an HTTP server in the output directory and open the URL in your local browser:

```bash
cd /storage/output/mriqc
python -m http.server 8080
```

### Email notifications

Add your email to the `config.yml` file under `slurm.mail_user`. Note that delivery reliability varies on the cluster.

---

## Performance

A typical job (one anatomical + ~200 TR functional) takes approximately **5 minutes**. Processing time scales linearly with data complexity.

---

## Limitations

!!! warning
    All input paths must reside under `/storage`. Using `/scratch` for intermediate outputs is not currently supported.

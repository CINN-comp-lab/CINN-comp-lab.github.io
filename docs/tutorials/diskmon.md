# Monitoring disk usage with CINN_diskmon

[CINN_diskmon](https://github.com/CINN-comp-lab/CINN_diskmon) provides an interactive browser-based dashboard for monitoring storage usage on the racc2 HPC cluster. It tracks how much space a given folder is consuming, breaks down usage by subfolder and file owner, identifies the largest and oldest files, and shows how usage has changed over time.

---

## Installation

Load Anaconda, then create a dedicated conda environment. If your home directory quota is limited, use `--prefix` to install the environment onto `/storage` instead:

```bash
module load anaconda
conda env create -f environment.yml --prefix /storage/path/of/your/choice/diskmon
conda activate /storage/path/of/your/choice/diskmon
pip install -e .
```

---

## Configuration

Before running, edit `config/config.yml` to set your self-imposed folder limits and dashboard port:

```yaml
quotas:
  warn_threshold: 0.80          # warn when a folder exceeds 80% of its limit
  folder_limits:
    /storage/research/cinn/comp_admin: "2 TB"   # add one entry per folder you monitor

dashboard:
  port: 4131
  top_n_files: 50
  du_max_depth: 2
```

Paths to monitor are **not** set in the config — they are entered directly in the dashboard sidebar at runtime. Any path you scan is remembered and appears in the dropdown on future runs. Folder limits defined here are looked up automatically when a snapshot is collected.

---

## Running the dashboard

```bash
cinn-diskmon
```

VSCode will detect the port and offer to open the dashboard in your local browser. The same port-forwarding mechanism used to view MRIQC HTML reports works here.

The dashboard opens empty. Type a path in the sidebar and click **Collect snapshot now** to take a first reading. Scanning a large directory may take a minute or two.

---

## Dashboard panels

Once a snapshot has been collected the dashboard shows:

**Summary strip**

Four quick-read metrics at the top: folder size, self-imposed limit, oldest file date, and the largest single file found.

**Storage usage gauge**

A speedometer-style gauge showing percentage of the folder limit consumed, with a red warning zone beyond the configured threshold.

**Folder breakdown**

A treemap and a ranked horizontal bar chart showing which subfolders are consuming the most space.

**Usage over time**

A line chart of folder size across all logged snapshots. The y-axis auto-scales to the data range so even small fluctuations are visible. This becomes more useful the more often you collect snapshots.

**File insights**

- **File age distribution** — histogram of modification dates, showing how much of the data is recent versus untouched for years
- **Storage per user** — total space consumed per file owner, useful for identifying who is contributing most to usage
- **Size vs age scatter** — each dot is a file; files in the top-left quadrant (large and old) are the strongest candidates for archiving or deletion

**File explorer**

Sortable tables of the largest and oldest files, including file owner and last modified date.

---

## Collecting snapshots over time

Usage history is only useful if snapshots are collected regularly. The simplest approach is to run the dashboard periodically and click **Collect snapshot now**, but you can also collect a snapshot from a notebook or script without opening the dashboard:

```python
from CINN_diskmon import DiskCollector, DiskLogger, load_pkg_yaml

config = load_pkg_yaml()
collector = DiskCollector(config)
logger = DiskLogger(config['paths']['db_path'])

snapshot = collector.collect_snapshot('/storage/research/cinn/comp_admin')
logger.log(snapshot)
```

---

## Clearing history

```python
from CINN_diskmon import DiskLogger, load_pkg_yaml

config = load_pkg_yaml()
logger = DiskLogger(config['paths']['db_path'])

logger.clear()                                    # clear all history
logger.clear('/storage/research/cinn/comp_admin') # clear one path only
```

Or delete the database file directly:

```bash
rm data/history.db
```

It is recreated automatically on the next snapshot.

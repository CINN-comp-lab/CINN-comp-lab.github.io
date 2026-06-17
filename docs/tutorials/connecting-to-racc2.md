# Connecting to racc2 via SSH and VSCode (Mac users)

This guide walks Mac users through connecting to the racc2 HPC cluster without a VPN using SSH keys and VSCode.

---

## Part 1: Generate an SSH key

These steps assume `~/.ssh/` directories exist both locally and on the remote system.

**1. Generate the key:**

```bash
ssh-keygen
```

Accept the default file location and set a passphrase you will remember.

**2. Create the `authorized_keys` file:**

```bash
cat ~/.ssh/id_rsa.pub >> authorized_keys
```

**3. Transfer your key to the remote system:**

```bash
scp ~/.ssh/authorized_keys <username>@mft.act.reading.ac.uk:/<username>/.ssh/
```

---

## Part 2: Connect to racc2

**Basic connection via the arc-ssh gateway:**

```bash
ssh <login>@arc-ssh.reading.ac.uk -X
```

To disconnect: `exit`

**Jump directly to racc2:**

```bash
ssh -J <login>@arc-ssh.reading.ac.uk <login>@racc.rdg.ac.uk -Y
```

You will be prompted for your UoR password once and your SSH passphrase twice.

---

## Part 3: Configure VSCode

**Required extensions:**

- Remote - SSH
- Python, Python Debugger, Jupyter, Pylance (for Python/notebook work)

**Setup steps:**

1. In VSCode settings, increase the Remote-SSH connection timeout to at least **30 seconds**
2. Add a new SSH host using the jump command above
3. Open the Remote Explorer panel and connect — refresh the panel if the host does not appear immediately

---

## Part 4: Jupyter notebooks in VSCode

Create new Jupyter notebooks directly in VSCode once connected. Kernels are automatically detected from your conda environments. If a kernel does not appear, creating a new environment through VSCode's Python extension interface can resolve detection issues.

# SHIFT-SNARE: one-POI artifact

This artifact locates one AP1 leakage sample in Falcon's discrete Gaussian sampler, fits two univariate Gaussian models, and predicts whether one unknown measurement corresponds to intermediate 0 or −1. AP2/sign recovery and the complete 54-POI pipeline are outside its scope.

## Setup with Python and pip

Use standard **Python 3.10 or newer** with pip and the standard `venv` module. Python 3.12 is a suitable baseline. Put `requirements.txt`, this README, and the notebook in the same project folder. Open a terminal in that folder. Create a fresh `.venv` for this project rather than reusing an environment with the import error.

### Windows: PowerShell or Command Prompt

```text
python --version
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
 
.\.venv\Scripts\python.exe -m ipykernel install --user --name shift-snare --display-name "Python (SHIFT-SNARE)"
.\.venv\Scripts\python.exe -m jupyterlab
```

If Windows exposes Python as `py` rather than `python`, use `py` for the first two commands. The remaining commands stay the same.

### macOS or Linux

```bash
python3 --version
python3 -m venv .venv
.venv/bin/python -m pip install --upgrade pip
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m pip check
.venv/bin/python -m ipykernel install --user --name shift-snare --display-name "Python (SHIFT-SNARE)"
.venv/bin/python -m jupyterlab
```

Open the notebook and select **Python (SHIFT-SNARE)** as its kernel. In VS Code, choose the same kernel or the Python executable inside this project's `.venv`. Restart the kernel after switching, then run the environment-check cell before the analysis. That cell prints the executable and package versions and tests the imports. The executable path should point inside this project's `.venv`.

The direct dependencies are listed in `requirements.txt`. Pip selects compatible versions for the running Python. The ranges are not a complete version lock; record the installed versions with `python -m pip freeze` using the environment's Python when archiving a specific experiment.

## If NumPy/Matplotlib imports fail

`ImportError: numpy.core.multiarray failed to import` can indicate an incompatible compiled package or a damaged/mixed installation. Follow the fresh-environment setup above, then confirm the notebook uses that environment. Installing into one Python while running another kernel will not fix it. Do not install or downgrade packages in a running analysis kernel; stop it, install using the environment's Python, and restart. If the new environment still fails, retain the full traceback, printed Python path, and package versions for diagnosis.

## Prepare the CSV inputs

Each row must be one complete trace; columns are sample positions. Use numeric comma-separated values with no header, index column, or blank lines. Files with one trace per column need a different extraction method.

Edit the two source paths in `keep_first_100.py`, then run it from the project folder:

```text
# Windows
.\.venv\Scripts\python.exe keep_first_100.py
```

```bash
# macOS/Linux
.venv/bin/python keep_first_100.py
```

The script copies the first 100 lines of each source to its `first100` subfolder without changing the values or original files. It stops after 100 lines and holds only one line at a time. Rerunning replaces the smaller output files. It also works with ordinary Python without installing the notebook dependencies.

Default notebook paths:

| Input | Path |
|---|---|
| Profiling intermediate 0 | `data/first100/first5_secret_0.csv` |
| Profiling intermediate −1 | `data/first100/first5_secret_-1.csv` |
| Separate unknown trace | `data/unknown.csv` |

Update the configuration cell for your filenames. Profiling filenames must end in `_0.csv` or `_-1.csv`; these suffixes generate the labels. The unknown filename supplies no label. Converted CSV values are preserved with `sample_scale = 1.0`.

## Run the analysis

The notebook loads 50 traces per class by default; set `traces_per_class = 100` to use all 100 per class. It prints the loaded dimensions, plots the first trace of each file, generates pseudo-Hamming-weight labels 0/64, selects one sample by maximum absolute Pearson correlation, fits the two Gaussian models, and classifies the unknown trace. It reads only the requested rows and performs no TRS conversion.

Use an independent unknown trace with matching alignment, sample window, and scaling. A prediction estimates one AP1 intermediate, not the full coefficient or key. Synthetic tests check software behavior; experimental accuracy requires your real captures.

## Setup references

- [Python virtual environments](https://docs.python.org/3/library/venv.html)
- [Registering a Jupyter kernel](https://ipython.readthedocs.io/en/stable/install/kernel_install.html)
- [NumPy import troubleshooting](https://numpy.org/doc/stable/user/troubleshooting-importerror.html)

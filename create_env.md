# Python Virtual Environment Setup Methods

This guide lists several popular ways to create and manage Python virtual environments.

---

## Method 1: `uv` (Modern, Fast, Recommended)

Install Python:

```bash
uv python install 3.10
uv python update-shell
```

Create a project and virtual environment:

```bash
cd <project-folder>
uv init
uv venv djenv --python 3.10
```

Activate the environment (Windows):

```bash
djenv\Scripts\activate
```

---

## Method 2: `python -m venv` (Built-in, Classic)

Create a virtual environment:

```bash
python -m venv autogen-crash-course
```

### Activate

**Windows**

```bash
autogen-crash-course\Scripts\activate
```

**macOS / Linux**

```bash
source autogen-crash-course/bin/activate
```

---

## Method 3: `conda` (Popular for Data Science)

Create a Conda environment:

```bash
conda create -n myenv python=3.10
```

Activate it:

```bash
conda activate myenv
```

---

## Method 4: `virtualenv` (Older but Still Common)

Install `virtualenv`:

```bash
pip install virtualenv
```

Create an environment:

```bash
virtualenv myenv
```

### Activate

**Windows**

```bash
myenv\Scripts\activate
```

**macOS / Linux**

```bash
source myenv/bin/activate
```

---

## Method 5: `pyenv` + `venv` (Popular on macOS/Linux)

Install and select a Python version:

```bash
pyenv install 3.10.13
pyenv local 3.10.13
```

Create and activate a virtual environment:

```bash
python -m venv myenv
source myenv/bin/activate
```

---

## Method 6: `Poetry` (Dependency Management + Virtual Environments)

Install Poetry:

```bash
pip install poetry
```

Initialize a project and create an environment:

```bash
cd <project-folder>
poetry init
poetry env use python3.10
poetry shell
```

---

## Quick Comparison

| Method | Best For | Cross-Platform |
|---------|----------|----------------|
| **uv** | Fast project setup, modern workflows | ✅ |
| **venv** | Standard Python projects | ✅ |
| **conda** | Data science, ML, AI | ✅ |
| **virtualenv** | Legacy projects | ✅ |
| **pyenv + venv** | Managing multiple Python versions | macOS/Linux ⭐ |
| **Poetry** | Dependency & package management | ✅ |

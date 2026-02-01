METHOD 1: uv (modern, fast, recommended)
---------------------------------------
uv python install 3.10
uv python update-shell

cd <project-folder>
uv init
uv venv djenv --python 3.10
djenv\Scripts\activate


METHOD 2: python -m venv (built-in, classic)
--------------------------------------------
python -m venv autogen-crash-course

# Windows
 source autogen-crash-course\Scripts\activate

# macOS / Linux
source autogen-crash-course/bin/activate


METHOD 3: conda (very famous, data-science heavy)
-------------------------------------------------
conda create -n myenv python=3.10
conda activate myenv


METHOD 4: virtualenv (older but still common)
----------------------------------------------
pip install virtualenv
virtualenv myenv

# Windows
myenv\Scripts\activate

# macOS / Linux
source myenv/bin/activate


METHOD 5: pyenv + venv (popular on macOS/Linux)
-----------------------------------------------
pyenv install 3.10.13
pyenv local 3.10.13

python -m venv myenv
source myenv/bin/activate


METHOD 6: poetry (famous for dependency management)
---------------------------------------------------
pip install poetry

cd <project-folder>
poetry init
poetry env use python3.10
poetry shell

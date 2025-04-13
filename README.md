# TensorRT-LLM Installation Without Docker or Root Privileges

This guide provides instructions for installing and setting up NVIDIA's TensorRT-LLM library without using Docker and without requiring root privileges. Tested Environment :

- NVIDIA A100 GPU
- CUDA 12.3

## Installation Steps

### 1. Create a Conda Environment

```bash
# Create a new conda environment with Python 3.10 (installation with 3.11 was tested but failed)
conda create -n tensorrt_llm_new python=3.10 -y

# Activate the environment
conda activate tensorrt_llm_new
```

### 2. Install OpenMPI

```bash
# Add conda-forge channel and set channel priority
conda config --add channels conda-forge
conda config --set channel_priority strict

# Install OpenMPI and related packages
conda install openmpi openmpi-mpicc openmpi-mpicxx openmpi-mpifort
```

### 3. Install TensorRT-LLM

```bash
# Install TensorRT-LLM from NVIDIA's package index
pip3 install tensorrt_llm --extra-index-url https://pypi.nvidia.com
```

## Troubleshooting

### Python Shared Library Issue

If you encounter an error similar to:
```
ImportError: libpython3.10.so.1.0: cannot open shared object file: No such file or directory
```

Add Conda's library path to the LD_LIBRARY_PATH:
```bash
export LD_LIBRARY_PATH=$CONDA_PREFIX/lib:$LD_LIBRARY_PATH
```

For persistence, add this line to your `~/.bashrc` file.

### MPI4Py Symbol Issues

If you encounter an error similar to:
```
ImportError: ...mpi4py/MPI.cpython-310-x86_64-linux-gnu.so: undefined symbol: MPI_Aint_add
```

This implies that mpi4py was compiled against a different MPI version than the OpenMPI you installed via Conda. The mpi4py package needs to be compiled against the same OpenMPI installation. Fix it by:
```bash
# Uninstall the pip version of mpi4py
pip uninstall mpi4py -y

# Install mpi4py using conda to ensure it uses the same MPI implementation
conda install -c conda-forge mpi4py
```

## Testing the Installation

Sanity check the installation by running the following test code obtained from the official documentation (https://nvidia.github.io/TensorRT-LLM/installation/linux.html):
```bash
mpiexec -n 2 --oversubscribe python3 /path/to/your/test_script.py
```
Replace `/path/to/your/test_script.py` with the actual path to your test script.

## References

- [Official TensorRT-LLM Documentation](https://nvidia.github.io/TensorRT-LLM/)
- [NVIDIA TensorRT-LLM GitHub Repository](https://github.com/NVIDIA/TensorRT-LLM)

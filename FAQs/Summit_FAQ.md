# Summit FAQ
Frequently asked questions related to the Summit high-performance computing (HPC) system of the Oak Ridge National Laboratory (ORNL).

- How do I set up my GitHub repository (i.e. working codebase) on Summit?
  - If you will be working on a BML project (mostly) by yourself, you will want to keep all of your project files and data in the ["Member Work" partition](https://docs.olcf.ornl.gov/data/project_centric.html) of Summit. You can access this partition by executing the command "cd $MEMBERWORK" from your home directory (i.e. the one at which you initially land when you log into Summit).
  - From there, you will be greeted with a directory for all the ORNL projects for which you applied (and were approved by ORNL staff). Once you "cd" into the directory of the ORNL project on which you are currently working, you can create your BML project directory structure(s) as you see fit.


- How do I create Conda environments on Summit?
  - See [this starter GitHub repository](https://github.com/amorehead/deep-learning-hpc-project-template) for an example of how you should create and maintain your BML projects' Conda environments on Summit.


- How do I install the Deep Graph Library (DGL) on Summit?
  - You will have to compile it from source, I'm afraid. Fortunately, this work has (largely) already been done for you and tested with version 0.6 of DGL. The steps to install DGL from source and install a Python binary into your currently-activated Conda environment are as follows.
  - Using one of Summit's login nodes, in your ORNL project's $MEMBERWORK directory and initially with no Conda/venv environments currently activated:
  ```
  # Load relevant modules for compiling DGL
  module load cmake/3.18.2
  module load gcc/6.4.0
  module load cuda/10.2.89
  
  # Clone either a specific version of DGL or instead the latest version, respectively
  git clone --depth 1 --branch 0.6.0 https://github.com/dmlc/dgl.git  # For installing DGL 0.6.0 - currently compiling successfully on HPC clusters like Summit
  git clone https://github.com/dmlc/dgl.git  # For installing the latest version of DGL
  
  # Populate the directory structure for the locally-cloned DGL repository
  cd dgl/
  git submodule update --init --recursive
  mkdir build
  cd build/
  
  # Compile and make DGL from source
  cmake -DUSE_AVX=OFF -DUSE_CUDA=ON -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ ..
  vim third_party/METIS/libmetis/CMakeFiles/metis.dir/flags.make  # To change '-march=native' to '-mcpu=native', followed by writing changes to storage and exiting file
  make -j 4
  
  # Install Python bindings in a Conda environment
  cd ../python
  conda activate $MY_PROJECT_DIR/venv  # Can use this to activate and install DGL Python bindings in any Conda environment under $MEMBERWORK
  python setup.py install  # Finish the installation of DGL's pip package in your currently-activated Conda environment
  ```
  - [This](https://github.com/dmlc/dgl/issues/2661) is a link to the original GitHub issue that spawned all of these instructions.
  - If you encountered no errors after executing all of the preceding commands (in succession), your installation of DGL should be complete! Now, you'll need to test the library with your own dataset and learning algorithms. Best of luck!


- How do I change where DGL stores its configuration?
  - Since DGL, by default, stores its configuration files on a read-only partition, you will need to change the location of this directory. [This](https://github.com/dmlc/dgl/issues/2697) is a link to the GitHub issue that explains this issue and its solution in more detail.

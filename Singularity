Bootstrap: docker
From: tensorflow/tensorflow:1.10.1-gpu-py3

%environment
  # use bash as default shell
  SHELL=/bin/bash
  export SHELL
  
  # add CUDA paths
  CPATH="/usr/local/cuda/include:$CPATH"
  PATH="/usr/local/cuda/bin:$PATH"
  LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
  CUDA_HOME="/usr/local/cuda"
  export CPATH PATH LD_LIBRARY_PATH CUDA_HOME
  
  # make conda accessible
  PATH=/opt/conda/envs/pytorch-py3.6/bin:$PATH
  export PATH

  # make soplex and scipopt accessible
  PATH="$HOME/code/scip":$PATH
  export PATH

%setup
  # runs on host - the path to the image is $SINGULARITY_ROOTFS

%files
  ./scipopt/scip-6.0.1.tgz
  ./scipopt/soplex-4.0.1.tgz

%post
  # post-setup script

  # load environment variables
  . /environment

  # use bash as default shell
  echo 'SHELL=/bin/bash' >> /environment

  # make environment file executable
  chmod +x /environment

  # default mount paths, files
  mkdir /scratch /data /work-zfs
  # add the nvidia system management interface path to image
  touch /usr/bin/nvidia-smi

  # run scipopt and soplex installation
  # TBD

  # additional packages
  apt-get update
  apt-get install -y python-tk
  apt-get install -y libsm6 libxext6
  apt-get install cmake
  apt-get install gcc
  apt-get install python3.6
  alias python=python3

  # install pip install
  pip install cython
  pip install numpy opencv scikit-learn scikit-image scipy pandas joblib
  pip install pytest flake8
  pip install tqdm natsort protobuf onnx spectrum
  pip install tensorboardx tensorboard
  pip install git+https://github.com/jma127/pyltr@78fa0ebfef67d6594b8415aa5c6136e30a5e3395
  pip install git+https://github.com/ds4dm/PySCIPOpt.git@ml-branching

  # user requests (contact marcc-help@marcc.jhu.edu)
  # /opt/conda/bin/conda config --add channels conda-forge
  # /opt/conda/bin/conda install numpy opencv scikit-learn scikit-image scipy pandas
  # /opt/conda/bin/conda install -c pytest flake8 tensorboard
  # /opt/conda/bin/conda install -c tensorboardx tqdm protobuf onnx spectrum nibabel

%runscript
  # executes with the singularity run command
  # delete this section to use existing docker ENTRYPOINT command

%test
  # test that script is a success
  cmake --version
  python --version
  pip --version

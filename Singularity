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

  #
  export SCIPOPTDIR='/opt/scip'

%setup
  # runs on host - the path to the image is $SINGULARITY_ROOTFS
  export SCIPOPTDIR='/opt/scip'

%files
  # ./scipopt/scip-6.0.1.tgz /opt
  #./scipopt/soplex-4.0.1.tgz /opt
  #./scipopt/vanillafullstrong.patch /opt

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

  # create scip output dir
  mkdir /opt/scip/

  # add base properties from ubuntu
  apt-get -y update && \
    apt-get install -y software-properties-common && \
    rm -rf /var/lib/apt/lists/
  # add package for python3.6+
  add-apt-repository ppa:deadsnakes/ppa
  apt-get -y update
  apt-get -y upgrade
  # install utilities
  apt-get install -y apt-transport-https ca-certificates gnupg \
                         software-properties-common
  # additional packages
  apt-get -y update
  apt-get -y upgrade
  apt-get install -y git
  apt-get install -y apt-utils
  apt-get install -y curl
  apt-get install -y python-tk
  apt-get install -y libsm6 libxext6
  apt-get install -y cmake
  apt-get install -y gcc
  apt-get install -y python3.6
  apt-get install -y python3.6-dev
  apt-get update && apt-get install -y \
            gcc libprotobuf-dev protobuf-compiler \
            luarocks git vim

  # try to get python3.6 to be our python
  curl https://bootstrap.pypa.io/get-pip.py | python3.6 - --user
  rm /usr/bin/python3
  ln -s /usr/bin/python3.5 /usr/bin/python3
  ln -s /usr/bin/python3.6 /usr/local/bin/python
  alias python3='/usr/bin/python3.6'
  alias python=python3
  python --version
  python3 --version
  python3.6 -m pip -V

  # enable latest version of cmake
  # see: https://stackoverflow.com/questions/49859457/how-to-reinstall-the-latest-cmake-version
  apt-get install -y wget
  wget -qO - https://apt.kitware.com/keys/kitware-archive-latest.asc |
    apt-key add -
  # apt-add-repository 'deb https://apt.kitware.com/ubuntu/ bionic main'
  add-apt-repository 'deb https://apt.kitware.com/ubuntu/ xenial main'
  apt-get -y update

  # try to install latest version of cmake
  apt-get install -y cmake
  cmake --version

  # debug check
  cmake --version
  apt-get update
  apt-get upgrade
  cmake --version

  git clone https://github.com/adam2392/deeplearning_hubs.git
  cd deeplearning_hubs
  git checkout sciptflow
  cp ./scipopt/scip-6.0.1.tgz /opt
  cp ./scipopt/soplex-4.0.1.tgz /opt
  cp ./scipopt/vanillafullstrong.patch /opt

  # run scipopt and soplex installation
  export SCIPOPTDIR='/opt/scip'
  cd /opt/
  tar -xzf soplex-4.0.1.tgz

  cd soplex-4.0.1/
  mkdir build
  ls
  pwd
  ls /opt/
  cmake -S . -B build -DCMAKE_INSTALL_PREFIX=$SCIPOPTDIR
  make -C ./build -j 4
  make -C ./build install
  cd ..

  tar -xzf scip-6.0.1.tgz
  cd scip-6.0.1/

  # link patches to scip
  patch -p1 < ../vanillafullstrong.patch

  mkdir build
  cmake -S . -B build -DSOPLEX_DIR=$SCIPOPTDIR -DCMAKE_INSTALL_PREFIX=$SCIPOPTDIR
  make -C ./build -j 4
  make -C ./build install
  cd ..

  # install pip install
  python3.6 -m pip install --upgrade pip
  python3.6 -m pip install cython
  python3.6 -m pip install numpy scikit-learn scikit-image scipy pandas joblib opencv-python
  python3.6 -m pip install graspy
  python3.6 -m pip install pytest flake8
  python3.6 -m pip install tqdm natsort protobuf onnx spectrum
  python3.6 -m pip install tensorflow-gpu==1.10.1
  python3.6 -m pip install tensorboardx tensorboard
  python3.6 -m pip install git+https://github.com/jma127/pyltr@78fa0ebfef67d6594b8415aa5c6136e30a5e3395
  python3.6 -m pip install git+https://github.com/ds4dm/PySCIPOpt.git@ml-branching

%runscript
  # executes with the singularity run command
  # delete this section to use existing docker ENTRYPOINT command

%test
  # test that script is a success
  cmake --version
  python --version
  pip --version

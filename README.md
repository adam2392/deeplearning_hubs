# deeplearning_hubs

Singularity images to support a bare-bones minimum image for creating Singularity images for HPC development.
This repo specifically has been engineered for use w/ MARCC at JHU.

Supports:
- pytorch with cuda
- tensorflow with cuda
- keras

Additional Package List:
- anaconda
- numpy
- scipy
- scikit-learn
- opencv
- pandas
- pytest
- flake8
- tensorboard
- tensorboardx
- tqdm
- protobuf
- onnx
- spectrum
- nibabel
- mne

# Adding a "New" Singularity Image

Singularity images are built and indexed on https://singularity-hub.org/. To add a new build 
one should create a branch of this repo, and then activate the branch in your 'Collections' 
on Singularity-Hub. Each branch has their own 'Singularity' image, which the Hub looks for and 
builds. Then the uri as 
    
    shub://<github_user>/deeplearning_hubs:<branch_name> 
    # (e.g. shub://adam2392/deeplearning_hubs:pytorch) 
    
can be used to build the corresponding Singularity image on that page. 
This can then be pulled from shub. For example:

    singularity pull --name tensorflow.simg shub://marcc-hpc/tensorflow
    
Then you can run scripts from this singularity container:

    # redefine SINGULARITY_HOME to mount current working directory to base $HOME
    export SINGULARITY_HOME=$PWD:/home/$USER
    # run signularity image w/ python script
    singularity exec --nv ./tensorflow.simg python softmax_regression.py
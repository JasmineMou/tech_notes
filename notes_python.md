# conda

conda --version
conda update conda
conda info --envs
conda create --name py36 python=3.6
conda create --name py34 python=3.4
conda create --name py27 python=2.7
conda info --envs
	base                  *  /Users/Jasmine.Mou@ibm.com/anaconda3
	py27                     /Users/Jasmine.Mou@ibm.com/anaconda3/envs/py27
	py34                     /Users/Jasmine.Mou@ibm.com/anaconda3/envs/py34
	py36                     /Users/Jasmine.Mou@ibm.com/anaconda3/envs/py36
source activate py36
source deactivate

## package installation: https://docs.anaconda.com/anaconda/#packages-available-in-anaconda

#### automatically installed with anaconda: > 150 packages

#### additional packages in anaconda repository: >250 packages
conda list  ## list all the installed packages in current environment
conda search PKG_NAME
conda install PKG_NAME

#### other packages
pip install PKG_NAME

#### make your own packages and share to the public
conda build PKG_NAME

anaconda-navigator ## to start an anaconda interface

conda install r-essentials --yes ## install R through conda

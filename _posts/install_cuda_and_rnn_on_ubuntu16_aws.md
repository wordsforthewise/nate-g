# run a recurrent neural net on AWS GPU machines

# using this git library: https://github.com/jcjohnson/torch-rnn

# first, follow installation instructions:

# install this stuff:

sudo apt-get -y install python2.7-dev
sudo apt-get install libhdf5-dev

cd torch-rnn
pip install -r requirements.txt

# install torch: http://torch.ch/docs/getting-started.html#_

git clone https://github.com/torch/distro.git ~/torch --recursive
cd ~/torch; bash install-deps; # takes a bit
./install.sh # takes a few minutes

# On Linux with bash
source ~/.bashrc
# On Linux with zsh
source ~/.zshrc
# On OSX or in Linux with none of the above.
source ~/.profile

luarocks install torch
luarocks install nn
luarocks install optim
luarocks install lua-cjson

git clone https://github.com/deepmind/torch-hdf5
cd torch-hdf5
luarocks make hdf5-0-0.rockspec

# install CUDA: for ubuntu 16.04 on AWS, I had to sign up for a developer account
# for CUDA, and download the .deb file
# https://developer.nvidia.com/cuda-release-candidate-download
# I created an account, signed in, and navigated to the page and got the cookie from
# the developer tools (ctrl + I in chrome) / navigation pane
# I had to first paste the command into a text editor (e.g. atom)
# because copying the cookie had a line break at the end
# I copied the link address for the download, and
# I then did:

wget --headers "cookie: aosentuhaoensuhtoenas" https://developer.nvidia.com/compute/cuda/8.0/rc/local_installers/cuda-repo-ubuntu1604-8-0-rc_8.0.27-1_amd64-deb
wget --header "Cookie:__unam=4d8c667-1571bd3a153-aoeu-2; visid_incap_23871=aoeu" https://developer.nvidia.com/compute/cuda/8.0/rc/local_installers/patches/cuda-misc-headers-8-0_8.0.27.1-1_amd64-deb

# which gets the CUDA pack as well as the patch for later versions of GCC

# next, do:

sudo dpkg -i cuda-repo-ubuntu1604-8-0-rc_8.0.27-1_amd64-deb # they mistakenly have a - instead of . before deb
sudo dpkg -i cuda-misc-headers-8-0_8.0.27.1-1_amd64-deb
sudo apt-get update
sudo apt-get install cuda
sudo apt-get install cuda-misc-headers-8-0

# I then started installing the torch/cuda lua packages.
# I had to force use a lower version of GCC and G++:
# (thanks to this guy: https://github.com/torch/nn/issues/570)
CC=gcc-4.8 CXX=g++-4.8 luarocks install cutorch
CC=gcc-4.8 CXX=g++-4.8 luarocks install cunn

git clone https://github.com/jcjohnson/torch-rnn.git

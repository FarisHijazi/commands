# Linux setup

Installing GNU parallel ([see stackoverflow answer here](https://stackoverflow.com/a/28361402/7771202))

```bash
sudo apt install -y parallel
```

## Established

Setup, mostly installation stuff

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y google-chrome-stable snapd cargo terminator htop dconf-editor snap copyq
sudo snap install code --classic
sudo snap install ruby --classic
cargo install dutree
gem install colorls
```

```bash
git config --global credential.helper store
git clone https://github.com/FarisHijazi/dotfiles && mv dotfiles/.git . && mv dotfiles/.gitignore . && rm -rf dotfiles
# to apply changes to latest branch:
git git reset --hard
```

## Conda

For anaconda, instead of going through the annoying webpage installer, just run the following command:

```bash
wget https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh
sh Anaconda3-2020.11-Linux-x86_64.sh -b -p $HOME/anaconda3
~/anaconda3/bin/conda config --set auto_activate_base true # (optional)
~/anaconda3/bin/conda init # (optional)

conda update -y -n base -c defaults conda # (optional)
```

### Auto activating when `cd`ing into a folder

if you're using bash, modify your `~/.bashrc` file and add the following lines at the bottom after the conda prompt:

```bash
## activate conda environment based on current directory name
cd () {
    if builtin cd ${1:+"$@"}; then
        conda activate $(echo $(basename $(pwd))) 2>/dev/null
    fi
}
conda activate $(echo $(basename $(pwd))) 2>/dev/null
```


## Installing cuda

### Using [lambda stack](https://lambdalabs.com/lambda-stack-deep-learning-software)

### Using nnabla script
find neat script at [nnabla.org/install](https://nnabla.org/install/)

Install

```bash
LAMBDA_REPO=$(mktemp) && \
wget -O${LAMBDA_REPO} https://lambdalabs.com/static/misc/lambda-stack-repo.deb && \
sudo dpkg -i ${LAMBDA_REPO} && rm -f ${LAMBDA_REPO} && \
sudo apt-get update && sudo apt-get install -y lambda-stack-cuda
sudo reboot
```

Update

```bash
sudo apt-get install docker.io nvidia-container-toolkit
```

```bash
# this is all taken from nnabla.org/install
apt-get update
apt-get install -y --no-install-recommends \
	gnupg2 \
	curl \
	ca-certificates
curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add -
echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list
if [ ! -z 1 ]; then echo "deb https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list; fi
apt-get update
apt-get install -y --no-install-recommends \
	cuda-cudart-11-0=11.0.221-1 \
	cuda-compat-11-0
ln -s cuda-11.0 /usr/local/cuda
echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf
echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf
apt-get update
apt-get install -y --no-install-recommends \
	cuda-libraries-11-0=11.0.3-1 \
	libnpp-11-0=11.1.0.245-1 \
	cuda-nvtx-11-0=11.0.167-1 \
	libcusparse-11-0=11.1.1.245-1 \
	libcublas-11-0=11.2.0.252-1 \
	libnccl2=2.10.3-1+cuda11.0
apt-mark hold libcublas-11-0 libnccl2
apt-get update
apt-get install -y --no-install-recommends \
	"libcudnn8=8.0.5.39-1+cuda11.0"
apt-mark hold "libcudnn8"
echo "PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}" >> /etc/environment
echo "LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64">> /etc/enviromnent

# If you cannot use nvidia cuda repository or cuda-drivers does not worl well (e.g Ubuntu18.04 with CUDA9.2),
# You can use ppa to install newest nvidia-driver
(sudo apt install -y --no-install-recommends cuda-drivers) || (sudo add-apt-repository -y ppa:graphics-drivers/ppa && sudo apt install -y nvidia-driver-430)

```

## Desktop and Window manager

```bash
#disable caps lock and make it LCtrl
/usr/bin/setxkbmap -option "nocaps"
```

visit [this page](https://itectec.com/ubuntu/ubuntu-how-to-snap-a-window-in-a-corner-with-18-04/) to see how to install Put windows. This allows you to snap windows to the corners

```bash
sudo apt install gnome-shell-extensions chrome-gnome-shell
```

```bash
# disable mouse acceleration
gsettings set org.gnome.desktop.peripherals.mouse accel-profile 'flat'
```

```bash
sudo apt-get install -y gnome-tweaks
```

## Packages

```bash
# redshift gtk
sudo add-apt-repository -y ppa:dobey/redshift-daily && sudo apt update && sudo apt install -y redshift
```

```bash
# RGB keyboard control 
sudo add-apt-repository -y ppa:tatokis/ckb-next
sudo apt-get update
sudo apt install -y ckb-next
```

```bash
# jebtrains
wget "https://download.jetbrains.com/toolbox/jetbrains-toolbox-1.19.7784.tar.gz"
```

### Install integrated terminal inside file explorer

see official docs here: https://github.com/flozz/nautilus-terminal

```bash
#Ubuntu 20.04 and later
#To install Nautilus Terminal on Ubuntu >= 20.04, first install dependencies:
conda deactivate
sudo apt install python3-nautilus python3-psutil python3-pip libglib2.0-bin dconf-editor
#Then install Nautilus Terminal:
sudo pip3 install nautilus-terminal
sudo nautilus-terminal --install-system
#Finally close current Nautilus instance to apply the changes:
nautilus -q
```

## Instasll MPS-Youtube

To listen to music from the command line

YouTube API key for `mpsyoutube`

```bash
sudo apt install -y mpv
sudo apt remove youtube-dl
pip install -U youtube-dl
pip install git+https://github.com/mps-youtube/mps-youtube.git
rm ~/.config/mps-youtube/cache_py_*

# quick fix for "KeyError: 'dislike_count' in pafy"
pip uninstall -y pafy; pip install git+https://github.com/Cupcakus/pafy

mpsyt set api_key APIKEYHERE
set search_music false
/Mick Gordon - The DOOM Hunter DOOM Eternal - Extended Gamerip, 1

# == more troubleshooting ==
https://github.com/mps-youtube/mps-youtube/issues/551#issuecomment-266250991
```

```bash
pip install ddgr
# use: 
ddgr -j "tensorflow mirrored strategy multigpu slower than single gpu"
```

```bash
# expressvpn
wget https://www.download-express-apps.net/clients/linux/xv_3.8.0.4-1_amd64.deb -O expressvpn.deb
sudo apt install ./expressvpn.deb

expressvpn activate
$ ENTERKEY
$ n
expressvpn autoconnect yes  # (optional)
```

```bash
# increase inotify limit
echo "fs.inotify.max_user_watches=524288" | sudo tee -a /etc/sysctl.conf
```

## Fixes

### Fix for `SyntaxError: multiple statements found while compiling a single statement`

This error happens to me when pasting python code into the command line

```bash
# https://stackoverflow.com/a/67757736/7771202
echo "set enable-bracketed-paste off" >> ~/.inputrc

```

## latex stuff

[https://www.maths.tcd.ie/~dwilkins/LaTeXPrimer/](https://www.maths.tcd.ie/~dwilkins/LaTeXPrimer/)

## Resources

- [https://blog.jread.com/speed-up-your-command-line-navigation-d4050207f02c?source=activity---post_recommended](https://blog.jread.com/speed-up-your-command-line-navigation-d4050207f02c?source=activity---post_recommended)
- [https://blog.jread.com/speed-up-your-command-line-navigation-part-2-d55ef1ef8ca9](https://blog.jread.com/speed-up-your-command-line-navigation-part-2-d55ef1ef8ca9)
- **RICE** nicer terminals:
    - [https://medium.com/@ivanaugustobd/your-terminal-can-be-much-much-more-productive-5256424658e8](https://medium.com/@ivanaugustobd/your-terminal-can-be-much-much-more-productive-5256424658e8)
    - [https://reaganmcf.medium.com/how-to-get-the-most-out-of-your-terminal-7af47f7730a3](https://reaganmcf.medium.com/how-to-get-the-most-out-of-your-terminal-7af47f7730a3)

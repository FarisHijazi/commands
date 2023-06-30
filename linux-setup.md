# Linux setup

for anything that needs `sudo`, be sure to run `sudo --validate` before pasting the commands so that they don't get interrupted by `sudo` asking for your password

---

Setup, mostly installation stuff

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y google-chrome-stable snapd cargo terminator htop dconf-editor snap copyq
sudo snap install code --classic
sudo snap install ruby --classic
cargo install dutree
gem install colorls

sudo wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar -O /opt
# then make sure to add alias: `alias bfg="java -jar /opt/bfg-1.14.0.jar"`
```

```bash
git config --global credential.helper store
git clone https://github.com/FarisHijazi/dotfiles && mv dotfiles/.git . && mv dotfiles/.gitignore . && rm -rf dotfiles
# to apply changes to latest branch:
git stash -m "original state before downloading dotfiles"
# or: git git reset --hard
```

## [fzf Fuzzy finder](https://github.com/junegunn/fzf)

```
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && yes y | ~/.fzf/install
```

## Conda

For anaconda, instead of going through the annoying webpage installer, just run the following command:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/anaconda3
~/anaconda3/bin/conda config --set auto_activate_base true # (optional)
~/anaconda3/bin/conda init # (optional)
source ~/.bashrc
conda update -y -n base -c defaults conda # (optional)
```

fix bluetooth, from [this answer](https://askubuntu.com/a/1123633)

```sh
sudo apt-get install 'bluez*' blueman
modprobe btusb
sudo systemctl restart bluetooth

```

### getting the average in the command line using a pipe `|`

```sh
python -c 'from sys import argv; argv=argv[1:]; print(sum(list(map(float, argv)))/len(argv))'
```

you can create an alias for this in `~/.bash_aliases`

```sh
alias avg="xargs python -c 'from sys import argv; argv=argv[1:]; print(sum(list(map(float, argv)))/len(argv))'"
```

Usage:

```sh
$ seq 10|avg
5.5
```

### Auto activating when `cd`ing into a folder

if you're using bash, modify your `~/.bashrc` file and add the following lines at the bottom after the conda prompt:

```bash
## activate conda environment based on current directory name
cd () {
    if builtin cd ${1:+"$@"}; then
        conda activate $(echo $(basename "$(pwd)")) 2>/dev/null
    fi
}
conda activate $(echo $(basename "$(pwd)")) 2>/dev/null
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
sudo apt install -y python3-nautilus python3-psutil python3-pip libglib2.0-bin dconf-editor
sudo pip3 install psutil
#Then install Nautilus Terminal:
sudo pip3 install nautilus-terminal
sudo nautilus-terminal --install-system
#Finally close current Nautilus instance to apply the changes:
nautilus -q
```

## To install POP!_OS's tiling window manager on Ubuntu, run the following:

```sh
cd /opt/
sudo apt install node-typescript make git -y 
git clone https://github.com/pop-os/shell.git
cd shell
make local-install
```

## Install MPS-Youtube

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

## Debugging `apt` issues

[This answer](https://askubuntu.com/a/1385445/1048079) soles 404 URLs when using apt like bellow:

```
E: Failed to fetch http://security.ubuntu.com/ubuntu/pool/main/e/expat/libexpat1-dev_2.2.9-1ubuntu0.4_amd64.deb  404  Not Found [IP: 185.125.190.36 80]
E: Failed to fetch http://security.ubuntu.com/ubuntu/pool/main/t/tiff/libtiffxx5_4.1.0+git191117-2ubuntu0.20.04.5_amd64.deb  404  Not Found [IP: 185.125.190.36 80]
E: Failed to fetch http://security.ubuntu.com/ubuntu/pool/main/t/tiff/libtiff-dev_4.1.0+git191117-2ubuntu0.20.04.5_amd64.deb  404  Not Found [IP: 185.125.190.36 80]
E: Failed to fetch http://security.ubuntu.com/ubuntu/pool/main/libx/libxml2/libxml2-dev_2.9.10+dfsg-5ubuntu0.20.04.4_amd64.deb  404  Not Found [IP: 185.125.190.36 80]
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

```sh
# First, restore the default repositories using these commands:

mkdir ~/solution
cd ~/solution/

cat << EOF > ~/solution/sources.list
deb http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse

deb http://archive.canonical.com/ubuntu focal partner
deb-src http://archive.canonical.com/ubuntu focal partner
EOF

sudo sed -i "s/focal/$(lsb_release -c -s)/" ~/answer/sources.list
sudo rm /etc/apt/sources.list
sudo cp ~/solution/sources.list /etc/apt/sources.list

# Remove all the PPAs in your system:
sudo mv /etc/apt/sources.list.d/* ~/solution

# Update the repositories:
sudo apt update
```




## latex stuff

[https://www.maths.tcd.ie/~dwilkins/LaTeXPrimer/](https://www.maths.tcd.ie/~dwilkins/LaTeXPrimer/)

## Resources

- [https://blog.jread.com/speed-up-your-command-line-navigation-d4050207f02c?source=activity---post_recommended](https://blog.jread.com/speed-up-your-command-line-navigation-d4050207f02c?source=activity---post_recommended)
- [https://blog.jread.com/speed-up-your-command-line-navigation-part-2-d55ef1ef8ca9](https://blog.jread.com/speed-up-your-command-line-navigation-part-2-d55ef1ef8ca9)
- **RICE** nicer terminals:
    - [https://medium.com/@ivanaugustobd/your-terminal-can-be-much-much-more-productive-5256424658e8](https://medium.com/@ivanaugustobd/your-terminal-can-be-much-much-more-productive-5256424658e8)
    - [https://reaganmcf.medium.com/how-to-get-the-most-out-of-your-terminal-7af47f7730a3](https://reaganmcf.medium.com/how-to-get-the-most-out-of-your-terminal-7af47f7730a3)

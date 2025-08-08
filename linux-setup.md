# Linux setup

for anything that needs `sudo`, be sure to run `sudo --validate` before pasting the commands so that they don't get interrupted by `sudo` asking for your password

---

Setup, mostly installation stuff

```bash
sudo add-apt-repository multiverse -y
sudo add-apt-repository universe -y
sudo add-apt-repository restricted -y
sudo add-apt-repository main -y
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl wget ripgrep rclone rsync google-chrome-stable snapd cargo terminator htop dconf-editor snap copyq net-tools htop nvtop iotop gpustat xclip
sudo snap install astral-uv --classic # or # curl -LsSf https://astral.sh/uv/install.sh | sh
sudo snap install code --classic
sudo snap install ruby --classic
cargo install dutree
gem install colorls

sudo wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar -O /opt
# then make sure to add alias: `alias bfg="java -jar /opt/bfg-1.14.0.jar"`
```

set automatic timezone
```bash
# for Saudi use Asia>Qatar as the location
sudo dpkg-reconfigure tzdata
timedatectl set-ntp true
```

install flameshot and setup the default shortcut like [here](https://github.com/flameshot-org/flameshot/#on-ubuntu-tested-on-1804-2004-2204)
```
sudo apt install flameshot -y
```

```bash
git config --global user.name "Faris Hijazi"
git config --global user.email theefaris@gmail.com

cd $HOME
git config --global credential.helper store
git clone https://github.com/FarisHijazi/dotfiles && mv dotfiles/.git . && mv dotfiles/.gitignore . && rm -rf dotfiles
# to apply changes to latest branch:
git stash -m "original state before downloading dotfiles"
# or: git git reset --hard
```

## reset `~/.ssh` permissions

```sh
sudo chmod 700 ~/.ssh
sudo chmod 600 ~/.ssh/id_rsa
sudo chmod 644 ~/.ssh/id_rsa.pub
sudo chmod 600 ~/.ssh/authorized_keys
sudo chmod 644 ~/.ssh/config
```

## sudo settings

```sh
# disallow ssh password login
echo "PasswordAuthentication no" | sudo tee -a /etc/ssh/sshd_config

# option 1
echo "$USER ALL = (ALL) NOPASSWD: ALL" | sudo tee -a /etc/sudoers

# option 2
# Edit the /etc/sudoers file by typing the visudo command in the terminal window.
sudo visudo
# Append the following entry so you can run all commands without a password.
# Save and exit.

```

## [fzf Fuzzy finder](https://github.com/junegunn/fzf)

```
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && yes y | ~/.fzf/install
```

## Tailscale

```
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

# You're connected! You can find your Tailscale IPv4 address by running:
tailscale ip -4
```

## [Fabric AI tool](https://github.com/danielmiessler/fabric?tab=readme-ov-file#installation)

```sh
# this assumes PATH variable contains $HOME/.local/bin
mkdir -p $HOME/.local/bin
cd $HOME/.local/bin
curl -L https://github.com/danielmiessler/fabric/releases/latest/download/fabric-linux-amd64 > fabric && chmod +x fabric && ./fabric --version
sudo apt upgrade libc6 -y # might be needed if you have GLIBC error
fabric -U # update prompts
fabric --setup

```

## [Cloudflare WARP on linux](https://developers.cloudflare.com/warp-client/get-started/linux/)

```sh
# Add cloudflare gpg key
curl -fsSL https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
# Add this repo to your apt repositories
echo "deb [signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list

# Install
sudo apt-get update && sudo apt-get install cloudflare-warp -y
yes | warp-cli registration new
warp-cli connect
curl https://www.cloudflare.com/cdn-cgi/trace/ # and verify that warp=on
```

## Node, npm and nvm

installation

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash

source ~/.bashrc
nvm --version
nvm ls-remote
nvm install v20.5.1
sudo ln -s $HOME/.nvm/versions/node/v20.5.1/bin/npm /usr/local/bin/npm
sudo ln -s $HOME/.nvm/versions/node/v20.5.1/bin/node /usr/local/bin/node
```

## Conda

For Miniconda3, instead of going through the annoying webpage installer, just run the following command:

```bash

wget --no-clobber https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
rm -rf $HOME/miniconda3
sh Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
$HOME/miniconda3/bin/conda config --set auto_activate_base true # (optional)
$HOME/miniconda3/bin/conda init # (optional)
source ~/.bashrc
conda update -y -n base -c defaults conda # (optional)
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

```bash

##
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker.io nvidia-container-toolkit nvidia-docker2
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
# test
sudo docker run hello-world


# install docker-compose standalone
curl -SL https://github.com/docker/compose/releases/download/v2.29.6/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

###

# Create a group named docker
sudo groupadd docker
sudo usermod -aG docker $USER
# docker-compose permission
sudo chmod 666 /var/run/docker.sock

# test
docker run hello-world
```

## Installing cuda

### Using [lambda stack](https://lambdalabs.com/lambda-stack-deep-learning-software)

### Using nnabla script
find neat script at [nnabla.org/install](https://nnabla.org/install/)

Install

```bash
wget -nv -O- https://lambdalabs.com/install-lambda-stack.sh | sh -
sudo reboot
```

## Ubuntu environment stuff

enable type-ahead-find in nautilus (jump to the file when you type the first letter just like windows):

```sh
gsettings set org.gnome.nautilus.preferences enable-interactive-search true
```

Run .sh file when double click

```sh
gsettings set org.gnome.nautilus.preferences executable-text-activation 'launch'
```

## Setting up shared `~/.cache` across users

Benefit: prevent downloading the same file multiple times for each user.
This is potentially dangerous, but I'm gonna go for it anyway.

The bellow code will have a common huggingface cache directory across all users, this will make models and datasets shared and only needed to be downloaded once per machine

```sh
sudo mkdir -p /shared/.cache/huggingface/
sudo chown -R $USER /shared/.cache/huggingface/
mkdir -p $HOME/.cache/huggingface/

sudo apt install rclone -y
# repeat the bellow for each user <CHANGE THE USER>
USERNAME=fhijazi
sudo rclone copy /home/$USERNAME/.cache/huggingface/* /shared/.cache/huggingface/ -L --update --verbose
ln -s /home/$USERNAME/.cache/huggingface/ /shared/.cache/huggingface/
```


fix bluetooth, from [this answer](https://askubuntu.com/a/1123633)

```sh
sudo apt-get install 'bluez*' blueman
modprobe btusb
sudo systemctl restart bluetooth

```

#### some help with installin nvidia docker

- https://github.com/NVIDIA/nvidia-docker/issues/1243#issuecomment-694981577
- https://github.com/NVIDIA/nvidia-docker/issues/1238#issuecomment-734918630


## Desktop and Window manager

```bash
#disable caps lock and make it LCtrl
/usr/bin/setxkbmap -option "nocaps"
```

visit [this page](https://itectec.com/ubuntu/ubuntu-how-to-snap-a-window-in-a-corner-with-18-04/) to see how to install Put windows. This allows you to snap windows to the corners

```bash
sudo apt install gnome-shell-extensions chrome-gnome-shell

# disable mouse acceleration
gsettings set org.gnome.desktop.peripherals.mouse accel-profile 'flat'

sudo apt-get install -y gnome-tweaks
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
sudo apt install node-typescript make git -y 

mkdir /opt/ && cd /opt/
sudo chown -R $USER .
git clone https://github.com/pop-os/shell.git
cd shell

git checkout master_focal # For Ubuntu 20.04, 20.10, 21.04, and 21.10.
#git checkout master_jammy # For Ubuntu 22.04, 22.10, and 23.04.
#git checkout master_mantic # For Ubuntu 23.10.
#git checkout master_noble # For Ubuntu 24.04, 24.10, and later versions.
make local-install
```

## Install MPS-Youtube

To listen to music from the command line

YouTube API key for `mpsyoutube`

```bash
sudo apt install -y mpv

#sudo apt remove youtube-dl
#pip install -U youtube-dl
#pip install git+https://github.com/mps-youtube/mps-youtube.git
#rm ~/.config/mps-youtube/cache_py_*

# quick fix for "KeyError: 'dislike_count' in pafy"
# pip uninstall -y pafy; pip install git+https://github.com/Cupcakus/pafy

# mpsyt set api_key APIKEYHERE

pip3 install yewtube

yt
set search_music false
set show_video false
/Mick Gordon - The DOOM Hunter DOOM Eternal - Extended Gamerip
1

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

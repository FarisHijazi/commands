## System and finder settings

```sh
# show hidden files by default
defaults write com.apple.finder AppleShowAllFiles -bool true

# sudo uses fingerprint
sed "s/^#auth/auth/" /etc/pam.d/sudo_local.template | sudo tee /etc/pam.d/sudo_local

# install homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

```


mac apple silicon conda

```bash
mkdir -p ~/miniconda3
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh -o ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
# rm ~/miniconda3/miniconda.sh
~/miniconda3/bin/conda config --set auto_activate_base true # (optional)
~/miniconda3/bin/conda init # (optional)
source ~/.bashrc
source ~/.zshrc
conda tos accept --override-channels --channel  defaults
conda update -y -n base -c defaults conda # (optional)
```

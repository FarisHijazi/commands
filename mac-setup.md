## System and finder settings

```sh
# show hidden files by default
defaults write com.apple.finder AppleShowAllFiles -bool true

# sudo uses fingerprint
sed "s/^#auth/auth/" /etc/pam.d/sudo_local.template | sudo tee /etc/pam.d/sudo_local

# install homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

```

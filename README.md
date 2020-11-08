# gpg-shell

`gpg-shell` is a zsh shell script to manage environment variables with GnuPG asymmetric encryption. It is designed and recommended to be used with Yubikey as the secret key storage.

## Install

```bash
# dotfiles/install.sh

# 1. Clone this repository with your dotfiles
  git submodule add https://github.com/menduz/gpg-shell

# 2. Add the folder to your PATH in your .zshrc
  echo "export PATH=\"$(pwd)/gpg-shell:\$PATH\"" >> "$HOME/.zshrc"

# 3. Configure the folder to store your encrypted environments
#    (I do it as a subfolder "encrypted-shells" in my dotfiles)
  mkdir -p "$(pwd)/encrypted-shells"
  ln -sf "$(pwd)/encrypted-shells" "$HOME/.gpg-shell-db"

# 4. Configure the recipient and backup GPG keys (interactive process)
  gpg-shell setup-keygroup
```

## How does it work?

First you must configure the allowed keys to decrypt the environment files: `gpg-shell setup-keygroup` always include yourself. It is a good idea to have a backup key, in case you lose your current GPG key.

The recipient list is not stored in the encrypted file ([`--throw-keyids`](https://www.gnupg.org/gph/en/manual/r2110.html)).

The recipient list is stored as a gpg group named `gpg_shell_keygroup` in the file `$HOME/.gnupg/gpg.conf`. (I also link this file to my dotfiles).

It is important to set the trust of all your recipient keys, otherwise you won't be able to encrypt anything for them.

### Step to step guide

1. Create a environment configuration "work-dev" using `gpg-shell create work-dev`
2. `vim` will open, in that configuration you may add environment variables like
   ```bash
   export MY_SECRET="asd123-work-dev"
   ```
   Save and quit, your encrypted shell is ready to be started.
3. To access your new environment type `gpg-shell work-dev`, touching your YubiKey may be required to decrypt the environment. You can test it works by executing the command
   ```bash
   echo "$MY_SECRET" # should print 'asd123-work-dev'
   ```
Welcome to IST
================

Below is some basic information for all IST employees.

Creating SSH Keys
------------------
IST uses [Code@Berkeley](http://code.berkeley.edu) to hold its repositories.  Code@Berkeley uses public key
authentication and key-based SSH login to authenticate users who are able to access these repositories.  The public
and private keys can be generated on any OS.

### Ubuntu
1. Open up a terminal and type in

        $ mkdir ~/.ssh  
        $ chmod 700 ~/.ssh  
        $ ssh-keygen -t rsa

2. You can keep the keys in the selected directory and you can choose to create a passphrase or leave it blank.

3. `ls` into your .ssh folder and search for your public key.  Open up a text editor and copy down the information
in the public key.

4. Go to [Code@Berkeley](http://code.berkeley.edu).  Log in with your CalNet credentials.  Go to "Manage SSH Public
Keys" and click "New SSH Public Key".  Paste your public key information into the text box.  Make sure your key name
matches the file name of the public key.

Setting Up Ruby
-----------------
To allow us to be most flexible in using and testing with different versions of Ruby, you will want to use `rbenv`
along with `ruby-build`. These will allow for installing and changing versions of Ruby without affecting your system's
version of Ruby. They are publicly available on GitHub, and we've provided instructions on setting each one up below.
Note that we are explicitly avoiding `rvm` and that `rbenv` is _incompatible_ with `rvm`. Things will appear to work until 
you try to install a gem. The problem is that rvm actually overrides the `gem` and `cd` commands with shell functions!
Please remove any references to `rvm` before using `rbenv` by following the instructions [here]
(https://raw.github.com/SixArm/sixarm_unix_shell_scripts/master/rvm-uninstall-danger).

### rbenv

This will get you going with the latest version of rbenv and make it
easy to fork and contribute any changes back upstream.

1. Check out rbenv into `~/.rbenv`.

        $ cd
        $ git clone git://github.com/sstephenson/rbenv.git .rbenv

2. Add `~/.rbenv/bin` to your `$PATH` for access to the `rbenv`
   command-line utility.

        $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile

    **Zsh note**: Modify your `~/.zshenv` file instead of `~/.bash_profile`.
    
    **Ubuntu note**: Modify your `~/.bashrc` file instead of `~/.bash_profile`.

3. Add rbenv init to your shell to enable shims and autocompletion.

        $ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile

    **Zsh note**: Modify your `~/.zshenv` file instead of `~/.bash_profile`.
    
    **Ubuntu note**: Modify your `~/.bashrc` file instead of `~/.bash_profile`.

4. Restart your shell so the path changes take effect. You can now
   begin using rbenv.

        $ exec $SHELL

5. Since we are using ruby-build, we can simply call an `rbenv install` command
to install new versions of Ruby. To install 1.9.3-p194 for example, do the following
once you set up ruby-build (below):

        $ rbenv install 1.9.2-p290

6. Rebuild the shim binaries. You should do this any time you install
   a new Ruby binary (for example, when installing a new Ruby version,
   or when installing a gem that provides a binary).

        $ rbenv rehash

### ruby-build

ruby-build provides a simple way to compile and install different
versions of Ruby on UNIX-like systems.

To install, simply get a copy of the repo and use the install script

    $ git clone git://github.com/sstephenson/ruby-build.git
    $ cd ruby-build
    $ ./install.sh

This will install ruby-build into `/usr/local`. If you do not have
write permission to `/usr/local`, you will need to run `sudo
./install.sh` instead. You can install to a different prefix by
setting the `PREFIX` environment variable.

If you only intend to use ruby-build via rbenv then you can
install it locally as a plugin:

    $ mkdir -p ~/.rbenv/plugins
    $ cd ~/.rbenv/plugins
    $ git clone git://github.com/sstephenson/ruby-build.git

Use the above commands if you get the `rbenv: no such command 'install'` error.

And if you're using Homebrew, you can just

    $ brew install ruby-build

to get the latest release, or

    $ brew install --HEAD ruby-build

to pull the latest from git.
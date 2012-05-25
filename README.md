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
        $ ssh-keygen

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
Note that we are explicitly avoiding `rvm` and that `rbenv` is _incompatible_ with `rvm`. Things will appear to work 
until you try to install a gem. The problem is that rvm actually overrides the `gem` and `cd` commands with shell 
functions! Please remove any references to `rvm` before using `rbenv` by following the instructions [here]
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

Setting up your Postgres Database from a database.template.yml
-------------------------------------------------------------

When setting up a Rails project from a repository, IST uses a database.template.yml file to outline the database 
for the project. We don't want to send the original database.yml file since that contains sensitive information 
such as passwords. To get the project up and running, you need to create a database for the project with the same 
properties as in  the template file. We're going to be using Postgres for this example.

###Set up Postgres on your machine

First, Postgres needs to be installed on your machine so enter the following in the terminal.

    $ sudo apt-get install postgresql

This will make a postgres user on your machine, whose password we need to change in order to access the postgres server.

    $ sudo su - postgres

to change to the postgres user, then

    $ \password postgres

to set its password.

###Make your databases

Now with the server set up, we want to make some databases for the Ruby on Rails project. This can be done with 
command-line or using pgadmin3, a nice application providing a user interface for interacting with your postgres 
server and its databases. We'll use pgadmin first.

#####pgadmin

To get pgadmin3 on your machine, simply use apt-get

    $ apt-get install pgadmin3

Now start up pgadmin.

*__Note__: We've had experience with pgadmin3 in Ubuntu 12 not appearing on the task bar, preventing you from selecting
it when minimized or alt-tabbing to it. If this occurs, restart pgadmin or your system.*

With pgadmin running, click the power-plug button to make a connection to your server and fill out the following
fields:

    Name: (something like postgres-server, as this is your server for your entire machine)
    Host: localhost
    Password: (password you made for your postgres user)

Make sure the `Username` field is 'postgres', and click Ok. It may propmt you about storing your password; you can 
ignore that. 

That should set up a connection to your server. Expand `Server Groups` and `Servers` to see your server with the name
you entered for your connection. Expand that to see your `Database`, `Tablespaces`, and `Login Roles`. The first thing
we want to do is make a new `Login Role` for the project. Postgres considers Roles like Users, so you'll see that the
postgres user is already there. To make a new role, right-click on `Login Roles` and select `New Login Role...`. Fill
out the Name (something to do with the project), then on the `Definition` tab, fill out the password for this role
(hopefully different from your postgres user). On the `Role privileges` tab, make sure `Can create databse objects` is
checked. Now click Ok. If you ever need to change the role settings, just right-click the role, and select `Properties`.

Now we can finally make a new database for our project. Right-click on `Databases` and select `New Database...`. Fill 
out the name to be (project\_name)\_development, and set the Owner to be the Role you just created. That should be all 
you need, so click Ok. This database will be for use when developing the Rails app. You'll want to make another 
database for your project called (project\_name)\_test for running tests on.

###Connect your Rails project to the databases

Now armed with your new databases, lets finally go into your Ruby on Rails project. Take your `database.template.yml` 
file and make a copy called `database.yml`. This is the file that will actually be used. Edit the file and ensure that
`database:` is the name of the databse you want to use, and that the `username:` and `password:` match the owner role
you made for the database. Now all you need to do is

    $ bundle exec rake db:migrate
    
and your database should be set up. Running `$ rails server` should work properly and you can view your Rails app!

**Note:** Be sure to place `database.yml` in your version control's ignore file.

#####Peer authentication failed!

When doing the `db:migrate`, you may get a Peer authentication failed error. First, check that your password is correct
and if so, you'll need to make a change to a Postgres file. First,

    $ cd /etc/postgresql/(version)/main/

Then edit `pg_hba.conf` (you'll have to sudo). Look for the lines that reads

    # TYPE  DATABASE           USER               ADDRESS               METHOD
    
    # "local" is for Unix domain socket connections only
    local    all                all                                      peer

What you want to change is that bottom line, making the method md5

    local    all                all                                      md5

That should fix the Peer authentication error.

###Change the Rails environment

As noted above, you'll probably have different databases for development and running tests (and maybe production). In
order to change which database you're using, you need to change the Rails environment. Run the following

    $ bundle exec rake environment RAILS_ENV=(new_environment) db:migrate
    
For example, to change to the test environment:

    $ bundle exec rake environment RAILS_ENV=test db:migrate
    

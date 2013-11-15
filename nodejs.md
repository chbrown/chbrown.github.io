# Node.js + NPM

Instructions for installing [Node.js](http://nodejs.org/) and its requirements on:

- [Mac OS X](#mac-os-x)
- The [Red Hat Enterprise Linux](#red-hat) (RHEL) family: Red Hat, CentOS, Fedora. Amazon's standard Linux AMI is part of this family.
- [Arch Linux](#arch-linux)

(Click any item to jump to that section of this tutorial.)


## Mac OS X

**Prerequisite package manager**

Get [Homebrew](http://brew.sh/) ([GitHub repo](https://github.com/mxcl/homebrew)).
You should have this already, if you're on a Mac.

        # OS X is most commonly a single-user workstation, so keep it simple:
        sudo chown -R `whoami`:staff /usr/local
        # Single line install from brew.sh website (installs in `/usr/local`)
        ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

Troubleshooting: if that doesn't work, see the Homebrew wiki's [Installation](https://github.com/mxcl/homebrew/wiki/Installation) page.

**Using Homebrew**

    # Update homebrew if you already have it:
    brew update
    # Homebrew install Node.js and NPM together
    brew install node


## Red Hat Enterprise Linux

Enable "EPEL" (Extra Packages for Enterprise Linux):

    # Edit the secondary repos...
    #   change the `enabled=0` line in the first block to `enabled=1`
    sudo vim /etc/yum.repos.d/epel.repo

On my AMI, it now looks something like this:

    [epel]
    name=Extra Packages for Enterprise Linux 6 - $basearch
    #baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
    mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
    failovermethod=priority
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6

Use `yum` to install Node.js via package manager from the EPEL repository:

    sudo yum install -y nodejs npm


## Arch Linux

Update your Pacman repository information first, just in case:

    sudo pacman -Sy

Use Pacman to install Node.js via package manager:

    sudo pacman -S nodejs

This installs the `node` binary to `/usr/bin/node`, unfortunately, which means you'll need to `sudo npm install -g ...` all your global packages down the line, but it's easier than installing from source. And `npm`'s attitude toward localizing packages makes it a good deal less painful than, say, Python's site-packages.

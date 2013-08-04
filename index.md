---
layout: default
---
## Other gh-pages

* [Set](http://chbrown.github.io/set/)
* [Clock](http://chbrown.github.io/clock-js/)
* [Tree](http://chbrown.github.io/tree-js/)
* [Maps](http://chbrown.github.io/maps/)


## Views

* [my gitspective](http://zmoazeni.github.io/gitspective/#/timeline/chbrown)


## References

### Adding a new user on linux

* `adduser` is sometimes a perl wrapper, askin for each detail of information interactively
* `useradd` is the basic linux system command, non-interactive
    - `-m` creates a home directory in some default location (see `/etc/default/useradd` for those defaults)
    - `-U` create a group of the same name as the user
    - `-G group1,group2` csv of secondary groups
    - and then a username

        sudo useradd -m -U -G wheel chbrown

And then maybe set the password:

    sudo passwd chbrown

Return to the local machine. Copy default local public key to the remote machine:

    ssh-copy-id chbrown@remote

Or if you don't have ssh-copy-id:

    # on remote:
    mkdir -pm 700 ~/.ssh
    touch ~/.ssh/authorized_keys
    chmod 644 ~/.ssh/authorized_keys

    # on local:
    cat ~/.ssh/id_rsa.pub | ssh remote 'cat > ~/.ssh/authorized_keys'

### ssh tunneling

* `ssh`
    - `-N` Do nothing
    - `-f` Fork to Background
    - `-q` Suppress any output
    - `-v` Verbose
    - `-p` Specify port (defaults to 22)

| command | effect |
|---------|--------|
| `ssh -D 5090 dark -N`                              | Start Socks proxy to dark on :5090 |
| `ssh dark -L 18053:localhost:8053 -N`              | Tunnel from outgoing :18053 to dark's outgoing :8053, and fork to bg |
| `ssh -q -o 'StrictHostKeyChecking no' pinker exit` | Unquestioningly add pinker's host key to `~/.ssh/known_hosts` and then quit |
| `ssh-copy-id -i local.id_rsa.pub dark`             | Copy the key in local.id_rsa.pub to dark's `~/.ssh/authorized_keys` file |
| `ssh-keygen -t rsa`                                | Interactive: create new key (defaults to `~/.ssh/id_rsa{.pub}`) |
| `sudo ~/src/sshuttle/sshuttle -l 22 -r chbrown@dark:8080 0.0.0.0/0 -v` | Tunnel all connections through dark on port 8080 |
| `ssh -t markov 'ssh n004'`                         | Connect to n004 via markov |

### screen

Command Line

|          | flags            | description                               |
|----------|------------------|-------------------------------------------|
| `screen` | `-DR`            | attach to existing or start new screen    |
| `screen` | `-dmS MySession` | start a detached screen session           |
| `screen` | `-r MySession`   | attach screen session with name MySession |


Commands

|          | flags    | description |
|----------|----------|-------------|
| `ctrl a` | `c`      | create new window |
| `ctrl a` | `A`      | set window name |
| `ctrl a` | `w`      | show all window |
|----------|----------|-----------------|
| `ctrl a` | `3`      | switch to window 3 |
| `ctrl a` | `"`      | choose window |
| `ctrl a` | `ctrl a` | switch between window |
|----------|----------|-----------------------|
| `ctrl a` | `d`      | detach window |
| `ctrl a` | `?`      | help |
| `ctrl a` | `[`      | start copy, move cursor to the copy location, press ENTER, select the chars, press ENTER to copy the selected characters to the buffer |
| `ctrl a` | `]`      | paste from buffer |
| `ctrl a` | `S`      | create split screen |
| `ctrl a` | `TAB`    | switch between split screens |
| `ctrl a` | `Q`      | Kill all regions but the current one. |
| `ctrl a` | `X`      | remove active window from split screen |
| `ctrl a` | `O`      | logout active window (disable output) |
| `ctrl a` | `I`      | login active window (enable output) |

## Default locations

Firefox GreaseMonkey:

    cd ~/Library/Application Support/Firefox/Profiles/*/gm_scripts

## Node.js + NPM

### Amazon AMI (Fedora / Red Hat / CentOS flavored)

1. Enable EPEL (Extra Packages for Enterprise Linux)
  - Edit the secondary repos `sudo vim /etc/yum.repos.d/epel.repo`
  - change the `enable=0` line in the first block to `enable=1`

2. Use yum and EPEL to install Node.js via package manager:
  - `sudo yum install -y npm`

On my AMI, it looks something like this:

    [epel]
    name=Extra Packages for Enterprise Linux 6 - $basearch
    #baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
    mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
    failovermethod=priority
    enabled=1
    gpgcheck=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6


### Mac OS X

1. Get [homebrew](http://brew.sh/) ([github repo](https://github.com/mxcl/homebrew)). You should have this already, if you're on a Mac.
  - `ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"`

2. Use homebrew to install Node.js and NPM together:
  - `brew install node`

### Arch Linux

1. Update your Pacman repository information first, just in case:
  - `sudo pacman -Sy`

2. Use pacman to install Node.js via package manager:
  - `sudo pacman -S nodejs`

This installs the `node` binary to `/usr/bin/node`, but it's better than installing from source.

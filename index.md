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

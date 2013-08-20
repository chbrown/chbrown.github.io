---
layout: default
---
## supervisor

`supervisor` refers to the supervisord + supervisorctl + program config file ecosystem.

* `supervisord` is the daemon process that you start manually or in your OS's init sequence.
* `supervisorctl` is the communicating program that sends commands (e.g., update, restart) to the daemon.

Package website (extensively documented): [supervisord.org](http://supervisord.org/)

## Installing

Become root, first:

```bash
sudo su root
```

And then use pip (or easy_install) to install directly from PyPI and set up some basic configuration:

```bash
pip install --upgrade supervisor
mkdir /etc/supervisor.d/
echo_supervisord_conf > /etc/supervisord.conf
```

Or just use the following trimmed down config file:

```ini
[inet_http_server]
port=127.0.0.1:911

[supervisorctl]
serverurl=http://localhost:991

[supervisord]
logfile=/tmp/supervisord.log
logfile_maxbytes=50MB
logfile_backups=10
loglevel=info
pidfile=/tmp/supervisord.pid
nodaemon=false               ; (start in foreground if true;default false)
minfds=1024
minprocs=200

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[include]
files = /etc/supervisor.d/*
```

`/etc/supervisord.conf` is a special location -- it is the default that supervisor will be looking for.
You can put it somewhere else and use `-c` whenever calling `supervisord`, but you'll also have to use `-c`
when calling `supervisorctl`, which is annoying.

* [Config file documentation](http://supervisord.org/configuration.html)

Now edit `/etc/supervisord.conf` and put the following at the bottom:

```
[include]
files = /etc/supervisor.d/*
```

Put the following info a file `/etc/init.d/supervisord`, with 755 permissions:

```bash
#!/bin/sh
# chkconfig: - 64 36
# description: Supervisor Daemon

. /etc/rc.d/init.d/functions

name=supervisord
bin=/usr/bin/supervisord
pid=/tmp/supervisord.pid

start()
{
  echo -n Starting $name:
  daemon $bin -c /etc/supervisord.conf
  [ -f $pid ] && success || failure
  echo
}

stop()
{
  echo -n Stopping $name:
  [ -f $pid ] && killproc $name || success
  echo
}

case "$1" in
  start)
    start
  ;;
  stop)
    stop
  ;;
  status)
    status $name
  ;;
  restart)
    stop
    start
  ;;
  *)
    echo Usage: $0 '{start|stop|status|restart}'
    exit 2
esac
```

And to add that init.d script on a Ubuntu machine, also run:

    sudo chkconfig --level 345 supervisord on

If you just want to kickstart supervisord for now:

    sudo supervisord

## Monitoring:

    sudo supervisorctl

Now you can add "program:" config files in `/etc/supervisor.d/`, call `sudo supervisorctl update`,
and it'll read in changes and add start up your applications as needed.

## Examples

[Program configuration documentation](http://supervisord.org/configuration.html#program-x-section-settings)

* A round-robin'ed Rails app with two instances and node.js frontend: `/etc/supervisor.d/fourfold`

```
[program:fourfold-queue-worker]
user=chbrown
directory=/www/fourfold-app/script
command=ruby redis_queue_worker
autorestart=true

[program:fourfold-rails-8001]
user=chbrown
directory=/www/fourfold-app
command=/www/fourfold-app/bin/thin start -p 8001 -e production

[program:fourfold-rails-8002]
user=chbrown
directory=/www/fourfold-app
command=/www/fourfold-app/bin/thin start -p 8002 -e production

[group:fourfold-rails]
programs=fourfold-queue-worker,fourfold-rails-8001,fourfold-rails-8002


[program:fourfold-node]
user=chbrown
directory=/www/fourfold-app
command=node server.js

[group:fourfold-public]
programs=fourfold-node

```

* Simple one-off node app: `/etc/supervisor.d/classrm.org`

```
[program:classrm.org]
directory=/www/classrm.org
command=node classrm.js
autorestart=true
user=chbrown
```

* Python Pyramid app served via gunicorn: `/etc/supervisor.d/confrm.org`

```
[program:confrm.org]
directory=/www/confrm.org/src
command=/www/confrm.org/src/virtualenv-exec /www/confrm.org gunicorn
    -c gunicorn-prod.py --log-level=DEBUG app-prod
autorestart=true
redirect_stderr=True
user=chbrown
environment=PYTHON_EGG_CACHE=/www/confrm.org/tmp
```

* [twitter-curl](https://github.com/chbrown/twilight) crawler for sarcastic tweets:

```
[program:saidnooneever]
user=chbrown
command=twitter-curl
    --filter "track=saidnooneever,%%F0%%9F%%91%%8F,butiam"
    --file /corpora/twitter/saidnooneever_TIMESTAMP.json
    --accounts /home/chbrown/.twitter
    --timeout 86400
    --interval 900
    --ttv2
```

## Process monitors

* [**Supervisor**](http://supervisord.org/)
    - Lightweight - 3MB of RSS on a machine running about 10 programs under supervisor's supervision.
    - Simple config format; constrained, but sufficient.
    - Sane backoff
    - Annoyingly writes all `stdout` and `stderr` captures as root, chmod'ed to 600

* [Daemontools](http://cr.yp.to/daemontools.html)
    - Becomes too embedded into your system
    - Requires too much file overhead, beyond the application itself
    - Everything being called "run" isn't helpful in `ps`
    - Super old!

* [Monit](http://mmonit.com/monit/)
    - Decent, but takes quite a bit of memory -- 50MB for just a few processes.
    - Steep-ish learning curve. A lot more functionality than I need.

* [God](http://godrb.com/)
    - The horror stories of memory leaks
    - No easy / automatic debounce (or backoff) -- perhaps the cause of those leaks
    - Ruby config -- the DSL is pretty, but falls short quickly if you're not running Rack apps.

* [Bluepill](https://github.com/arya/bluepill)
    - I found this didn't hook in to my Linux instance well. I didn't spend a lot of time with it.


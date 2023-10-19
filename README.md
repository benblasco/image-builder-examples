# image-builder-examples
Example blueprints and instructions for use of Image Builder for creating Enterprise Linux images

# Useful links

Super useful blog from Major Hayden
https://major.io/p/build-custom-centos-stream-cloud-image/

# Install and configure
```

dnf install -y osbuild-composer composer-cli cockpit-composer bash-completion

systemctl enable --now osbuild-composer.socket
systemctl enable --now cockpit.socket

source /etc/bash_completion.d/composer-cli

```

# Add an external repo

```
composer-cli sources add <repo name>.toml
composer-cli sources list
```

# Create a blueprint

```
composer-cli blueprints push <blueprint name>.toml
composer-cli blueprints depsolve <blueprint name>
composer-cli compose start <blueprint name> qcow2
composer-cli compose status
watch -n 5 composer-cli compose status
```

Check the results
```
journalctl -u osbuild-worker@1
journalctl -xe -f -u osbuild-worker@1.service 

```

Download/generate the file
```
composer-cli compose image <UUID>
```

### Logs for tracing

To find the journal output for traceback, run the following commands:
```
$ journalctl | grep osbuild
```

To show both remote or local workers:
```
$ journalctl -u osbuild-worker*
```

To show the running services:
```
$ journalctl -u osbuild-composer.service
```

### How to install and enable EPEL

https://www.redhat.com/sysadmin/install-epel-linux

# Things image builder can do that other methods can't

- Partitioning
- pre-load configuration so that a system is *NEVER* booted with unacceptable configurations (e.g. not yet hardened)
- Enable services at first boot
- Add more repos and install software from those repos
- Brings us closer to immutable infrastructure
